# Api de Integração Sistema KDL - Lista SIMCONs

### KDL API REST
*versão 0.0.3*

Este endpoint é responsável pela entrega de uma lista de concentradores (SIMCONs) com suas informações básicas e localizações.

| Método | URI                          | Exemplo                                                                 |
| ------ |------------------------------|:------------------------------------------------------------------------|
| GET    | `/lista-simcons/v1`          | http://url/lista-simcons/v1                                             |

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

params = {
    "limit": 200,
    "offset": 0
}

response = requests.get(
    url="http://url/lista-simcons/v1",
    headers=headers,
    params=params
)

print(response.status_code)
print(response.json())
```

> Substitua `<token>` pelo token obtido no processo de autenticação.

##### Parâmetros da URL:
| Identificador | Tipo     | Obrigatório | Descrição                                                    | 
|---------------|----------|:-----------:|:-------------------------------------------------------------| 
| version       | `string` | **Sim**     | Versão da API (ex: v1)                                       |

##### Parâmetros opcionais:
| Identificador | Tipo   | Default   | Descrição                                                                                                                                                                | 
| -------------- | -------| :--------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------| 
| limit          | `int`  |  **1000** | Quantidade de itens retornados.                                                                                                                                          |
| offset         | `int`  |  **0**    | Paginação do resultado.                                                                                                                                                  |

O parâmetro limit especifica o número máximo de documentos que devem ser retornados pela consulta. Por exemplo, se você definir limit como 10 em uma consulta, ela retornará no máximo 10 documentos. Se houver mais documentos que correspondam à consulta, eles serão ignorados.

O parâmetro offset é usado para permitir a paginação dos resultados. Ele especifica o número de documentos que devem ser ignorados antes de retornar os resultados. Por exemplo, se você definir offset como 10 em uma consulta com limit definido como 10, a consulta retornará os documentos 11 a 20 que correspondem à consulta.

| Exemplo de uso dos parâmetros    | 
|:---------------------------------| 
| ?limit=200&offset=0 |

### Exemplo do Resultado:
```json
{
    "data": [
        {
            "idSimcon": "2508002",
            "etiqueta": "",
            "lat": "-23.59436",
            "lng": "-46.82554",
            "status": 204,
            "type": "",
            "result": 0
        },
        {
            "idSimcon": "2206203",
            "etiqueta": "",
            "lat": "-23.59428",
            "lng": "-46.82586",
            "status": 204,
            "type": "",
            "result": 0
        },
        {
            "idSimcon": "2419999",
            "etiqueta": "",
            "lat": "-23.59476",
            "lng": "-46.82505",
            "status": 204,
            "type": "",
            "result": 0
        },
        {
            "idSimcon": "2508001",
            "etiqueta": "",
            "lat": "-23.59332",
            "lng": "-46.82498",
            "status": 204,
            "type": "",
            "result": 0
        },
        {
            "idSimcon": "2350005",
            "etiqueta": "",
            "lat": "-23.59413",
            "lng": "-46.82653",
            "status": 204,
            "type": "",
            "result": 0
        }
    ],
    "total": 5,
    "offset": 0,
    "limit": 1000,
    "success": true,
    "elapsedTime": "40.6964ms",
    "totalRetornado": 5
}
```

### Dicionário do Resultado:
##### Bloco PRINCIPAL:
| Identificador   | Tipo     | Descrição                                                           | 
|:--------------- |----------|:--------------------------------------------------------------------| 
| data            | `array`  | Lista de SIMCONs encontrados                                        | 
| total           | `int`    | Quantidade total de itens encontrados                               |
| offset          | `int`    | Valor do offset utilizado na consulta                              |
| limit           | `int`    | Valor do limit utilizado na consulta                               |
| success         | `bool`   | Status de sucesso/falha da interação                                | 
| elapsedTime     | `string` | Tempo de duração da consulta                                        | 
| totalRetornado  | `int`    | Quantidade de itens retornados nesta página                         |

##### Bloco DATA (array de objetos):
| Identificador | Tipo      | Descrição                                         | 
|:--------------|-----------|:--------------------------------------------------| 
| idSimcon      | `string`  | Identificador único do concentrador SIMCON       |
| etiqueta      | `string`  | Etiqueta/rótulo do concentrador                   |
| lat           | `string`  | Latitude da localização                           |
| lng           | `string`  | Longitude da localização                          |
| status        | `int`     | Código do status atual do concentrador            |
| type          | `string`  | Tipo do concentrador                              |
| result        | `int`     | Resultado da última operação                      |

##### Códigos de Status:
| Status | Descrição                                         | 
|:-------|:--------------------------------------------------| 
| 203    | SIMCON offline, sem conexão (30+ minutos)        |
| 204    | SIMCON offline, sem conexão (60+ minutos)        |
| 253    | SIMCON Online Chip 1                             |
| 254    | SIMCON Online Chip 2                             |
| 255    | SIMCON Cabeado                                    |
| 256    | SIMCON Offline                                    |
| 257    | SIMCON Cadastrado                                 |

## Detalhamento dos Status

**203 - SIMCON offline, sem conexão (30+ minutos)**
**204 - SIMCON offline, sem conexão (60+ minutos)**
Quando o SIMCON se encontra:
- Sem sinal da primeira operadora de celular;
- Sem sinal da segunda operadora de celular;
- Falta de energia no local com término da carga da bateria;
- Interferência de sinal provocada por outro tipo de rádio de alta potência;

**253 - SIMCON Online Chip 1**
Concentrador conectado através do primeiro chip SIM.

**254 - SIMCON Online Chip 2**
Concentrador conectado através do segundo chip SIM (redundância).

**255 - SIMCON Cabeado**
Concentrador conectado através de conexão cabeada (ethernet/cabo).

**256 - SIMCON Offline**
Concentrador totalmente desconectado da rede.

**257 - SIMCON Cadastrado**
Concentrador registrado no sistema mas ainda não ativo.

## Nota importante
- O envio do token de acesso é obrigatório para este endpoint.
- Este endpoint suporta paginação através dos parâmetros `limit` e `offset`.
- Para obter detalhes completos de um SIMCON específico, utilize o endpoint `/detalhes-simcon/v1/{simcon}`.
