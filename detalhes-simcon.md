# Api de Integração Sistema KDL - Detalhes SIMCON

### KDL API REST
*versão 0.0.3*

Este endpoint é responsável pela entrega de informações detalhadas de um concentrador específico (SIMCON).

| Método | URI                          | Exemplo                                                                 |
| ------ |------------------------------|:------------------------------------------------------------------------|
| GET    | `/detalhes-simcon/v1/{simcon}` | http://url/detalhes-simcon/v1/2350005 |

## Autenticação

Para acessar este endpoint, é necessário incluir o token de autenticação no header da requisição. Consulte o arquivo `autenticacao.md` para saber como obter o token.

Inclua o token no header `Authorization`:

```
Authorization: Bearer <token>
```

##### Exemplo de uso em Python

```python
import requests

headers = {
    "Authorization": "Bearer <token>"
}

simcon_id = "2350005"
response = requests.get(
    url=f"http://url/detalhes-simcon/v1/{simcon_id}",
    headers=headers
)

print(response.status_code)
print(response.json())
```

> Substitua `<token>` pelo token obtido no processo de autenticação.

##### Parâmetros da URL:
| Identificador | Tipo     | Obrigatório | Descrição                                                    | 
|---------------|----------|:-----------:|:-------------------------------------------------------------| 
| version       | `string` | **Sim**     | Versão da API (ex: v1)                                       |
| simcon        | `string` | **Sim**     | Identificador do concentrador SIMCON                         |

### Exemplo do Resultado:
```json
{
    "data": {
        "type": "detalhes-simcon",
        "result": [
            {
                "idSimcon": 2350005,
                "label": "",
                "areaGroup": "",
                "observations": "",
                "createdAt": "2025-07-30T00:00:00Z",
                "lat": -23.59413,
                "lng": -46.82653,
                "address": "Indeterminado",
                "lastReadings": "2025-07-30T16:55:55Z",
                "hardwareVersion": "2.42",
                "softwareVersion": "2.65",
                "voltage": 5.02,
                "statusConnection": "2025-07-30T17:00:16Z",
                "signal": 19,
                "ip": "177.190.2.145",
                "operator": "Não Identificado",
                "icon": 1,
                "totalSimucs": 2,
                "connectionType": "3G",
                "ipAddress": "177.190.2.145",
                "cardInUsing": 1,
                "simcardOperator": "Não Identificado",
                "signalLevel": 19,
                "connectedSince": null,
                "disconnectedSince": "2025-07-30T17:00:16Z",
                "simucReadingTime": "2025-07-30T16:55:55Z",
                "installationDate": "2025-07-30T00:00:00Z",
                "powerVoltage": 5.02
            }
        ]
    },
    "total": 1,
    "totalRetornado": 1,
    "success": true,
    "elapsedTime": "31.4823ms"
}
```

### Dicionário do Resultado:
##### Bloco PRINCIPAL:
| Identificador   | Tipo     | Descrição                                                           | 
|:--------------- |----------|:--------------------------------------------------------------------| 
| data            | `object` | Resultado da consulta                                               | 
| total           | `int`    | Quantidade de itens encontrados                                     |
| totalRetornado  | `int`    | Quantidade de itens retornados                                      |
| success         | `bool`   | Status de sucesso/falha da interação                                | 
| elapsedTime     | `string` | Tempo de duração da consulta                                        | 

##### Bloco DATA:
| Identificador | Tipo            | Descrição                            | 
|:--------------|-----------------|:-------------------------------------| 
| type          | `string`        | Identifica o tipo de interação       | 
| result        | `array<object>` | Lista com os detalhes do SIMCON      | 

##### Bloco RESULT:
| Identificador         | Tipo      | Descrição                                         | 
|:----------------------|-----------|:--------------------------------------------------| 
| idSimcon              | `int`     | Identificador único do concentrador SIMCON       |
| label                 | `string`  | Etiqueta/rótulo do concentrador                   |
| areaGroup             | `string`  | Grupo da área onde está instalado                |
| observations          | `string`  | Observações sobre o concentrador                  |
| createdAt             | `string`  | Data/hora de criação do registro                  |
| lat                   | `float`   | Latitude da localização                           |
| lng                   | `float`   | Longitude da localização                          |
| address               | `string`  | Endereço de instalação                            |
| lastReadings          | `string`  | Data/hora da última leitura realizada             |
| hardwareVersion       | `string`  | Versão do hardware                                |
| softwareVersion       | `string`  | Versão do software                                |
| voltage               | `float`   | Tensão atual do equipamento                       |
| statusConnection      | `string`  | Data/hora do último status de conexão             |
| signal                | `int`     | Nível do sinal de comunicação                     |
| ip                    | `string`  | Endereço IP do concentrador                       |
| operator              | `string`  | Operadora de telefonia utilizada                  |
| icon                  | `int`     | Código do ícone para representação visual         |
| totalSimucs           | `int`     | Total de SIMUCs conectados ao concentrador        |
| connectionType        | `string`  | Tipo de conexão utilizada (3G, 4G, etc.)         |
| ipAddress             | `string`  | Endereço IP (duplicado de ip)                     |
| cardInUsing           | `int`     | Número do cartão SIM em uso                       |
| simcardOperator       | `string`  | Operadora do cartão SIM (duplicado de operator)   |
| signalLevel           | `int`     | Nível do sinal (duplicado de signal)              |
| connectedSince        | `string`  | Data/hora desde quando está conectado             |
| disconnectedSince     | `string`  | Data/hora desde quando está desconectado          |
| simucReadingTime      | `string`  | Hora da última leitura dos SIMUCs                 |
| installationDate      | `string`  | Data de instalação do equipamento                 |
| powerVoltage          | `float`   | Tensão de alimentação (duplicado de voltage)      |

##### Códigos de Ícone:
| Icon | Descrição                | 
|:-----|:-------------------------| 
| 1    | SIMCON Online            |
| 2    | SIMCON Offline           |
| 3    | SIMCON com Problemas     |

## Nota importante
- O envio do token de acesso é obrigatório para este endpoint.
- O parâmetro `{simcon}` na URL deve ser substituído pelo ID do concentrador desejado.
- Caso o concentrador não seja encontrado, será retornado um erro 404.
