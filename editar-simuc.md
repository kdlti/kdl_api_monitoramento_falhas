# Api de Integração Sistema KDL - Editar SIMUC

### KDL API REST
*versão 0.0.3*

Este endpoint permite editar uma unidade SIMUC existente no sistema. A funcionalidade de etiquetas também está disponível durante a edição: pode atualizar ou cadastrar etiqueta associada após o sucesso da edição do SIMUC.

| Método | URI                              | Exemplo                                                                 |
| ------ |----------------------------------|:------------------------------------------------------------------------|
| PUT    | `/editar-simuc/{version}`        | https://url/editar-simuc/v1                                              |

## Autenticação

Para acessar este endpoint, é necessário incluir o token de autenticação no header da requisição. Consulte o arquivo `autenticacao.md` para saber como obter o token.

Inclua o token no header `Authorization`:

```
Authorization: Bearer <token>
```

## 🏷️ Funcionalidade de Etiquetas (no fluxo de edição)

### ✅ **Comportamento Adaptável**
- **SEM campos opcionais** = Edição normal (apenas atualização do SIMUC)
- **COM campos opcionais** = Edição + processamento/atualização de etiqueta

### 🚀 **Lógica Inteligente**
- Edição do SIMUC é sempre executada primeiro
- Se campos de etiqueta forem fornecidos, o processamento de etiqueta só ocorre após sucesso da edição
- Atualiza etiqueta existente quando aplicável, ou cadastra nova etiqueta
- Em caso de falha no processamento da etiqueta, a edição do SIMUC permanece aplicada

## 📝 Exemplos de Uso

### Edição COM Etiqueta (atualiza SIMUC e processa etiqueta)

##### Exemplo de uso em Python

```python
import requests

headers = {
    "Authorization": "Bearer <token>",
    "Content-Type": "application/json"
}

# Edição com atualização/cadastro de etiqueta
simuc_data = {
    "simcon": 2525025,
    "nserlum": 2000000001,
    "latitude": -23.563593,
    "longitude": -46.654527,
    "tipolum": 49,
    "nsetor": 1,
    "etiqueta": "IPXX101",
    "sub": "LAPA",
    "obs": "TESTE"
}

response = requests.put(
    url="https://url/editar-simuc/v1",
    headers=headers,
    json=simuc_data
)

print(response.status_code)  # 200
print(response.json())
```

> Substitua `<token>` pelo token obtido no processo de autenticação.

##### Parâmetros da URL:
| Identificador | Tipo     | Obrigatório | Descrição                                                    |
|---------------|----------|:-----------:|:-------------------------------------------------------------|
| version       | `string` | **Sim**     | Versão da API (ex: v1)                                       |

##### Parâmetros do Body (Edição COM/SEM Etiqueta):
| Identificador | Tipo     | Obrigatório | Descrição                                                    |
|---------------|----------|:-----------:|:-------------------------------------------------------------|
| simcon        | `int`    | **Sim**     | ID do concentrador SIMCON — identifica o concentrador alvo    |
| nserlum       | `int`    | **Sim**     | Número serial da luminária/SIMUC (identificador do SIMUC)     |
| latitude      | `float`  | Não         | Latitude da localização (atualiza se informado)              |
| longitude     | `float`  | Não         | Longitude da localização (atualiza se informado)             |
| tipolum       | `int`    | Não         | Tipo de luminária (atualiza se informado)                   |
| nsetor        | `int`    | Não         | Número do setor (atualiza se informado)                      |
| etiqueta      | `string` | Não         | Etiqueta identificadora (atualiza/cadastra etiqueta)         |
| sub           | `string` | Não         | Subprefeitura ou região (atributo de etiqueta)              |
| obs           | `string` | Não         | Observações adicionais                                       |

> Observação: para edição, `simcon` + `nserlum` identificam o registro alvo; demais campos são opcionais e atualizam os valores existentes.

### Exemplo do Resultado (COM Etiqueta):
```json
{
    "data": {
        "success": true,
        "status": "sucesso",
        "message": "✅ SIMUC editado com sucesso",
        "codigo": 5,
        "simcon": 2525025,
        "nserlum": 2000000001,
        "processed_at": "2026-04-27T15:17:09-03:00",
        "etiqueta_status": "Etiqueta editada com sucesso"
    },
    "success": true,
    "elapsed_time": "1.5s"
}
```

### Exemplo do Resultado (SEM Etiqueta):
```json
{
    "data": {
        "success": true,
        "status": "sucesso",
        "message": "✅ SIMUC editado com sucesso",
        "codigo": 5,
        "simcon": 2525025,
        "nserlum": 2000000001,
        "processed_at": "2026-04-27T15:17:09-03:00"
    },
    "success": true,
    "elapsed_time": "0.9s"
}
```

## 📊 Dicionário de Resultados

### Resposta de Edição
##### Bloco PRINCIPAL:
| Identificador | Tipo     | Descrição                                                           |
|:------------- |----------|:--------------------------------------------------------------------|
| data          | `object` | Resultado da edição do SIMUC                                         |
| success       | `bool`   | Status de sucesso/falha da interação                                 |
| elapsed_time  | `string` | Tempo de duração da execução                                         |

##### Bloco DATA:
| Identificador     | Tipo     | Descrição                                          |
|:------------------|----------|:---------------------------------------------------|
| success           | `bool`   | Indica se o SIMUC foi editado com sucesso          |
| status            | `string` | Status da operação (sucesso/erro)                  |
| message           | `string` | Mensagem descritiva do resultado                   |
| codigo            | `int`    | Código de resposta da operação                     |
| simcon            | `int`    | ID do concentrador SIMCON                          |
| nserlum           | `int`    | Número serial da luminária editada                 |
| processed_at      | `string` | Timestamp do processamento                         |
| etiqueta_status   | `string` | Status do processamento de etiqueta (opcional)     |

## 🎯 Lógica de Etiquetas na Edição

- Se nenhum campo de etiqueta for enviado: somente o SIMUC é atualizado
- Se houver campos de etiqueta: após sucesso da edição, a etiqueta é criada ou atualizada
- O campo `etiqueta_status` aparece quando ocorreu processamento de etiqueta
- Em caso de conflito (etiqueta já existe) a resposta traz mensagem específica

## 📊 Códigos de Resposta

### Códigos de Sucesso
| Código | Descrição                                          |
|:-------|:---------------------------------------------------|
| 5      | ✅ SIMUC editado com sucesso                       |
| 6      | ✅ SIMUC editado com sucesso (etiqueta processada) |

### Status de Operação
| Status    | Descrição                                    |
|:----------|:---------------------------------------------|
| `sucesso` | Operação executada com sucesso               |
| `erro`    | Falha na execução da operação                |

## ⚙️ Validações Específicas para EDIÇÃO
- `simcon` e `nserlum` devem existir no sistema para permitir a edição
- Campos fornecidos são validados (formato, alcance e consistência)
- Coordenadas devem estar no formato válido
- Em caso de alteração de `nserlum`, verificar duplicidade na base

## 🛡️ Segurança
- JWT obrigatório para o endpoint
- Validação de permissões por usuário (permissão de edição)
- Audit logs mantidos para todas as edições
- Sanitização de dados de entrada

## ❌ Códigos de Erro
### Erros HTTP Comuns
| Código | Descrição                                    |
|:-------|:---------------------------------------------|
| 400    | Parâmetros inválidos ou malformados         |
| 401    | Token de autenticação inválido ou expirado  |
| 403    | Usuário sem permissão para edição           |
| 404    | SIMUC não encontrado para edição            |
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
        "processed_at": "2026-04-27T15:17:09-03:00"
    },
    "success": false,
    "elapsed_time": "1.2s"
}
```

## 📝 Boas Práticas
1. Fornecer apenas os campos que devem ser atualizados
2. Se for fornecer etiqueta, confirme o formato para evitar conflitos
3. Utilize o mesmo token de integração com permissões adequadas
4. Monitore `etiqueta_status` para confirmar o resultado do processamento

## Nota Importante
- A edição do SIMUC é aplicada antes do processamento de etiquetas
- Em caso de falha no processamento de etiqueta, o SIMUC permanece editado
- Use este endpoint para atualizar dados do SIMUC com ou sem alteração de etiqueta
