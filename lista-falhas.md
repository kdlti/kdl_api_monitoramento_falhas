# Api de Integração Sistema KDL / UNIDESK-SIIP

### KDL API REST
*versão 0.0.3*

Este endpoint é resposável pela entrega de informações das peças que apresentam falhas na telegestão.


| Método | URI                          | Exemplo                                                   | 
| --- |------------------------------|:----------------------------------------------------------| 
| GET | `/lista-falhas/v1`           | api-afericao.kdltelegestao.com/lista-falhas/v1            |

##### Parâmetros opcionais:
| Identificador | Tipo   | Default   | Descrição                                                                                                                                                                | 
| -------------- | -------| :--------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------| 
| limit          | `int`  |  **1000** | Quantidade de itens retornados.                                                                                                                                          |
| offset     | `int`  |  **0**    | Paginação do resultado.                                                                                                                                                  |

O parâmetro limit especifica o número máximo de documentos que devem ser retornados pela consulta. Por exemplo, se você definir limit como 10 em uma consulta, ela retornará no máximo 10 documentos. Se houver mais documentos que correspondam à consulta, eles serão ignorados.

O parâmetro offset é usado para permitir a paginação dos resultados. Ele especifica o número de documentos que devem ser ignorados antes de retornar os resultados. Por exemplo, se você definir offset como 10 em uma consulta com limit definido como 10, a consulta retornará os documentos 11 a 20 que correspondem à consulta.

| Exemplo de uso dos parâmetros    | 
|:---------------------------------| 
| ?limit=200&offset=400 |


### Exemplo do Resultado:
``` json
{
    "data": {
        "type": "lista-falhas",
        "result": [
            {
                "id": 2281356173,
                "idSimuc": "2231021764",
                "idSimcon": "2236026",
                "etiqueta": "IP0505091",
                "subPrefeitura": "PINHEIROS",
                "dateTime": "2023-02-06T13:32:58Z",
                "status": 51
            }
        ]
    },
    "offset": 3027,
    "limit": 1000,
    "total": 3028,
    "success": true,
    "elapsedTime": "294.0598ms"
}
```
### Dicionário do Resultado:
##### Bloco PRINCIPAL:
| Identificador | Tipo     | Descrição                                                           | 
|:--------------|----------|:--------------------------------------------------------------------| 
| data          | `object` | Resultado da consulta                                               | 
| total         | `int`    | Quantidade de itens encontrados                                     |
| totalRetornado | `int`  | Quantidade de itens retornados                                     |
| limit         | `int`    | Quantidade de itens retornados por página                           | 
| offset        | `int`    | O parâmetro usado para permitir a paginação dos resultados |
| success       | `bool`   | Status de sucesso/falha da interação                                | 
| elapsedTime   | `string` | Tempo de duração da consulta                                        | 

##### Bloco DATA:
| Identificador | Tipo            | Descrição                            | 
|:--------------|-----------------|:-------------------------------------| 
| type          | `string`        | Identifica o tipo de interação       | 
| result        | `array<object>` | Lista de falhas de peças encontradas | 

##### Bloco RESULT:
| Identificador         | Tipo     | Descrição                             | 
|:----------------------|----------|:--------------------------------------| 
| id            | `uint32` | Identificador do evento               | 
| idSimuc            | `string` | Identificador da peça com falha       |
| idSimcon              | `string` | Identificador do concentrador         |
| etiqueta         | `string` | Identificador da etiqueta             | 
| subPrefeitura        | `string` | Identificador da SubPrefeitura        | 
| dateTime              | `string` | Dia/Hora/Minuto do registro do evento |
| status             | `string` | Código do evento                      |


##### Bloco Código de Status:
| Status | Descrição                                                                                            | 
|:-------|:-----------------------------------------------------------------------------------------------------| 
| 1      | LUMINÁRIA acesa durante o dia                                                                        |
| 2      | LUMINÁRIA apagada durante a noite                                                                    | 
| 4      | LUMINÁRIA com queda de potência                                                                      |
| 5      | LUMINÁRIA com falha e defeito                                                                        |
| 51     | SIMUC sem comunicação (evento informado a cada 6h)                                                   | 
| 52     | SIMUC sem leituras coletadas desde a instalação (evento informado a cada 6h, após 48h da instalação) | 
| 203    | SIMCON offline, sem conexão (30+ minutos)                                                                |
| 204    | SIMCON offline, sem conexão (60+ minutos)                                                                                     |
| 205    | SIMCOM em bateria                                                                                     |
| 251    | REDE em tensão abaixo de 190V                                                                                     |
| 252    | REDE em tensão alta > 264V                                                                                     |

## Detalhamento do Status
**1 - LUMINÁRIA acesa durante o dia**

**2 - LUMINÁRIA apagada durante a noite**

**4 - LUMINÁRIA com queda de potência**
1. Uma luminária que acende com potência reduzida em 30% da potência
nominal registrada no momento da instalação, exceto quando estiver
dimerizada.

**5 - LUMINÁRIA com falha e defeito**
Luminárias consideradas com falhas ou defeitos:
1. Enviado um comando (respeitado todas as etapas do comando) para acender, não acende;
2. Sensor de luz indicando baixa luminosidade para acendimento da luminária, não acende;
3. Dentro da programação do horário de acendimento da luminária, não acende;

**51 - SIMUC sem comunicação** (evento informado a cada 6h) 

Quando o sistema mostra que o SIMUC não retorna dados da leitura:
1. Falta de energia no ponto;
2. Interferência de rádio no local;
3. Ligação errada ou mal contato na tomada da luminária;
4. Defeito na peça;

**52 - SIMUC sem leituras coletadas desde a instalação** (evento informado a cada 6h,
após 48h da instalação)

**203 - SIMCON offline, sem conexão** (30+ minutos)
**204 - SIMCON offline, sem conexão** (60+ minutos)
Quando o SIMCON se encontra:
Sem sinal da primeira operadora de celular;
Sem sinal da segunda operadora de celular;
Falta de energia no local com término da carga da bateria;
Interferência de sinal provocada por outro tipo de rádio de alta potência;

**205 - SIMCOM em bateria**
Quando o SIMCOM se encontra:
Falta de energia no ponto de alimentação;
Queima da fonte interna por ocorrência de surtos elétricos;

**251 - REDE em tensão abaixo de 190V**

**252 - REDE em tensão alta > 264V**




## Nota importante
Nesta versão não será solicitado o envio de token de acesso.
