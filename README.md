# Api de Integração Sistema KDL

Este repositório contém a documentação e exemplos de uso da **API de Integração Sistema KDL**, responsável pela integração e monitoramento de falhas de telegestão.

## Sumário

- [Descrição](#descrição)
- [Pré-requisitos](#pré-requisitos)
- [Endpoints Disponíveis](#endpoints-disponíveis)
- [Exemplo de Uso Rápido](#exemplo-de-uso-rápido)
- [Contato](#contato)

## Endpoints Disponíveis

### Autenticação
- [Autenticação](autenticacao.md) - Como obter e usar tokens de acesso

### Falhas
- [Lista de Falhas](lista-falhas.md) - Consulta de falhas no sistema
- [Confirmação de Falhas Baixadas](confirma-falhas-baixadas.md) - Confirmar resolução de falhas

### Concentradores (SIMCON)
- [Lista de SIMCONs](lista-simcons.md) - Lista todos os concentradores
- [Detalhes do SIMCON](detalhes-simcon.md) - Informações detalhadas de um concentrador específico

### Unidades (SIMUC)
- [Lista de SIMUCs](lista-simucs.md) - Lista todas as unidades de controle
- [Detalhes do SIMUC](detalhes-simuc.md) - Informações detalhadas de uma unidade específica
- [Comandos SIMUC](comandos-simuc.md) - Execução de comandos em unidades SIMUC (único ou batch)

## Descrição

A API permite consultar e atualizar o status de falhas em dispositivos de telegestão, além de gerenciar informações de concentradores (SIMCON) e unidades de controle (SIMUC), facilitando o monitoramento e a manutenção dos equipamentos de iluminação pública.

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
resp = requests.get("https://simcidadesinteligentes.com.br:44300/lista-falhas/v1/cidade", headers=headers)
print(resp.json())
```

## Contato 

Dúvidas ou suporte: [suporte@kdliluminacao.com.br](mailto:suporte@kdliluminacao.com.br)

---

Consulte os arquivos de documentação para mais detalhes:

### Autenticação
- [Autenticação](autenticacao.md)

### Falhas
- [Lista de Falhas](lista-falhas.md)
- [Confirmação de Falhas Baixadas](confirma-falhas-baixadas.md)

### Concentradores (SIMCON)
- [Lista de SIMCONs](lista-simcons.md)
- [Detalhes do SIMCON](detalhes-simcon.md)

### Unidades (SIMUC)
- [Lista de SIMUCs](lista-simucs.md)
- [Detalhes do SIMUC](detalhes-simuc.md)
- [Comandos SIMUC](comandos-simuc.md)
