# Api de Integração Sistema KDL - Histórico SIMUC

### KDL API REST
*versão 0.0.3*

Este endpoint permite consultar o histórico de peças SIMUC no sistema, retornando em JSON as leituras elétricas e dados de ambiente do período informado.

| Método | URI                               | Exemplo                                  |
| ------ |-----------------------------------|:-----------------------------------------|
| POST   | `/historico-simuc/{version}`      | https://url/historico-simuc/v1           |

## Autenticação

Para acessar este endpoint, é necessário incluir o token de autenticação no header da requisição. Consulte o arquivo `autenticacao.md` para saber como obter o token.

Inclua o token no header `Authorization`:

```
Authorization: Bearer <token>
```

## Consulta de Histórico

### Comportamento da Consulta
- `dataInicial` e `dataFinal` são obrigatórias
- A consulta pode ser feita por `etiqueta`, `nserlum` ou `nserlums`
- Quando `etiqueta` não for informada, pelo menos um SIMUC deve ser enviado
- Quando uma `etiqueta` é enviada, o sistema expande automaticamente o histórico para o grupo correspondente quando aplicável
- Quando um `nserlum` é enviado, o sistema também pode expandir a consulta para o grupo histórico da etiqueta atual
- O serviço seleciona automaticamente a tabela histórica correta de `logsuc` conforme servidor e período consultado

### Regras de Validação
- `dataInicial` é obrigatória
- `dataFinal` é obrigatória
- Deve ser informado ao menos um entre `etiqueta`, `nserlum` ou `nserlums`
- Se `etiqueta` não for enviada, a consulta exige `nserlum` ou `nserlums`
- O intervalo de datas deve ser válido para processamento

## Exemplos de Uso

### Consulta por Etiqueta e Múltiplos SIMUCs

##### Exemplo de uso em Python

```python
import requests

headers = {
	"Authorization": "Bearer <token>",
	"Content-Type": "application/json"
}

payload = {
	"dataInicial": "2026-05-01",
	"dataFinal": "2026-05-21",
	"nserlums": ["1234567890", "1234567891"],
	"etiqueta": "ABC123"
}

response = requests.post(
	url="https://url/historico-simuc/v1",
	headers=headers,
	json=payload
)

print(response.status_code)  # 200
print(response.json())
```

> Substitua `<token>` pelo token obtido no processo de autenticação.

##### Parâmetros da URL:
| Identificador | Tipo     | Obrigatório | Descrição                              |
|---------------|----------|:-----------:|:---------------------------------------|
| version       | `string` | **Sim**     | Versão da API (ex: v1)                 |

##### Parâmetros do Body:
| Identificador | Tipo         | Obrigatório | Descrição                                                                 |
|---------------|--------------|:-----------:|:--------------------------------------------------------------------------|
| dataInicial   | `string`     | **Sim**     | Data inicial da consulta no formato `YYYY-MM-DD`                          |
| dataFinal     | `string`     | **Sim**     | Data final da consulta no formato `YYYY-MM-DD`                            |
| etiqueta      | `string`     | Não         | Etiqueta base para expansão automática do grupo histórico                 |
| nserlum       | `string`     | Não         | Número serial de um único SIMUC                                           |
| nserlums      | `string[]`   | Não         | Lista com um ou mais números seriais de SIMUC                             |

### Exemplo do Resultado
```json
{
	"data": {
		"success": true,
		"status": "sucesso",
		"message": "Histórico consultado com sucesso",
		"simucsConsultados": [
			2601000011,
			2601000012
		],
		"historico": [
			{
				"nserlum": 2601000011,
				"etiqueta": "AA",
				"dataLeitura": "2026-05-20T12:41:50Z",
				"hora": 12,
				"tensao": 243,
				"corrente": 0,
				"fatorPot": 0,
				"potAtiva": 0,
				"potAparente": 0,
				"falhas": 0,
				"luz": 255,
				"status": 0,
				"tempoResp": 8.42,
				"rssiF1": 46,
				"rssiF2": 63,
				"rssiF3": 50,
				"temperatura": 28,
				"whAcc": 28.92
			}
		]
	},
	"success": true,
	"elapsed_time": "1.8s"
}
```

### Consulta por Um Único SIMUC

##### Exemplo de uso em Python

```python
import requests

headers = {
	"Authorization": "Bearer <token>",
	"Content-Type": "application/json"
}

payload = {
	"dataInicial": "2026-05-01",
	"dataFinal": "2026-05-21",
	"nserlum": "1234567890"
}

response = requests.post(
	url="https://url/historico-simuc/v1",
	headers=headers,
	json=payload
)

print(response.status_code)  # 200
print(response.json())
```

## Dicionário de Resultados

### Resposta de Consulta
##### Bloco PRINCIPAL:
| Identificador | Tipo     | Descrição                                               |
|:------------- |----------|:--------------------------------------------------------|
| data          | `object` | Resultado da consulta de histórico                      |
| success       | `bool`   | Status de sucesso/falha da interação                    |
| elapsed_time  | `string` | Tempo de duração da execução                            |

##### Bloco DATA:
| Identificador      | Tipo        | Descrição                                                        |
|:-------------------|-------------|:-----------------------------------------------------------------|
| success            | `bool`      | Indica se a consulta foi executada com sucesso                   |
| status             | `string`    | Status da operação (`sucesso` ou `erro`)                         |
| message            | `string`    | Mensagem descritiva do resultado                                 |
| simucsConsultados  | `int[]`     | Relação de SIMUCs efetivamente considerados na consulta          |
| historico          | `object[]`  | Lista de registros históricos retornados                         |

##### Itens do Array `historico`:
| Identificador | Tipo      | Descrição                                              |
|:--------------|-----------|:-------------------------------------------------------|
| nserlum       | `int`     | Número serial do SIMUC                                 |
| etiqueta      | `string`  | Etiqueta associada ao registro                         |
| dataLeitura   | `string`  | Data e hora da leitura no formato ISO 8601             |
| hora          | `int`     | Hora da leitura                                        |
| tensao        | `int`     | Tensão elétrica registrada                             |
| corrente      | `int`     | Corrente elétrica registrada                           |
| fatorPot      | `int`     | Fator de potência registrado                           |
| potAtiva      | `int`     | Potência ativa registrada                              |
| potAparente   | `int`     | Potência aparente registrada                           |
| falhas        | `int`     | Quantidade ou indicador de falhas                      |
| luz           | `int`     | Valor de leitura de luminosidade                       |
| status        | `int`     | Status retornado pelo dispositivo                      |
| tempoResp     | `float`   | Tempo de resposta da comunicação                       |
| rssiF1        | `int`     | Intensidade de sinal na fase F1                        |
| rssiF2        | `int`     | Intensidade de sinal na fase F2                        |
| rssiF3        | `int`     | Intensidade de sinal na fase F3                        |
| temperatura   | `int`     | Temperatura interna registrada                        |
| whAcc         | `float`   | Energia acumulada                                      |

## Regras Internas da Implementação

### Fluxo de Resolução
1. O endpoint recebe a requisição `POST /historico-simuc/{version}`
2. O handler valida os parâmetros obrigatórios
3. O service resolve as peças consultadas a partir de `etiqueta`, `nserlum` ou `nserlums`
4. Havendo etiqueta, o grupo histórico é expandido automaticamente quando aplicável
5. O período é aplicado individualmente com corte por `dataInicial` e `dataFinal`
6. A tabela histórica correta de `logsuc` é escolhida conforme servidor e período
7. O retorno consolida os SIMUCs consultados e os registros históricos no mesmo payload

### Cobertura da Implementação
- Endpoint `POST` registrado em `routes.go`
- Handler implementado em `endpoint.go`
- Models de request e response definidos em `model.go`
- Lógica de consulta implementada em `service_query.go`

## Códigos de Resposta

### Status de Operação
| Status    | Descrição                              |
|:----------|:---------------------------------------|
| `sucesso` | Consulta executada com sucesso         |
| `erro`    | Falha na execução da consulta          |

### Erros HTTP Comuns
| Código | Descrição                                    |
|:-------|:---------------------------------------------|
| 400    | Parâmetros inválidos ou malformados          |
| 401    | Token de autenticação inválido ou expirado   |
| 403    | Usuário sem permissão para consulta          |
| 422    | Erro de validação dos dados informados       |
| 500    | Erro interno do servidor                     |

### Exemplo de Erro
```json
{
	"error": "Parâmetros inválidos",
	"message": "Informe etiqueta ou ao menos um SIMUC para consulta",
	"success": false,
	"elapsed_time": "0.002s"
}
```

## Segurança

- JWT obrigatório para o endpoint
- Validação de permissões por usuário
- Logs de auditoria da consulta mantidos no servidor
- Sanitização de dados de entrada

## Nota Importante

- O envio do token de acesso é obrigatório para este endpoint
- `dataInicial` e `dataFinal` sempre devem ser informadas
- `etiqueta` é opcional, mas quando enviada pode expandir automaticamente a consulta para o grupo histórico relacionado
- Se `etiqueta` não for enviada, é obrigatório informar `nserlum` ou `nserlums`
- O retorno consolida leituras elétricas e dados de ambiente no mesmo payload
