# Api de Integração Sistema KDL - Detalhes SIMUC

### KDL API REST
*versão 0.0.3*

Este endpoint é responsável pela entrega de informações detalhadas de uma unidade específica (SIMUC).

| Método | URI                          | Exemplo                                                                 |
| ------ |------------------------------|:------------------------------------------------------------------------|
| GET    | `/detalhes-simuc/v1/{simuc}` | http://url/detalhes-simuc/v1/4000000103                                 |

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

simuc_id = "4000000103"
response = requests.get(
    url=f"http://url/detalhes-simuc/v1/{simuc_id}",
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
| simuc         | `string` | **Sim**     | Identificador da unidade SIMUC                               |

### Exemplo do Resultado:
```json
{
    "data": {
        "type": "detalhes-simuc",
        "result": [
            {
                "idSimuc": 4000000103,
                "idSimcon": 2206203,
                "sector": 1,
                "subSector": 0,
                "totalPower": 0,
                "numberOfLightFixtures": 1,
                "simucVersion": 0,
                "installationDate": "2024-12-18T00:00:00Z",
                "nearbySimucs": "0 - 0",
                "lastSimconEvent": null,
                "lastTrackingReading": null,
                "voltage": 0,
                "current": 0,
                "activePower": 0,
                "apparentPower": 0,
                "frequency": "60 Hz",
                "powerFactor": 0,
                "dimmerValue": null,
                "activeEnergy": 368,
                "ambientLight": 0,
                "rfNetwork": "146916 - 100%",
                "signal1": 35,
                "signal2": 39,
                "signal3": 41,
                "lat": -23.6153,
                "lng": -46.68945,
                "label": "IP0496689 WWW",
                "areaGroup": "PINHEIROS WWW",
                "observation": "observação",
                "failureDetected": "Nunca comunicou",
                "tipolum": 49,
                "iconSector": "10",
                "iconSimuc": 27,
                "programmingHour": "Desativado",
                "hourProgrammingValue": null,
                "dimmerProgramming": "Desativado",
                "dimmerProgrammingValue": null,
                "dimmerPresent": "Presente",
                "lightSensorPresent": "Presente",
                "temperatureSensorPresent": "Ausente",
                "relayTypePresent": "NF",
                "simucType": "Luminária",
                "lightingTime": "MjE6MDA6MDA=",
                "shutdownTime": "MjE6MDA6MDA=",
                "timeOn": "00:00:00",
                "consumption": 0
            }
        ]
    },
    "total": 1,
    "totalRetornado": 1,
    "success": true,
    "elapsedTime": "32.9398ms"
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
| result        | `array<object>` | Lista com os detalhes do SIMUC       | 

##### Bloco RESULT:
| Identificador             | Tipo      | Descrição                                                   | 
|:--------------------------|-----------|:------------------------------------------------------------| 
| idSimuc                   | `int`     | Identificador único da unidade SIMUC                       |
| idSimcon                  | `int`     | Identificador do concentrador pai                           |
| sector                    | `int`     | Setor onde a unidade está localizada                       |
| subSector                 | `int`     | Subsetor da localização                                     |
| totalPower                | `int`     | Potência total configurada (watts)                          |
| numberOfLightFixtures     | `int`     | Número de luminárias conectadas                             |
| simucVersion              | `int`     | Versão do firmware da unidade                               |
| installationDate          | `string`  | Data de instalação da unidade                               |
| nearbySimucs              | `string`  | SIMUCs próximos para comunicação RF                         |
| lastSimconEvent           | `string`  | Último evento reportado pelo SIMCON                         |
| lastTrackingReading       | `string`  | Última leitura de rastreamento                              |
| voltage                   | `int`     | Tensão elétrica medida (volts)                              |
| current                   | `int`     | Corrente elétrica medida (amperes)                          |
| activePower               | `int`     | Potência ativa medida (watts)                               |
| apparentPower             | `int`     | Potência aparente medida (VA)                               |
| frequency                 | `string`  | Frequência da rede elétrica                                 |
| powerFactor               | `int`     | Fator de potência                                           |
| dimmerValue               | `int`     | Valor atual do dimmer (0-100%)                              |
| activeEnergy              | `int`     | Energia ativa consumida (kWh)                               |
| ambientLight              | `int`     | Nível de luz ambiente detectado                             |
| rfNetwork                 | `string`  | Informações da rede RF (canal e qualidade)                  |
| signal1                   | `int`     | Intensidade do sinal RF 1 (dBm)                            |
| signal2                   | `int`     | Intensidade do sinal RF 2 (dBm)                            |
| signal3                   | `int`     | Intensidade do sinal RF 3 (dBm)                            |
| lat                       | `float`   | Latitude da localização                                     |
| lng                       | `float`   | Longitude da localização                                    |
| label                     | `string`  | Etiqueta/rótulo da unidade                                  |
| areaGroup                 | `string`  | Grupo da área onde está instalada                          |
| observation               | `string`  | Observações sobre a unidade                                 |
| failureDetected           | `string`  | Descrição da falha detectada                                |
| tipolum                   | `int`     | Código do tipo de luminária                                 |
| iconSector                | `string`  | Código do ícone do setor                                    |
| iconSimuc                 | `int`     | Código do ícone da unidade SIMUC                           |
| programmingHour           | `string`  | Status da programação por horário                           |
| hourProgrammingValue      | `string`  | Valor da programação de horário                             |
| dimmerProgramming         | `string`  | Status da programação do dimmer                             |
| dimmerProgrammingValue    | `string`  | Valor da programação do dimmer                              |
| dimmerPresent             | `string`  | Indica se o dimmer está presente                            |
| lightSensorPresent        | `string`  | Indica se o sensor de luz está presente                     |
| temperatureSensorPresent  | `string`  | Indica se o sensor de temperatura está presente             |
| relayTypePresent          | `string`  | Tipo de relé presente (NF/NA)                               |
| simucType                 | `string`  | Tipo da unidade SIMUC                                       |
| lightingTime              | `string`  | Horário de acendimento (codificado em base64)               |
| shutdownTime              | `string`  | Horário de desligamento (codificado em base64)              |
| timeOn                    | `string`  | Tempo total ligado no dia                                   |
| consumption               | `int`     | Consumo total de energia                                    |

##### Códigos de Ícone SIMUC:
| Icon | Descrição                    | 
|:-----|:-----------------------------| 
| 1    | SIMUC Online                 |
| 2    | SIMUC Offline                |
| 5    | SIMUC com Falha              |
| 27   | SIMUC sem Comunicação        |

##### Status de Programação:
| Status      | Descrição                               | 
|:------------|:----------------------------------------| 
| Ativado     | Programação está ativa                  |
| Desativado  | Programação está desabilitada           |

##### Sensores/Componentes:
| Status   | Descrição                          | 
|:---------|:-----------------------------------| 
| Presente | Componente está instalado          |
| Ausente  | Componente não está instalado      |

##### Tipos de Relé:
| Tipo | Descrição            | 
|:-----|:---------------------| 
| NF   | Normalmente Fechado  |
| NA   | Normalmente Aberto   |

## Detalhamento dos Campos Especiais

**rfNetwork**
Formato: "canal - qualidade%" - Indica o canal RF utilizado e a qualidade do sinal.

**lightingTime / shutdownTime**
Horários codificados em base64. Para decodificar:
```python
import base64
horario = base64.b64decode("MjE6MDA6MDA=").decode('utf-8')  # Resulta em "21:00:00"
```

**failureDetected**
Possíveis valores:
- "Nunca comunicou" - SIMUC nunca estabeleceu comunicação
- "Sem comunicação há X horas" - Tempo desde a última comunicação
- "Funcionando normalmente" - Sem falhas detectadas

## Nota importante
- O envio do token de acesso é obrigatório para este endpoint.
- O parâmetro `{simuc}` na URL deve ser substituído pelo ID da unidade desejada.
- Caso a unidade não seja encontrada, será retornado um erro 404.
- Para listar múltiplas unidades, utilize o endpoint `/lista-simcons/v1`.
