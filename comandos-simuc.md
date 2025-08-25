# Api de Integração Sistema KDL - Comandos SIMUC

### KDL API REST
*versão 0.0.3*

Este endpoint permite executar comandos em unidades SIMUC de forma híbrida: execução direta para comandos únicos ou processamento em batch assíncrono para múltiplos comandos.

## 📡 Endpoints

### 1. Execução de Comandos (Híbrido)
| Método | URI                              | Exemplo                                                                 |
| ------ |----------------------------------|:------------------------------------------------------------------------|
| POST   | `/comandos-simuc/{version}`      | httpss://url/comandos-simuc/v1                                            |

### 2. Consulta Status do Batch
| Método | URI                                      | Exemplo                                                                 |
| ------ |------------------------------------------|:------------------------------------------------------------------------|
| GET    | `/comandos-simuc-batch/{version}/{id}`   | httpss://url/comandos-simuc-batch/v1/batch_20250820_143022_abc123         |

## Autenticação

Para acessar estes endpoints, é necessário incluir o token de autenticação no header da requisição. Consulte o arquivo `autenticacao.md` para saber como obter o token.

Inclua o token no header `Authorization`:

```
Authorization: Bearer <token>
```

## 🔄 Lógica Híbrida

### ✅ **1 Comando = Execução Direta**
- Comportamento síncrono
- Aguarda resposta completa
- Retorna status final
- Status https: 200 OK

### 🚀 **Múltiplos Comandos = Batch Assíncrono**
- Resposta imediata com `batch_id`
- Processamento em segundo plano
- Consulta status via endpoint separado
- Status https: 202 Accepted

## 📝 Exemplos de Uso

### Comando Único (Comportamento Síncrono)

##### Exemplo de uso em Python

```python
import requests

headers = {
    "Authorization": "Bearer <token>",
    "Content-Type": "application/json"
}

# Comando único
comando_data = {
    "simcon": 2522006,
    "comando": 5,
    "target": 2408027754,
    "destino": 0,
    "tipolum": 0,
    "dados": ""
}

response = requests.post(
    url="httpss://url/comandos-simuc/v1",
    headers=headers,
    json=comando_data
)

print(response.status_code)  # 200
print(response.json())
```

> Substitua `<token>` pelo token obtido no processo de autenticação.

##### Parâmetros da URL:
| Identificador | Tipo     | Obrigatório | Descrição                                                    | 
|---------------|----------|:-----------:|:-------------------------------------------------------------| 
| version       | `string` | **Sim**     | Versão da API (ex: v1)                                       |

##### Parâmetros do Body (Comando Único):
| Identificador | Tipo     | Obrigatório | Descrição                                                    | 
|---------------|----------|:-----------:|:-------------------------------------------------------------| 
| simcon        | `int`    | **Sim**     | ID do concentrador SIMCON                                    |
| comando       | `int`    | **Sim**     | Código do comando a ser executado                            |
| target        | `int`    | **Sim**     | ID do setor ou da unidade SIMUC de destino                  |
| destino       | `int`    | **Sim**     | Código de destino (0=SIMUC, 1=SETOR)                        |
| tipolum       | `int`    | **Sim**     | Tipo de luminária (geralmente 49)                           |
| dados         | `string` | Não         | Dados adicionais do comando                                  |

### Exemplo do Resultado (Comando Único):
```json
{
    "data": {
        "success": true,
        "message": "Comando 5 (Acende) - ✅ Comando enviado com sucesso",
        "comando_id": 3
    },
    "success": true,
    "elapsed_time": "2.345s"
}
```

### Múltiplos Comandos (Batch Assíncrono)

##### Exemplo de uso em Python

```python
import requests

headers = {
    "Authorization": "Bearer <token>",
    "Content-Type": "application/json"
}

# Múltiplos comandos
batch_data = {
    "comandos": [
        {
            "simcon": 2522006,
            "comando": 5,
            "target": 2408027754,
            "destino": 0,
            "tipolum": 0,
            "dados": ""
        },
        {
            "simcon": 2522006,
            "comando": 5,
            "target": 2408027755,
            "destino": 0,
            "tipolum": 0,
            "dados": ""
        }
    ]
}

response = requests.post(
    url="httpss://url/comandos-simuc/v1",
    headers=headers,
    json=batch_data
)

print(response.status_code)  # 202
print(response.json())
```

##### Parâmetros do Body (Batch):
| Identificador | Tipo            | Obrigatório | Descrição                                                    | 
|---------------|-----------------|:-----------:|:-------------------------------------------------------------| 
| comandos      | `array<object>` | **Sim**     | Lista de comandos a serem executados                         |

Cada objeto dentro de `comandos` deve conter os mesmos parâmetros do comando único (exceto `usuario`, que é inferido do token).

### Exemplo do Resultado (Batch):
```json
{
    "data": {
        "batch_id": "batch_20250820_143022_abc123",
        "total_comandos": 2,
        "status": "processando"
    },
    "message": "Comandos sendo executados em segundo plano",
    "success": true,
    "elapsed_time": "0.015s"
}
```

## 📊 Consulta Status do Batch

### Endpoint de Consulta

##### Exemplo de uso em Python

```python
import requests

headers = {
    "Authorization": "Bearer <token>"
}

batch_id = "batch_20250820_143022_abc123"
response = requests.get(
    url=f"httpss://url/comandos-simuc-batch/v1/{batch_id}",
    headers=headers
)

print(response.status_code)
print(response.json())
```

##### Parâmetros da URL:
| Identificador | Tipo     | Obrigatório | Descrição                                                    | 
|---------------|----------|:-----------:|:-------------------------------------------------------------| 
| version       | `string` | **Sim**     | Versão da API (ex: v1)                                       |
| id            | `string` | **Sim**     | ID do batch a ser consultado                                 |

### Exemplo do Resultado (Consulta Batch):
```json
{
    "data": {
        "batch_id": "batch_20250820_143022_abc123",
        "total_comandos": 2,
        "status": "concluido",
        "processados": 2,
        "resultados": [
            {
                "target": 2408027754,
                "simcon": 2522006,
                "comando": 5,
                "status": "sucesso",
                "codigo": 3,
                "mensagem": "Comando executado com sucesso",
                "processado_em": "2025-08-20T14:30:25Z"
            },
            {
                "target": 2408027755,
                "simcon": 2522006,
                "comando": 5,
                "status": "erro",
                "erro": "SIMCON offline",
                "processado_em": "2025-08-20T14:30:26Z"
            }
        ],
        "criado_em": "2025-08-20T14:30:22Z",
        "atualizado_em": "2025-08-20T14:30:26Z"
    },
    "success": true,
    "elapsed_time": "0.002s"
}
```

## 📊 Dicionário de Resultados

### Resposta Comando Único
##### Bloco PRINCIPAL:
| Identificador | Tipo     | Descrição                                                           | 
|:------------- |----------|:--------------------------------------------------------------------| 
| data          | `object` | Resultado da execução do comando                                    | 
| success       | `bool`   | Status de sucesso/falha da interação                                | 
| elapsed_time  | `string` | Tempo de duração da execução                                        | 

##### Bloco DATA (Comando Único):
| Identificador | Tipo     | Descrição                                    | 
|:------------- |----------|:---------------------------------------------| 
| success       | `bool`   | Indica se o comando foi executado com sucesso| 
| message       | `string` | Mensagem descritiva do resultado             | 
| comando_id    | `int`    | ID único do comando executado                | 

### Resposta Batch
##### Bloco DATA (Batch):
| Identificador   | Tipo     | Descrição                                    | 
|:--------------- |----------|:---------------------------------------------| 
| batch_id        | `string` | ID único do batch                            | 
| total_comandos  | `int`    | Quantidade total de comandos no batch       | 
| status          | `string` | Status atual do batch                        | 

### Consulta Batch
##### Bloco DATA (Consulta):
| Identificador   | Tipo            | Descrição                                    | 
|:--------------- |-----------------|:---------------------------------------------| 
| batch_id        | `string`        | ID único do batch                            | 
| total_comandos  | `int`           | Quantidade total de comandos no batch       | 
| status          | `string`        | Status atual do batch                        | 
| processados     | `int`           | Quantidade de comandos já processados        | 
| resultados      | `array<object>` | Lista com resultados de cada comando        | 
| criado_em       | `string`        | Timestamp de criação do batch                | 
| atualizado_em   | `string`        | Timestamp da última atualização              | 

##### Bloco RESULTADOS (Itens):
| Identificador   | Tipo     | Descrição                                    | 
|:--------------- |----------|:---------------------------------------------| 
| target          | `int`    | ID da unidade SIMUC de destino              | 
| simcon          | `int`    | ID do concentrador SIMCON                   | 
| comando         | `int`    | Código do comando executado                  | 
| status          | `string` | Status da execução (sucesso/erro)           | 
| codigo          | `int`    | Código de retorno (quando sucesso)          | 
| mensagem        | `string` | Mensagem de sucesso (quando sucesso)        | 
| erro            | `string` | Mensagem de erro (quando erro)              | 
| processado_em   | `string` | Timestamp do processamento                   | 

## 📊 Status e Códigos

### Status de Batch
| Status                   | Descrição                                    |
|:-------------------------|:---------------------------------------------|
| `processando`            | Comandos sendo executados                    |
| `concluido`              | Todos os comandos processados com sucesso    |
| `concluido_com_erros`    | Alguns comandos falharam                     |

### Status de Comando Individual
| Status    | Descrição                                    |
|:----------|:---------------------------------------------|
| `sucesso` | Comando executado com sucesso                |
| `erro`    | Falha na execução do comando                 |

### Códigos de Comando
| Código | Descrição                                    |
|:-------|:---------------------------------------------|
| 5      | Acende                                       |
| 6      | Apaga                                        |
| 7      | Pisca                                        |
| 8      | Dimmer                                       |

### Códigos de Resposta/Alertas
| Código | Descrição                                                                                         |
|:-------|:--------------------------------------------------------------------------------------------------|
| 1      | ⏳ Aguardando resposta do SIMUC                                                                   |
| 2      | 🔄 Ponto comunicando, porém em processo de atualização                                           |
| 3      | ✅ Comando enviado com sucesso                                                                    |
| 4      | ❌ Falha ao enviar o comando                                                                      |
| 5      | 📡 SIMUC não responde ao comando enviado                                                         |
| 6      | 🔌 Houve perda de conexão entre o servidor e o SIMCON, então o envio do comando foi cancelado   |
| 7      | ⚠️ Há um parâmetro inválido no comando fornecido, então o envio do comando foi cancelado        |
| 8      | 🔄 Um novo comando foi fornecido antes que o anterior fosse concluído, então ele foi cancelado  |
| 9      | ✅ O comando destinado a um setor já foi enviado e concluído com sucesso                         |
| 10     | ✅ Comando enviado com sucesso                                                                    |
| 11     | 🔄 Atualizações do SIMUC concluídas                                                              |
| 12     | 📊 Dados estatísticos recebidos do SIMUC                                                         |
| 13     | 🔄 O SIMUC respondeu ao comando, mas está em processo de reinicialização                         |
| 14     | ❌ Número de SIMCON inexistente                                                                   |
| 15     | ❌ Número de SIMUC inexistente                                                                    |
| 16     | ⚙️ Configurações do SIMUC atualizadas                                                            |
| 17     | ✅ Comando enviado com sucesso                                                                    |
| 18     | ❌ Falha ao enviar o comando                                                                      |

## ⚙️ Configurações e Limitações

### Cache de Batches
- Armazenado em memória do servidor
- Limpeza automática de batches com mais de 24h
- Scheduler executa a cada 1 hora

### Processamento
- Comandos processados sequencialmente
- Pausa de 500ms entre comandos
- Logs detalhados para cada comando

### Validações
- Mesmas validações do comando único aplicadas
- Validação em lote antes do processamento
- Falha rápida em caso de erro de validação

### Rate Limiting
- Mesmos limites aplicados a ambos os endpoints
- Contabilização por usuário/token

## 🛡️ Segurança

- JWT obrigatório para ambos os endpoints
- Middlewares de segurança aplicados
- Audit logs mantidos para todos os comandos
- Validação de permissões por usuário

## ❌ Códigos de Erro

### Erros https Comuns
| Código | Descrição                                    |
|:-------|:---------------------------------------------|
| 400    | Parâmetros inválidos ou malformados         |
| 401    | Token de autenticação inválido ou expirado  |
| 403    | Usuário sem permissão para o comando        |
| 404    | Batch ID não encontrado (consulta)          |
| 422    | Erro de validação dos dados                  |
| 500    | Erro interno do servidor                     |

### Exemplos de Erro
```json
{
    "error": "Parâmetros inválidos",
    "message": "Campo 'comando' é obrigatório",
    "success": false,
    "elapsed_time": "0.001s"
}
```

## 📈 Vantagens da Implementação

1. **Compatibilidade**: Código existente continua funcionando
2. **Performance**: Múltiplos comandos sem bloquear interface
3. **Monitoramento**: Status detalhado de cada comando
4. **Escalabilidade**: Processamento assíncrono
5. **Logs**: Cada comando mantém log individual

## 🔧 Manutenção

### Limpeza Automática
O sistema executa limpeza automática de batches antigos:
- **Frequência**: A cada 1 hora
- **Critério**: Batches com mais de 24 horas
- **Logs**: Registra quantos batches foram removidos

### Monitoramento
- Logs detalhados em cada etapa
- Status de progresso em tempo real
- Histórico completo no cache até a limpeza

## Nota Importante

- O envio do token de acesso é obrigatório para ambos os endpoints
- Para comando único, envie os parâmetros diretamente no body
- Para múltiplos comandos, envie um array `comandos` no body
- O sistema detecta automaticamente o tipo de requisição
- Batches são processados em segundo plano e podem ser consultados a qualquer momento
- IDs de batch seguem o padrão: `batch_YYYYMMDD_HHMMSS_XXXXX`
