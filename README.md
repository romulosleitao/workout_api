# FastAPI
### Quem é o FastAPi?
Framework FastAPI, alta performance, fácil de aprender, fácil de codar, pronto para produção.
FastAPI é um moderno e rápido (alta performance) framework web para construção de APIs com Python 3.6 ou superior, baseado nos type hints padrões do Python.

### Async
Código assíncrono apenas significa que a linguagem tem um jeito de dizer para o computador / programa que em certo ponto, ele terá que esperar por algo para finalizar em outro lugar

# Projeto
## WorkoutAPI

Esta é uma API de competição de crossfit chamada WorkoutAPI (isso mesmo rs, eu acabei unificando duas coisas que gosto: codar e treinar). É uma API pequena, devido a ser um projeto mais hands-on e simplificado nós desenvolveremos uma API de poucas tabelas, mas com o necessário para você aprender como utilizar o FastAPI.

## Modelagem de entidade e relacionamento - MER
![MER](/mer.jpg "Modelagem de entidade e relacionamento")

## Stack da API

A API foi desenvolvida utilizando o `fastapi` (async), junto das seguintes libs: `alembic`, `SQLAlchemy`, `pydantic`. Para salvar os dados está sendo utilizando o `postgres`, por meio do `docker`.

## Execução da API

Para executar o projeto, utilizei a [pyenv](https://github.com/pyenv/pyenv), com a versão 3.11.4 do `python` para o ambiente virtual.

Caso opte por usar pyenv, após instalar, execute:

```bash
pyenv virtualenv 3.11.4 workoutapi
pyenv activate workoutapi
pip install -r requirements.txt
```
Para subir o banco de dados, caso não tenha o [docker-compose](https://docs.docker.com/compose/install/linux/) instalado, faça a instalação e logo em seguida, execute:

```bash
make run-docker
```
Para criar uma migration nova, execute:

```bash
make create-migrations d="nome_da_migration"
```

Para criar o banco de dados, execute:

```bash
make run-migrations
```

## API

Para subir a API, execute:
```bash
make run
```
e acesse: http://127.0.0.1:8000/docs

# Desafio Final
    - adicionar query parameters nos endpoints
        - atleta
            - nome
            - cpf
    - customizar response de retorno de endpoints
        - get all
            - atleta
                - nome
                - centro_treinamento
                - categoria
    - Manipular exceção de integridade dos dados em cada módulo/tabela
        - sqlalchemy.exc.IntegrityError e devolver a seguinte mensagem: “Já existe um atleta cadastrado com o cpf: x”
        - status_code: 303
    - Adicionar paginação utilizando a lib: fastapi-pagination
        - limit e offset

## 🛠️ Resolução do Desafio: Modificações Implementadas

Este documento detalha as alterações realizadas no projeto base para atender aos requisitos propostos no desafio da API de Atletismo:

### 1. Adição de Query Parameters nos Endpoints
* **O que foi pedido:** Permitir a filtragem de atletas no endpoint de listagem (`GET /atletas`) utilizando parâmetros de busca.
* **Como foi feito:** Foram adicionados os parâmetros `nome` e `cpf` utilizando o `Query` do FastAPI no controller de atletas, aplicando filtros dinâmicos na query do SQLAlchemy (`ilike` para aproximação de nome e igualdade para CPF).

### 2. Customização do Response (Get All)
* **O que foi pedido:** Ajustar o retorno da listagem geral de atletas para conter apenas campos específicos.
* **Como foi feito:** Criado um novo schema Pydantic (`AtletaResponse`) restrito aos campos obrigatórios exigidos:
  - `nome`
  - `centro_treinamento`
  - `categoria`
  E configurado o `response_model` do endpoint GET correspondente.

### 3. Tratamento de Exceção de Integridade dos Dados
* **O que foi pedido:** Tratar o erro de chave duplicada do banco (`sqlalchemy.exc.IntegrityError`) na inserção e retornar uma mensagem específica com status HTTP 303.
* **Como foi feito:** Adicionado um bloco `try/except IntegrityError` na rota de criação (`POST`), fazendo o `rollback` da sessão e lançando uma `HTTPException` com o `status_code=303` e a mensagem: 
  > *“Já existe um atleta cadastrado com o cpf: [cpf]”*

### 4. Implementação de Paginação
* **O que foi pedido:** Utilizar a biblioteca `fastapi-pagination` para gerenciar os dados por meio de limite e deslocamento (`limit` e `offset`).
* **Como foi feito:** 
  - Inicializada a biblioteca globalmente no `main.py` com `add_pagination(app)`.
  - Aplicada a função `paginate` integrada com o `fastapi-pagination.ext.sqlmodel` no endpoint de listagem, alterando o tipo de retorno para o modelo `Page[AtletaResponse]`.


        
# Referências

FastAPI: https://fastapi.tiangolo.com/

Pydantic: https://docs.pydantic.dev/latest/

SQLAlchemy: https://docs.sqlalchemy.org/en/20/

Alembic: https://alembic.sqlalchemy.org/en/latest/

Fastapi-pagination: https://uriyyo-fastapi-pagination.netlify.app/
