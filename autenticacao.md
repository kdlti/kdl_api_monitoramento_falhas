# Autenticação e Extração de Token

Para acessar os endpoints da API, é necessário realizar login e obter um token de autenticação.

## Endpoint de Login

- **URL:** `https://simcidadesinteligentes.com.br:44300/login`
- **Método:** `POST`
- **Headers:**  
  `Content-Type: application/json`

### Exemplo de Requisição

```json
{
  "username": "seu_login",
  "password": "sua_senha"
}
```

> **Obs:** O login e senha são fornecidos pela equipe responsável.

### Exemplo de Resposta

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

O campo `"token"` deve ser utilizado para autenticar as próximas requisições.

## Como usar o token

Inclua o token no header `Authorization` das requisições aos endpoints protegidos:

```
Authorization: Bearer <token>
```

### Exemplo usando `curl`

```bash
curl -X POST "https://simcidadesinteligentes.com.br:44300/login" ^
  -H "Content-Type: application/json" ^
  -d "{\"username\": \"seu_login\", \"password\": \"sua_senha\"}"
```

Depois, utilize o token retornado:

```bash
curl -X GET "https://api-afericao.kdltelegestao.com/lista-falhas/v1/maua" ^
  -H "Authorization: Bearer <token>"
```