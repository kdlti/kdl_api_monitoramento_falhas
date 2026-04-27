# Api de Integração Sistema KDL - Deletar SIMUC

### KDL API REST
*versão 0.0.3*

Este endpoint permite remover (deletar) uma unidade SIMUC do sistema. A funcionalidade de etiquetas também é tratada no fluxo de exclusão: a etiqueta associada pode ser removida ou sinalizada conforme a operação.

| Método | URI                              | Exemplo                                                                 |
| ------ |----------------------------------|:------------------------------------------------------------------------|
| DELETE | `/deletar-simuc/{version}`       | https://url/deletar-simuc/v1                                             |

## Autenticação

Para acessar este endpoint, é necessário incluir o token de autenticação no header da requisição. Consulte o arquivo `autenticacao.md` para saber como obter o token.

Inclua o token no header `Authorization`:

```
Authorization: Bearer <token>
```

## 🏷️ Tratamento de Etiquetas (no fluxo de exclusão)

### ✅ **Comportamento Adaptável**
- **SEM campos opcionais** = Exclusão normal (apenas remoção do SIMUC)
- **COM campos opcionais** = Exclusão + processamento de etiqueta (remoção/ajuste)

### 🚀 **Lógica Inteligente**
- A tentativa de exclusão do SIMUC é executada primeiro
- Se campos relativos à etiqueta forem enviados, o processamento da etiqueta ocorre após sucesso da exclusão
- Remove ou marca a etiqueta como inativa quando aplicável
- Em caso de falha no processamento da etiqueta, a exclusão do SIMUC permanece efetivada

## 📝 Exemplos de Uso

### Exclusão COM processamento de Etiqueta

##### Exemplo de uso em Python

```python
import requests

headers = {
    "Authorization": "Bearer <token>",
    "Content-Type": "application/json"
}

# Requisição de exclusão com instrução de remoção de etiqueta
payload = {
    "simcon": 2525025,
    "nserlum": 2000000001,
    "etiqueta": "IPXX101",
    "sub": "LAPA",
    "obs": "Remover por substituição"
}

response = requests.delete(
    url="https://url/deletar-simuc/v1",
    headers=headers,
    json=payload
)

print(response.status_code)  # 200
print(response.json())
```

> Substitua `<token>` pelo token obtido no processo de autenticação.

##### Parâmetros da URL:
| Identificador | Tipo     | Obrigatório | Descrição                                                    |
|---------------|----------|:-----------:|:-------------------------------------------------------------|
| version       | `string` | **Sim**     | Versão da API (ex: v1)                                       |

##### Parâmetros do Body (Exclusão COM/SEM Etiqueta):
| Identificador | Tipo     | Obrigatório | Descrição                                                    |
|---------------|----------|:-----------:|:-------------------------------------------------------------|
| simcon        | `int`    | **Sim**     | ID do concentrador SIMCON — identifica o concentrador alvo    |
| nserlum       | `int`    | **Sim**     | Número serial da luminária/SIMUC (identificador do SIMUC)     |
| etiqueta      | `string` | Não         | Etiqueta associada (opcional - instrução para remoção)       |
| sub           | `string` | Não         | Subprefeitura ou região (opcional)                           |
| obs           | `string` | Não         | Observações para auditoria (opcional)                        |

> Observação: `simcon` + `nserlum` identificam exclusivamente o registro a ser removido.

### Exemplo do Resultado (COM processamento de etiqueta):
```json
{
    "data": {
        "success": true,
        "status": "sucesso",
        "message": "✅ SIMUC deletado com sucesso",
        "codigo": 2,
        "simcon": 2525025,
        "nserlum": 2000000001,
        "processed_at": "2026-04-27T15:17:38-03:00",
        "etiqueta_status": "Etiqueta deletada com sucesso"
    },
    "success": true,
    "elapsed_time": "2.56s"
}
```

### Exemplo do Resultado (SEM processamento de etiqueta):
```json
{
    "data": {
        "success": true,
        "status": "sucesso",
        "message": "✅ SIMUC deletado com sucesso",
        "codigo": 2,
        "simcon": 2525025,
        "nserlum": 2000000001,
        "processed_at": "2026-04-27T15:17:38-03:00"
    },
    "success": true,
    "elapsed_time": "1.1s"
}
```

## 📊 Dicionário de Resultados

### Resposta de Exclusão
##### Bloco PRINCIPAL:
| Identificador | Tipo     | Descrição                                                           |
|:------------- |----------|:--------------------------------------------------------------------|
| data          | `object` | Resultado da exclusão do SIMUC                                       |
| success       | `bool`   | Status de sucesso/falha da interação                                 |
| elapsed_time  | `string` | Tempo de duração da execução                                         |

##### Bloco DATA:
| Identificador     | Tipo     | Descrição                                          |
|:------------------|----------|:---------------------------------------------------|
| success           | `bool`   | Indica se o SIMUC foi deletado com sucesso         |
| status            | `string` | Status da operação (sucesso/erro)                  |
| message           | `string` | Mensagem descritiva do resultado                   |
| codigo            | `int`    | Código de resposta da operação                     |
| simcon            | `int`    | ID do concentrador SIMCON                          |
| nserlum           | `int`    | Número serial da luminária deletada                |
| processed_at      | `string` | Timestamp do processamento                         |
| etiqueta_status   | `string` | Status do processamento de etiqueta (opcional)     |

## 🎯 Lógica de Etiquetas na Exclusão

- Se nenhum campo de etiqueta for enviado: somente o SIMUC é removido
- Se houver instrução de etiqueta: após sucesso da exclusão, a etiqueta é removida ou marcada conforme política
- O campo `etiqueta_status` aparece quando ocorreu processamento de etiqueta
- Em caso de conflito (etiqueta vinculada a outro registro) a resposta traz mensagem específica

## 📊 Códigos de Resposta

### Códigos de Sucesso
| Código | Descrição                                          |
|:-------|:---------------------------------------------------|
| 2      | ✅ SIMUC deletado com sucesso                      |
| 3      | ✅ SIMUC deletado com sucesso (etiqueta processada)|

### Status de Operação
| Status    | Descrição                                    |
|:----------|:---------------------------------------------|
| `sucesso` | Operação executada com sucesso               |
| `erro`    | Falha na execução da operação                |

## ⚙️ Validações Específicas para EXCLUSÃO
- `simcon` e `nserlum` devem existir no sistema para permitir a exclusão
- Verificação de vínculos (ex.: ordens, histórico) pode impedir exclusão
- Em caso de exclusão física ou lógica, o sistema pode aplicar regras internas
- Se `nserlum` for inválido ou já removido, retorna 404

## 🛡️ Segurança
- JWT obrigatório para o endpoint
- Validação de permissões por usuário (permissão de exclusão)
- Audit logs mantidos para todas as exclusões
- Sanitização de dados de entrada

## ❌ Códigos de Erro
### Erros HTTP Comuns
| Código | Descrição                                    |
|:-------|:---------------------------------------------|
| 400    | Parâmetros inválidos ou malformados         |
| 401    | Token de autenticação inválido ou expirado  |
| 403    | Usuário sem permissão para exclusão         |
| 404    | SIMUC não encontrado para exclusão          |
| 409    | Conflito (vínculos que impedem exclusão)    |
| 422    | Erro de validação dos dados                  |
| 500    | Erro interno do servidor                     |

### Exemplos de Erro
```json
{
    "error": "Parâmetros inválidos",
    "message": "Campo 'nserlum' é obrigatório",
    "success": false,
    "elapsed_time": "0.001s"
}
```

```json
{
    "data": {
        "success": false,
        "status": "erro",
        "message": "SIMUC não encontrado",
        "codigo": 14,
        "simcon": 9999999,
        "processed_at": "2026-04-27T15:17:38-03:00"
    },
    "success": false,
    "elapsed_time": "1.2s"
}
```

## 📝 Boas Práticas
1. Confirme o identificador (`simcon` + `nserlum`) antes de remover
2. Se desejar remover etiqueta, envie o campo `etiqueta` para garantir processamento
3. Mantenha logs de auditoria e motivo em `obs` quando pertinente
4. Utilize credenciais com permissão adequada

## Nota Importante
- A exclusão do SIMUC é a operação primária; qualquer processamento de etiqueta ocorre depois
- Em caso de falha no processamento de etiqueta, a exclusão do SIMUC NÃO é revertida automaticamente
- Utilize este endpoint com cautela; operações de exclusão podem afetar histórico e relacionamentos no sistema
