# Api de Integração Sistema KDL

Este repositório contém a documentação e exemplos de uso da **API de Integração Sistema KDL**, responsável pela integração e monitoramento de falhas de telegestão.

## Sumário

- [Descrição](#descrição)
- [Pré-requisitos](#pré-requisitos)
- [Autenticação](autenticacao.md)
- [Lista de Falhas](lista-falhas.md)
- [Confirmação de Falhas Baixadas](confirma-falhas-baixadas.md)
- [Exemplo de Uso Rápido](#exemplo-de-uso-rápido)
- [Contato](#contato)

## Descrição

A API permite consultar e atualizar o status de falhas em dispositivos de telegestão, facilitando o monitoramento e a manutenção dos equipamentos.

## Pré-requisitos

- Python 3.x (para exemplos)
- Biblioteca `requests` instalada (`pip install requests`)
- Credenciais de acesso (fornecidas pela equipe responsável)

## Exemplo de Uso Rápido

```python
import requests

# Autenticação
login = {"username": "seu_login", "password": "sua_senha"}
resp = requests.post("https://simcidadesinteligentes.com.br:44300/login", json=login)
token = resp.json()["token"]

# Consulta de falhas
headers = {"Authorization": f"Bearer {token}"}
resp = requests.get("https://simcidadesinteligentes.com.br:44300/lista-falhas/v1/maua", headers=headers)
print(resp.json())
```

## Contato

Dúvidas ou suporte: [email@dominio.com](mailto:email@dominio.com)

---

Consulte os arquivos de documentação para mais detalhes:

- [Autenticação](autenticacao.md)
- [Lista de Falhas](lista-falhas.md)
- [Confirmação de Falhas Baixadas](confirma-falhas-baixadas.md)