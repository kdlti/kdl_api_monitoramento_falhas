# Api de Integração Sistema KDL - Incluir SIMUC

### KDL API REST
*versão 0.0.3*

Este endpoint permite incluir uma nova unidade SIMUC no sistema, com funcionalidade opcional de etiquetas para cadastro automático de dados auxiliares.

| Método | URI                              | Exemplo                                                                 |
| ------ |----------------------------------|:------------------------------------------------------------------------|
| POST   | `/incluir-simuc/{version}`       | https://url/incluir-simuc/v1                                            |

## Autenticação

Para acessar este endpoint, é necessário incluir o token de autenticação no header da requisição. Consulte o arquivo `autenticacao.md` para saber como obter o token.

Inclua o token no header `Authorization`:

```
Authorization: Bearer <token>
```

## 🏷️ Funcionalidade de Etiquetas

### ✅ **Comportamento Adaptável**
- **SEM campos opcionais** = Inclusão normal (apenas SIMUC)
- **COM campos opcionais** = Inclusão + processamento de etiqueta

### 🚀 **Lógica Inteligente**
- Verificação automática de duplicatas
- Sistema de sufixos automáticos (A, B, C, etc.)
- Reutilização de dados de comissionamento
- Execução APENAS após sucesso da inclusão do SIMUC

## 📝 Exemplos de Uso

### Inclusão COM Etiqueta (Funcionalidade Completa)

##### Exemplo de uso em Python

```python
import requests

headers = {
    "Authorization": "Bearer <token>",
    "Content-Type": "application/json"
}

# Inclusão com etiqueta
simuc_data = {
    "simcon": 2525025,
    "nserlum": 2000000001,
    "latitude": -23.563593,
    "longitude": -46.654527,
    "tipolum": 49,
    "nsetor": 1,
    "etiqueta": "IP001",
    "sub": "LAPA",
    "obs": "TESTE"
}

response = requests.post(
    url="https://url/incluir-simuc/v1",
    headers=headers,
    json=simuc_data
)

print(response.status_code)  # 200
print(response.json())
```

> Substitua `<token>` pelo token obtido no processo de autenticação.

##### Parâmetros da URL:
| Identificador | Tipo     | Obrigatório | Descrição                                                    | 
|---------------|----------|:-----------:|:-------------------------------------------------------------| 
| version       | `string` | **Sim**     | Versão da API (ex: v1)                                       |

##### Parâmetros do Body (Inclusão COM Etiqueta):
| Identificador | Tipo     | Obrigatório | Descrição                                                    | 
|---------------|----------|:-----------:|:-------------------------------------------------------------| 
| simcon        | `int`    | **Sim**     | ID do concentrador SIMCON                                    |
| nserlum       | `int`    | **Sim**     | Número serial da luminária/SIMUC                             |
| latitude      | `float`  | **Sim**     | Latitude da localização                                      |
| longitude     | `float`  | **Sim**     | Longitude da localização                                     |
| tipolum       | `int`    | **Sim**     | Tipo de luminária (geralmente 49)                           |
| nsetor        | `int`    | **Sim**     | Número do setor                                              |
| etiqueta      | `string` | Não         | Etiqueta identificadora (ativa lógica de etiquetas)          |
| sub           | `string` | Não         | Subprefeitura ou região                                      |
| obs           | `string` | Não         | Observações adicionais                                       |

### Exemplo do Resultado (COM Etiqueta):
```json
{
    "data": {
        "success": true,
        "status": "sucesso",
        "message": "✅ SIMUC incluído com sucesso",
        "codigo": 2,
        "simcon": 2525025,
        "nserlum": 2000000001,
        "processed_at": "2024-01-15T10:30:00Z",
        "etiqueta_status": "Etiqueta cadastrada com sucesso"
    },
    "success": true,
    "elapsed_time": "2.5s"
}
```

### Inclusão SEM Etiqueta (Funcionamento Normal)

##### Exemplo de uso em Python

```python
import requests

headers = {
    "Authorization": "Bearer <token>",
    "Content-Type": "application/json"
}

# Inclusão tradicional
simuc_data = {
    "simcon": 2525025,
    "nserlum": 2000000002,
    "latitude": -23.563593,
    "longitude": -46.654527,
    "tipolum": 49,
    "nsetor": 1
}

response = requests.post(
    url="https://url/incluir-simuc/v1",
    headers=headers,
    json=simuc_data
)

print(response.status_code)  # 200
print(response.json())
```

##### Parâmetros do Body (Inclusão SEM Etiqueta):
| Identificador | Tipo     | Obrigatório | Descrição                                                    | 
|---------------|----------|:-----------:|:-------------------------------------------------------------| 
| simcon        | `int`    | **Sim**     | ID do concentrador SIMCON                                    |
| nserlum       | `int`    | **Sim**     | Número serial do SIMUC/SIMUC                             |
| latitude      | `float`  | **Sim**     | Latitude da localização                                      |
| longitude     | `float`  | **Sim**     | Longitude da localização                                     |
| tipolum       | `int`    | **Sim**     | Tipo de luminária (geralmente 49)                           |
| nsetor        | `int`    | **Sim**     | Número do setor                                              |

### Exemplo do Resultado (SEM Etiqueta):
```json
{
    "data": {
        "success": true,
        "status": "sucesso",
        "message": "✅ SIMUC incluído com sucesso",
        "codigo": 2,
        "simcon": 2525025,
        "nserlum": 2000000002,
        "processed_at": "2024-01-15T10:30:00Z"
    },
    "success": true,
    "elapsed_time": "2.5s"
}
```

## 📊 Dicionário de Resultados

### Resposta de Inclusão
##### Bloco PRINCIPAL:
| Identificador | Tipo     | Descrição                                                           | 
|:------------- |----------|:--------------------------------------------------------------------| 
| data          | `object` | Resultado da inclusão do SIMUC                                     | 
| success       | `bool`   | Status de sucesso/falha da interação                                | 
| elapsed_time  | `string` | Tempo de duração da execução                                        | 

##### Bloco DATA:
| Identificador     | Tipo     | Descrição                                          | 
|:------------------|----------|:---------------------------------------------------| 
| success           | `bool`   | Indica se o SIMUC foi incluído com sucesso         | 
| status            | `string` | Status da operação (sucesso/erro)                  | 
| message           | `string` | Mensagem descritiva do resultado                   | 
| codigo            | `int`    | Código de resposta da operação                     | 
| simcon            | `int`    | ID do concentrador SIMCON                          | 
| nserlum           | `int`    | Número serial da luminária incluída                | 
| processed_at      | `string` | Timestamp do processamento                         | 
| etiqueta_status   | `string` | Status do processamento de etiqueta (opcional)     | 

## 🎯 Lógica de Etiquetas Implementada

### Campos Opcionais
- `etiqueta` (string) - Identificador da etiqueta
- `sub` (string) - Subprefeitura ou região  
- `obs` (string) - Observações adicionais

### Comportamento Inteligente
1. **Nenhum campo opcional fornecido** → Inclusão normal (só SIMUC)
2. **Algum campo opcional fornecido** → Inclusão + processamento de etiqueta

### Campo de Retorno
- `etiqueta_status` aparece na resposta quando há processamento de etiqueta
- Indica se a etiqueta foi inserida com sucesso ou houve algum problema

## 📊 Códigos de Resposta

### Códigos de Sucesso
| Código | Descrição                                          |
|:-------|:---------------------------------------------------|
| 2      | ✅ SIMUC incluído com sucesso                      |
| 3      | ✅ SIMUC incluído com sucesso (com etiqueta)       |

### Status de Operação
| Status    | Descrição                                    |
|:----------|:---------------------------------------------|
| `sucesso` | Operação executada com sucesso               |
| `erro`    | Falha na execução da operação                |

### Possíveis Mensagens de Etiqueta
| Mensagem                              | Descrição                                    |
|:--------------------------------------|:---------------------------------------------|
| `Etiqueta cadastrada com sucesso`     | Etiqueta processada e inserida              |
| `SIMUC já existe na base`  | Número serial já cadastrado                  |
| `Etiqueta já existe na base` | Etiqueta já cadastrada                       |
| `Erro no processamento da etiqueta`   | Falha geral no processamento                 |

## ⚙️ Configurações e Validações

### Validações Obrigatórias
- Todos os campos obrigatórios devem ser fornecidos
- Formato correto de coordenadas (latitude/longitude)
- IDs numéricos válidos
- SIMCON deve existir no sistema

### Validações de Etiqueta
- Verificação de duplicatas antes da inserção
- Validação de formato da etiqueta
- Consistência com dados existentes

### Performance
- Processamento otimizado em duas etapas
- Rollback automático em caso de falha na etiqueta
- Logs detalhados para auditoria

## 🛡️ Segurança

- JWT obrigatório para o endpoint
- Validação de permissões por usuário
- Audit logs mantidos para todas as inclusões
- Sanitização de dados de entrada

## ❌ Códigos de Erro

### Erros HTTP Comuns
| Código | Descrição                                    |
|:-------|:---------------------------------------------|
| 400    | Parâmetros inválidos ou malformados         |
| 401    | Token de autenticação inválido ou expirado  |
| 403    | Usuário sem permissão para inclusão         |
| 422    | Erro de validação dos dados                  |
| 500    | Erro interno do servidor                     |

### Exemplos de Erro
```json
{
    "error": "Parâmetros inválidos",
    "message": "Campo 'nserlum' é obrigatório",
    "success": false,
    "elapsed_time": "0.001s"
}
```

```json
{
    "data": {
        "success": false,
        "status": "erro",
        "message": "SIMCON não encontrado",
        "codigo": 14,
        "simcon": 9999999,
        "processed_at": "2024-01-15T10:30:00Z"
    },
    "success": false,
    "elapsed_time": "1.2s"
}
```

## 📈 Vantagens da Implementação

1. **Compatibilidade**: Funciona como inclusão normal se não usar etiquetas
2. **Flexibilidade**: Campos opcionais permitem uso gradual da funcionalidade
3. **Inteligência**: Sistema de sufixos automáticos evita conflitos
4. **Eficiência**: Reutiliza dados existentes para otimizar processo
5. **Segurança**: Verificações de duplicata evitam inconsistências
6. **Auditoria**: Logs completos para rastreabilidade

## Nota Importante

- O envio do token de acesso é obrigatório para este endpoint
- Campos opcionais (`etiqueta`, `sub`, `obs`) ativam automaticamente o processamento de etiquetas
- O sistema detecta automaticamente se deve processar etiquetas
- A inclusão do SIMUC sempre acontece primeiro; etiquetas são processadas apenas em caso de sucesso
- Em caso de falha no processamento da etiqueta, o SIMUC permanece incluído
- Use este endpoint para incluir novos SIMUCs no sistema com ou sem etiquetas
