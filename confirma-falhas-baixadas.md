# Api de Integração Sistema KDL

### KDL API REST
*versão 0.0.3*

Este endpoint é responsável pela atualização do status em registros de falhas já entregues.

Quando uma notificação de falha é visualizada, é necessário atualizar seu status para indicar que ela já foi tratada, a fim de evitar que a mesma notificação seja exibida novamente em futuras pesquisas.

Essa atualização é feita enviando uma lista de identificadores para este endpoint, que será responsável por atualizar o status da notificação.

Esta regra visa preservar a ordem e eficácia do sistema de monitoramento, de modo que os usuários possam dedicar-se somente às notificações pertinentes e proveitosas.

| Método | URI                                         | Exemplo                                                                 |
|--------|---------------------------------------------|:------------------------------------------------------------------------|
| POST   | `/confirma-falhas-baixadas/v1/sua_cidade`         | https://simcidadesinteligentes.com.br:44300/confirma-falhas-baixadas/v1/sua_cidade |

## Autenticação

Para acessar este endpoint, é necessário incluir o token de autenticação no header da requisição. Consulte o arquivo `autenticacao.md` para saber como obter o token.

Inclua o token no header `Authorization`:

```
Authorization: Bearer <token>
```

##### Exemplo de uso em Python

```python
import requests
import json

# lista de valores uint32
listaUint32 = [123456789, 987654321, 111111111]

# converter a lista em formato JSON
jsonData = json.dumps(listaUint32)

# definir os cabeçalhos da requisição
headers = {
    "Content-Type": "application/json",
    "Authorization": "Bearer <token>"
}

# fazer a requisição POST
response = requests.post(
    url="https://simcidadesinteligentes.com.br:44300/confirma-falhas-baixadas/v1/sua_cidade",
    data=jsonData,
    headers=headers
)

print(response.status_code)
print(response.json())
```

> Substitua `<token>` pelo token obtido no processo de autenticação.

## Nota importante
O envio do token de acesso é obrigatório para este endpoint.