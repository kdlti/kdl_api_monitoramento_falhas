# Api de Integração Sistema KDL - Substituir SIMUC

### KDL API REST
*versão 0.0.3*

Este endpoint substitui um SIMUC antigo por um novo, preservando a posição (latitude/longitude), o tipo/setor e a etiqueta cadastrada. Internamente ele exclui o SIMUC antigo e inclui o novo reaproveitando os dados do antigo, usando o mesmo protocolo assíncrono de comando (fila `atualizacao`) já usado pelos endpoints `incluir-simuc`/`deletar-simuc`/`editar-simuc`.

| Método | URI                              | Exemplo                                                                 |
| ------ |----------------------------------|:------------------------------------------------------------------------|
| POST   | `/substituir-simuc/{version}`    | https://url/substituir-simuc/v6                                          |

## Autenticação

Para acessar este endpoint, é necessário incluir o token de autenticação no header da requisição. Consulte o arquivo `autenticacao.md` para saber como obter o token.

Inclua o token no header `Authorization`:

```
Authorization: Bearer <token>
```

## 🔄 Funcionalidade de Substituição

### ✅ **Comportamento Adaptável**
- O **SIMCON não é enviado no corpo da requisição** — é descoberto automaticamente a partir do cadastro atual do SIMUC antigo
- Posição, tipo, setor e etiqueta do SIMUC antigo são **reaproveitados automaticamente** pelo SIMUC novo
- Se `nserlumNovo` já pertencer a outro ponto **inativo/offline**, esse ponto é realocado automaticamente

### 🚀 **Lógica Inteligente**
- Descoberta automática do SIMCON
- Verificação de conflito: se `nserlumNovo` já existir e estiver ativo/comunicando no local atual, a substituição é abortada sem alterar nada
- Se o ponto conflitante estiver inativo, é realocado para um número fictício reservado (faixa `1000000001`–`1299999998`), preservando posição e etiqueta
- Exclusão do SIMUC antigo e inclusão do SIMUC novo são executadas via fila `atualizacao`, com 1 retry automático (após 4s) na exclusão
- Transferência da etiqueta capturada do SIMUC antigo para o SIMUC novo é executada **apenas após sucesso** da exclusão e inclusão

## 📝 Exemplos de Uso

### Substituição de SIMUC

##### Exemplo de uso em Python

```python
import requests

headers = {
    "Authorization": "Bearer <token>",
    "Content-Type": "application/json"
}

# Substituição de SIMUC antigo por um novo
substituicao_data = {
    "nserlumAntigo": "1234567890",
    "nserlumNovo": "9876543210"
}

response = requests.post(
    url="https://url/substituir-simuc/v6",
    headers=headers,
    json=substituicao_data
)

print(response.status_code)  # 200
print(response.json())
```

> Substitua `<token>` pelo token obtido no processo de autenticação.

##### Exemplo de uso em cURL

```bash
curl -X POST \
  'https://url/substituir-simuc/v6' \
  -H 'Authorization: Bearer <token>' \
  -H 'Content-Type: application/json' \
  -d '{
    "nserlumAntigo": "1234567890",
    "nserlumNovo": "9876543210"
  }'
```

##### Exemplo de uso em JavaScript

```javascript
const response = await fetch('https://url/substituir-simuc/v6', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer ' + token,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    nserlumAntigo: '1234567890',
    nserlumNovo: '9876543210'
  })
});

const result = await response.json();
console.log(result.data.message);
```

##### Parâmetros da URL:
| Identificador | Tipo     | Obrigatório | Descrição                                                    |
|---------------|----------|:-----------:|:-------------------------------------------------------------|
| version       | `string` | **Sim**     | Versão da API (ex: v5, v6)                                    |

##### Parâmetros do Body:
| Identificador   | Tipo     | Obrigatório | Descrição                                                    |
|-----------------|----------|:-----------:|:-------------------------------------------------------------|
| nserlumAntigo   | `string` | **Sim**     | SIMUC que será removido (10 dígitos numéricos)                |
| nserlumNovo     | `string` | **Sim**     | SIMUC que será cadastrado no lugar (10 dígitos numéricos)      |

> Observação: o SIMCON não é enviado no corpo da requisição. Ele é descoberto automaticamente a partir do cadastro atual do SIMUC antigo. Isso evita divergência entre o SIMCON informado manualmente e o SIMCON ao qual o SIMUC antigo realmente está associado.

### Exemplo do Resultado (Sucesso simples):
```json
{
    "data": {
        "success": true,
        "status": "sucesso",
        "message": "SIMUC substituído com sucesso",
        "codigo": 2,
        "simcon": 1234567,
        "nserlum_antigo": 1234567890,
        "nserlum_novo": 9876543210,
        "etiqueta_transferida": true,
        "etiqueta": "R.PRINCIPAL,123",
        "processed_at": "2026-07-06T10:30:00Z"
    },
    "success": true,
    "elapsed_time": "3.4s"
}
```

### Exemplo do Resultado (Sucesso com conflito resolvido):

Quando `nserlumNovo` já pertence a outro ponto cadastrado (mas esse ponto está inativo/offline naquele local), o ponto conflitante é automaticamente realocado para um número fictício reservado (faixa `1000000001`–`1299999998`), preservando sua posição e etiqueta para reaproveitamento futuro. A mensagem final já informa a origem (SIMCON/IP) e o número fictício gerado:

```json
{
    "data": {
        "success": true,
        "status": "sucesso",
        "message": "SIMUC substituído com sucesso. O SIMUC novo (9876543210) já estava cadastrado no SIMCON 7654321 (IP0001) e foi realocado automaticamente para o número fictício 1000000042",
        "codigo": 2,
        "simcon": 1234567,
        "nserlum_antigo": 1234567890,
        "nserlum_novo": 9876543210,
        "nserlum_ficticio": 1000000042,
        "etiqueta_transferida": true,
        "etiqueta": "IP0001xx",
        "processed_at": "2026-07-06T10:30:00Z"
    },
    "success": true,
    "elapsed_time": "5.1s"
}
```

## 📊 Dicionário de Resultados

### Resposta de Substituição
##### Bloco PRINCIPAL:
| Identificador | Tipo     | Descrição                                                           |
|:------------- |----------|:--------------------------------------------------------------------|
| data          | `object` | Resultado da substituição do SIMUC                                   |
| success       | `bool`   | Status de sucesso/falha da interação                                 |
| elapsed_time  | `string` | Tempo de duração da execução                                         |

##### Bloco DATA:
| Identificador          | Tipo     | Descrição                                                    |
|:-----------------------|----------|:---------------------------------------------------------------|
| success                | `bool`   | Indica se o SIMUC foi substituído com sucesso                 |
| status                 | `string` | Status da operação (sucesso/erro)                             |
| message                | `string` | Mensagem descritiva do resultado                              |
| codigo                 | `int`    | Código de resposta da operação                                |
| simcon                 | `int`    | ID do concentrador SIMCON descoberto a partir do SIMUC antigo |
| nserlum_antigo         | `int`    | Número serial do SIMUC removido                               |
| nserlum_novo           | `int`    | Número serial do SIMUC cadastrado no lugar                    |
| nserlum_ficticio       | `int`    | Número fictício gerado para o ponto conflitante (opcional)    |
| etiqueta_transferida   | `bool`   | Indica se a etiqueta do SIMUC antigo foi transferida          |
| etiqueta               | `string` | Etiqueta transferida para o SIMUC novo (opcional)             |
| processed_at           | `string` | Timestamp do processamento                                    |

## 🎯 Descoberta do SIMCON e Resolução de Conflitos

- O SIMCON é sempre descoberto a partir do cadastro do SIMUC antigo, nunca informado manualmente
- Se `nserlumNovo` já existir e estiver ativo/comunicando no local atual, a substituição é recusada sem alterar nada
- Se `nserlumNovo` já existir mas estiver inativo/offline, o ponto conflitante é realocado para um número fictício (faixa `1000000001`–`1299999998`), preservando posição e etiqueta
- O campo `nserlum_ficticio` aparece apenas quando ocorreu realocação de um ponto conflitante
- O campo `etiqueta` e `etiqueta_transferida` refletem a etiqueta do SIMUC antigo, transferida para o SIMUC novo

## 🔄 Processo Interno

1. **Validação**: confere formato de `nserlumAntigo`/`nserlumNovo` (10 dígitos, diferentes entre si)
2. **Descoberta do SIMCON**: busca posição, tipo, setor e SIMCON do SIMUC antigo
3. **Verificação do SIMCON**: confirma que o SIMCON descoberto está online
4. **Captura da etiqueta**: lê etiqueta/observações/sub do SIMUC antigo, antes de qualquer alteração
5. **Verificação de conflito**: se `nserlumNovo` já existe, verifica se está ativo no local atual; se não estiver, gera um número fictício e realoca esse ponto (preservando posição e etiqueta) para liberar o número
6. **Reset de status** do SIMUC antigo
7. **Exclusão do SIMUC antigo** via fila (com 1 retry automático após 4s caso a primeira tentativa falhe ou expire)
8. **Encerramento do registro antigo** 
9. **Inclusão do SIMUC novo** reaproveitando posição/tipo/setor do antigo
10. **Transferência da etiqueta** capturada no passo 4 para o novo SIMUC
11. **Log e resposta**: grava o registro em CSV e retorna o resultado

## 📊 Códigos de Resposta

### Códigos de Sucesso
| Código | Descrição                                          |
|:-------|:---------------------------------------------------|
| 2      | ✅ SIMUC substituído com sucesso                    |

### Status de Operação
| Status    | Descrição                                    |
|:----------|:---------------------------------------------|
| `sucesso` | Operação executada com sucesso               |
| `erro`    | Falha na execução da operação                |

## ⚙️ Validações Específicas para SUBSTITUIÇÃO
- `nserlumAntigo` e `nserlumNovo` devem ter exatamente 10 dígitos numéricos
- `nserlumAntigo` e `nserlumNovo` não podem ser iguais
- O SIMUC antigo precisa existir
- O SIMCON descoberto a partir do SIMUC antigo precisa estar online
- Se `nserlumNovo` já pertencer a um ponto ativo no local atual, a substituição é recusada (não há realocação automática de pontos em uso)

## 🛡️ Segurança
- JWT obrigatório para o endpoint
- Conexão ao banco resolvida pelo tenant do usuário autenticado (`bank_index` do token)
- Todas as consultas usam parâmetros preparados (`database/sql`), sem concatenação de SQL
- Audit logs detalhados mantidos para todas as substituições

## ❌ Códigos de Erro
### Erros HTTP Comuns
| Código | Descrição                                                    |
|:-------|:---------------------------------------------------------------|
| 400    | Parâmetros inválidos (formato, iguais, etc.)                  |
| 401    | Token ausente ou inválido                                     |
| 404    | SIMUC/SIMCON não encontrado ou não pertence ao usuário        |
| 408    | Timeout aguardando resposta do sistema                        |
| 409    | SIMUC novo já cadastrado e em funcionamento no local atual     |
| 500    | Demais falhas internas                                         |
| 503    | SIMCON offline / sem disponibilidade de envio de comando       |

### Exemplos de Erro
```json
{
    "error": "Parâmetros inválidos",
    "message": "Campo 'nserlumNovo' é obrigatório",
    "success": false,
    "elapsed_time": "0.001s"
}
```

```json
{
    "data": {
        "success": false,
        "status": "erro",
        "message": "SIMUC novo já está cadastrado e em funcionamento no local atual",
        "nserlum_antigo": 1234567890,
        "nserlum_novo": 9876543210,
        "processed_at": "2026-07-06T10:30:00Z"
    },
    "success": false,
    "elapsed_time": "0.8s",
    "error": "SIMUC novo já está cadastrado e em funcionamento no local atual"
}
```

```json
{
    "data": {
        "success": false,
        "status": "erro",
        "message": "SIMCON offline - aguarde o retorno e tente novamente",
        "simcon": 1234567,
        "nserlum_antigo": 1234567890,
        "nserlum_novo": 9876543210,
        "processed_at": "2026-07-06T10:30:00Z"
    },
    "success": false,
    "elapsed_time": "0.3s",
    "error": "ERRO: o SIMCON 1234567 está OFFLINE no momento"
}
```

## 📁 Arquivos de Log

Os logs são salvos em `logs/substituicao_simuc_YYYY-MM-DD.csv` com as colunas:
- Data
- Usuario
- SIMCON
- SIMUC_ANTIGO
- SIMUC_NOVO
- Status

## ⚠️ Limitações
- O SIMCON do SIMUC antigo deve estar online para a substituição ser executada
- Sistema deve ter disponibilidade de envio de comandos
- Timeout de até ~30 segundos por operação de comando (exclusão e inclusão), com 1 retry automático na exclusão
- Se o número novo já pertencer a um ponto ativo no local atual, a substituição é recusada (não há realocação automática de pontos em uso)

## 📝 Boas Práticas
1. Confirme `nserlumAntigo` e `nserlumNovo` (10 dígitos, distintos) antes de enviar a requisição
2. Não envie o SIMCON manualmente — ele é sempre descoberto a partir do SIMUC antigo
3. Monitore `etiqueta_transferida` e `nserlum_ficticio` para confirmar o resultado completo da operação
4. Consulte os logs em `logs/substituicao_simuc_YYYY-MM-DD.csv` para auditoria da operação
5. Utilize credenciais com permissão adequada

## Nota Importante
- A substituição exclui o SIMUC antigo e inclui o SIMUC novo reaproveitando posição, tipo, setor e etiqueta
- Em caso de conflito com ponto inativo, a realocação para número fictício é automática; em caso de ponto ativo, a operação é abortada sem alterar nada
- Use este endpoint com cautela; a exclusão do SIMUC antigo faz parte do processo e não é revertida automaticamente em caso de falha na etapa de inclusão do novo
