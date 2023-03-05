# Api de Integração Sistema KDL / UNIDESK-SIIP

### KDL API REST
*versão 0.0.3*

Este endpoint é resposável pela atualização do status em registros de falhas já entregues.

Quando uma notificação de falha é visualizada, é necessário atualizar seu status para indicar que ela já foi tratada, a fim de evitar que a mesma notificação seja exibida novamente em futuras pesquisas. 

Essa atualização é feita enviando uma lista de identificadores para este endpoint, que será responsável por atualizar o status da notificação. 

Esta regra visa preservar a ordem e eficácia do sistema de monitoramento, de modo que os usuários possam dedicar-se somente às notificações pertinentes e proveitosas.


| Método | URI                          | Exemplo                                                   | 
|--------|------------------------------|:----------------------------------------------------------| 
| POST   | `/confirma-falhas-baixadas.md/v1`           | api-afericao.kdltelegestao.com/confirma-falhas-baixadas.md/v1            |

##### Exemplo de uso
```go
// lista de valores uint32
listaUint32 = [123456789, 987654321, 111111111]

// converter a lista em formato JSON
jsonData = json.dumps(listaUint32)

// definir os cabeçalhos da requisição
headers = {"Content-Type": "application/json"}

// fazer a requisição POST
response = requests.post(url="api-afericao.kdltelegestao.com/confirma-falhas-baixadas.md/v1", data=jsonData, headers=headers)

```

## Nota importante
Nesta versão não será solicitado o envio de token de acesso.