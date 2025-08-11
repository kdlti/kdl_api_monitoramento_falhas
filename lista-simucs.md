# Api de Integração Sistema KDL - Lista SIMUCs

### KDL API REST
*versão 0.0.3*

Este endpoint é responsável pela entrega de uma lista de unidades (SIMUCs) com suas informações básicas, localizações e status.

| Método | URI                          | Exemplo                                                                 |
| ------ |------------------------------|:------------------------------------------------------------------------|
| GET    | `/lista-simucs/v1`           | http://url/lista-simucs/v1                                              |

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
    "offset": 0,
    "simcon": "2206203"
}

response = requests.get(
    url="http://url/lista-simucs/v1",
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
| Identificador | Tipo     | Default   | Descrição                                                                                                                                                                | 
| -------------- |----------|:--------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------| 
| limit          | `int`    | **1000**  | Quantidade de itens retornados.                                                                                                                                          |
| offset         | `int`    | **0**     | Paginação do resultado.                                                                                                                                                  |
| simcon         | `string` | **-**     | Filtrar por ID do concentrador específico.                                                                                                                              |

O parâmetro limit especifica o número máximo de documentos que devem ser retornados pela consulta. Por exemplo, se você definir limit como 10 em uma consulta, ela retornará no máximo 10 documentos. Se houver mais documentos que correspondam à consulta, eles serão ignorados.

O parâmetro offset é usado para permitir a paginação dos resultados. Ele especifica o número de documentos que devem ser ignorados antes de retornar os resultados. Por exemplo, se você definir offset como 10 em uma consulta com limit definido como 10, a consulta retornará os documentos 11 a 20 que correspondem à consulta.

O parâmetro simcon permite filtrar apenas SIMUCs que estão conectados a um concentrador específico.

| Exemplo de uso dos parâmetros                    | 
|:-------------------------------------------------| 
| ?limit=200&offset=0                              |
| ?simcon=2206203                                  |
| ?limit=50&offset=100&simcon=2508002              |

### Exemplo do Resultado:
```json
{
    "data": [
        {
            "idSimuc": "1234567891",
            "etiqueta": "1598522",
            "idSimcon": "2206203",
            "lat": "0.00000",
            "lng": "0.00000",
            "status": 52,
            "type": "Lista Simucs",
            "result": 51
        },
        {
            "idSimuc": "2432005153",
            "etiqueta": "",
            "idSimcon": "2206203",
            "lat": "-23.59469",
            "lng": "-46.82609",
            "status": 1,
            "type": "Lista Simucs",
            "result": 51
        },
        {
            "idSimuc": "2436300015",
            "etiqueta": "",
            "idSimcon": "2508002",
            "lat": "-23.59450",
            "lng": "-46.82542",
            "status": 6,
            "type": "Lista Simucs",
            "result": 51
        },
        {
            "idSimuc": "2448000004",
            "etiqueta": "",
            "idSimcon": "2206203",
            "lat": "-23.59457",
            "lng": "-46.82568",
            "status": 7,
            "type": "Lista Simucs",
            "result": 51
        }
    ],
    "total": 51,
    "offset": 0,
    "limit": 1000,
    "success": true,
    "elapsedTime": "70.4792ms",
    "totalRetornado": 51
}
```

### Dicionário do Resultado:
##### Bloco PRINCIPAL:
| Identificador   | Tipo     | Descrição                                                           | 
|:--------------- |----------|:--------------------------------------------------------------------| 
| data            | `array`  | Lista de SIMUCs encontrados                                         | 
| total           | `int`    | Quantidade total de itens encontrados                               |
| offset          | `int`    | Valor do offset utilizado na consulta                              |
| limit           | `int`    | Valor do limit utilizado na consulta                               |
| success         | `bool`   | Status de sucesso/falha da interação                                | 
| elapsedTime     | `string` | Tempo de duração da consulta                                        | 
| totalRetornado  | `int`    | Quantidade de itens retornados nesta página                         |

##### Bloco DATA (array de objetos):
| Identificador | Tipo      | Descrição                                         | 
|:--------------|-----------|:--------------------------------------------------| 
| idSimuc       | `string`  | Identificador único da unidade SIMUC             |
| etiqueta      | `string`  | Etiqueta/rótulo da unidade                        |
| idSimcon      | `string`  | Identificador do concentrador pai                 |
| lat           | `string`  | Latitude da localização                           |
| lng           | `string`  | Longitude da localização                          |
| status        | `int`     | Código do status atual da unidade                 |
| type          | `string`  | Tipo da consulta (sempre "Lista Simucs")          |
| result        | `int`     | Código do resultado da última operação            |

##### Códigos de Status:
| Status | Descrição                                                                                            | 
|:-------|:-----------------------------------------------------------------------------------------------------| 
| 1      | LUMINÁRIA acesa durante o dia                                                                        |
| 2      | LUMINÁRIA apagada durante a noite                                                                    |
| 4      | LUMINÁRIA com queda de potência                                                                      |
| 5      | LUMINÁRIA com falha e defeito                                                                        |
| 6      | Luminária Ligado                                                                                     |
| 7      | Luminária Desligado                                                                                  |
| 8      | Luminária Dimerizado                                                                                 |
| 51     | SIMUC sem comunicação (evento informado a cada 6h)                                                   |
| 52     | SIMUC sem leituras coletadas desde a instalação (evento informado a cada 6h, após 48h da instalação) |
| 251    | REDE em tensão abaixo de 190V                                                                        |
| 252    | REDE em tensão alta > 264V                                                                           |

## Detalhamento dos Status

### Status de Luminária (1-8)
**1 - LUMINÁRIA acesa durante o dia**
Luminária permanece acesa em horário diurno quando deveria estar apagada.

**2 - LUMINÁRIA apagada durante a noite**
Luminária permanece apagada em horário noturno quando deveria estar acesa.

**4 - LUMINÁRIA com queda de potência**
Luminária funcionando com potência reduzida em 30% ou mais da potência nominal.

**5 - LUMINÁRIA com falha e defeito**
Luminárias que não respondem a comandos ou apresentam defeitos técnicos.

**6 - Luminária Ligado**
Estado operacional normal - luminária funcionando corretamente.

**7 - Luminária Desligado**
Estado operacional normal - luminária desligada conforme programação.

**8 - Luminária Dimerizado**
Luminária funcionando com potência reduzida por programação de dimmer.

### Status de Comunicação (51-52)
**51 - SIMUC sem comunicação**
- Falta de energia no ponto;
- Interferência de rádio no local;
- Problemas de conexão física;
- Defeito na peça.

**52 - SIMUC sem leituras desde instalação**
Unidade instalada há mais de 48h que nunca estabeleceu comunicação.

### Status de Rede (251-252)
**251 - REDE em tensão abaixo de 190V**
Tensão da rede elétrica abaixo do limite mínimo operacional.

**252 - REDE em tensão alta > 264V**
Tensão da rede elétrica acima do limite máximo operacional.

## Filtros Disponíveis

### Por Concentrador
Use o parâmetro `simcon` para listar apenas SIMUCs de um concentrador específico:
```
GET /lista-simucs/v1?simcon=2206203
```

### Por Paginação
Combine `limit` e `offset` para navegar por grandes conjuntos de dados:
```
GET /lista-simucs/v1?limit=50&offset=100
```

### Combinando Filtros
Todos os parâmetros podem ser combinados:
```
GET /lista-simucs/v1?simcon=2508002&limit=25&offset=0
```

## Nota importante
- O envio do token de acesso é obrigatório para este endpoint.
- Este endpoint suporta paginação através dos parâmetros `limit` e `offset`.
- Use o parâmetro `simcon` para filtrar por concentrador específico.
- Para obter detalhes completos de um SIMUC específico, utilize o endpoint `/detalhes-simuc/v1/{simuc}`.
- O campo `result` sempre retorna 51 e representa o código de resultado da operação.
