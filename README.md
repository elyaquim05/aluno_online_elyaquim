# 🎓 Aluno Online API

API RESTful para gerenciamento de uma plataforma educacional, cobrindo o ciclo completo de **alunos**, **professores**, **disciplinas** e **matrículas** com cálculo automático de aprovação/reprovação.

Desenvolvida com **Spring Boot 4**, **Spring Data JPA** e **PostgreSQL**, seguindo a arquitetura em camadas clássica do ecossistema Spring.

---

## 📋 Índice

- [Sobre o Projeto](#sobre-o-projeto)
- [Tecnologias Utilizadas](#tecnologias-utilizadas)
- [Arquitetura do Projeto](#arquitetura-do-projeto)
- [Estrutura de Diretórios](#estrutura-de-diretórios)
- [Modelo de Dados](#modelo-de-dados)
  - [Diagrama de Entidades](#diagrama-de-entidades)
  - [Entidade: Aluno](#entidade-aluno)
  - [Entidade: Professor](#entidade-professor)
  - [Entidade: Disciplina](#entidade-disciplina)
  - [Entidade: MatriculaAluno](#entidade-matriculaaluno)
  - [Enum: MatriculaAlunoStatusEnum](#enum-matriculaalostatusenum)
- [Endpoints da API](#endpoints-da-api)
  - [Alunos `/alunos`](#alunos-alunos)
  - [Professores `/professores`](#professores-professores)
  - [Disciplinas `/disciplinas`](#disciplinas-disciplinas)
  - [Matrículas `/matriculas`](#matrículas-matriculas)
- [Regras de Negócio](#regras-de-negócio)
- [Configuração do Banco de Dados](#configuração-do-banco-de-dados)
- [Pré-requisitos](#pré-requisitos)
- [Como Executar](#como-executar)

---

## Sobre o Projeto

O **Aluno Online** é uma API backend que simula o core de um sistema acadêmico. Permite cadastrar alunos e professores, criar disciplinas vinculadas a professores, matricular alunos em disciplinas e gerenciar o ciclo de vida dessas matrículas — incluindo lançamento de notas com cálculo automático de média e definição de status (APROVADO/REPROVADO), além de trancamento e destrancamento de matrícula.

---

## Tecnologias Utilizadas

| Tecnologia | Versão | Papel |
|---|---|---|
| Java | 21 | Linguagem principal |
| Spring Boot | 4.0.3 | Framework da aplicação |
| Spring Web MVC | — | Camada REST (controllers, rotas, status HTTP) |
| Spring Data JPA | — | Abstração da camada de persistência |
| Hibernate | — | Implementação JPA / ORM |
| PostgreSQL | — | Banco de dados relacional |
| Lombok | — | Geração de código boilerplate (getters, setters, construtores) |
| Maven | — | Gerenciamento de build e dependências |

---

## Arquitetura do Projeto

O projeto segue a arquitetura em **3 camadas** padrão Spring:

```
Requisição HTTP
       │
       ▼
┌──────────────┐
│  Controller  │   Recebe a requisição, valida o path/método, retorna status HTTP
└──────┬───────┘
       │
       ▼
┌──────────────┐
│   Service    │   Contém toda a lógica de negócio (cálculo de média, validação de status)
└──────┬───────┘
       │
       ▼
┌──────────────┐
│  Repository  │   Interface JpaRepository — acessa o banco de dados via Spring Data
└──────┬───────┘
       │
       ▼
┌──────────────┐
│  PostgreSQL  │   Persiste os dados nas tabelas geradas automaticamente pelo Hibernate
└──────────────┘
```

**DTO** (`AtualizarNotasRequestDTO`): usado no endpoint de atualização de notas para receber apenas os campos `nota1` e `nota2`, implementando um PATCH parcial — só sobrescreve os campos que chegarem preenchidos.

---

## Estrutura de Diretórios

```
aluno_online_elyaquim/
├── Prints Insomnia e Dbeaver/   # Screenshots de testes e banco de dados (16 imagens)
└── api/
    ├── pom.xml                  # Dependências e configuração Maven
    ├── mvnw / mvnw.cmd          # Maven Wrapper (execução sem Maven instalado globalmente)
    └── src/
        ├── main/
        │   ├── java/br/com/alunoonline/api/
        │   │   ├── AlunoOnlineApplication.java         # Entry point da aplicação
        │   │   ├── MatriculaAlunoStatusEnum.java        # Enum de status da matrícula
        │   │   ├── controller/
        │   │   │   ├── AlunoController.java             # Endpoints de Aluno
        │   │   │   ├── ProfessorController.java         # Endpoints de Professor
        │   │   │   ├── DisciplinaController.java        # Endpoints de Disciplina
        │   │   │   └── MatriculaAlunoController.java    # Endpoints de Matrícula
        │   │   ├── dtos/
        │   │   │   └── AtualizarNotasRequestDTO.java    # DTO para PATCH de notas
        │   │   ├── model/
        │   │   │   ├── Aluno.java
        │   │   │   ├── Professor.java
        │   │   │   ├── Disciplina.java
        │   │   │   └── MatriculaAluno.java
        │   │   ├── repository/
        │   │   │   ├── AlunoRepository.java
        │   │   │   ├── ProfessorRepository.java
        │   │   │   ├── DisciplinaRepository.java
        │   │   │   └── MatriculaAlunoRepository.java    # + query findByAlunoId
        │   │   └── service/
        │   │       ├── AlunoService.java
        │   │       ├── ProfessorService.java
        │   │       ├── DisciplinaService.java
        │   │       └── MatriculaAlunoService.java       # Lógica de notas e status
        │   └── resources/
        │       └── application.properties               # Configurações da aplicação
        └── test/
            └── java/br/com/alunoonline/api/
                └── AlunoOnlineApplicationTests.java
```

---

## Modelo de Dados

### Diagrama de Entidades

```
┌─────────────┐        ┌───────────────┐        ┌─────────────────────┐
│   Professor │        │   Disciplina  │        │   MatriculaAluno    │
│─────────────│        │───────────────│        │─────────────────────│
│ id (PK)     │◄───────│ id (PK)       │◄───────│ id (PK)             │
│ nome        │  1:N   │ nome          │  1:N   │ aluno_id (FK)       │
│ email       │        │ cargaHoraria  │        │ disciplina_id (FK)  │
│ cpf         │        │ professor_id  │        │ nota1               │
└─────────────┘        │   (FK)        │        │ nota2               │
                       └───────────────┘        │ status (ENUM)       │
                                                 └─────────────────────┘
                                                          ▲
┌─────────────┐                                           │ N:1
│    Aluno    │                                           │
│─────────────│───────────────────────────────────────────┘
│ id (PK)     │  1:N
│ nome        │
│ email       │
│ cpf         │
└─────────────┘
```

---

### Entidade: Aluno

Tabela: `aluno`

| Campo | Tipo Java | Tipo SQL | Descrição |
|---|---|---|---|
| `id` | `Long` | `BIGINT` (PK, auto) | Identificador único |
| `nome` | `String` | `VARCHAR` | Nome completo do aluno |
| `email` | `String` | `VARCHAR` | E-mail do aluno |
| `cpf` | `String` | `VARCHAR` | CPF do aluno |

---

### Entidade: Professor

Tabela: `professor`

| Campo | Tipo Java | Tipo SQL | Descrição |
|---|---|---|---|
| `id` | `Long` | `BIGINT` (PK, auto) | Identificador único |
| `nome` | `String` | `VARCHAR` | Nome completo do professor |
| `email` | `String` | `VARCHAR` | E-mail do professor |
| `cpf` | `String` | `VARCHAR` | CPF do professor |

---

### Entidade: Disciplina

Tabela: `disciplina`

| Campo | Tipo Java | Tipo SQL | Descrição |
|---|---|---|---|
| `id` | `Long` | `BIGINT` (PK, auto) | Identificador único |
| `nome` | `String` | `VARCHAR` | Nome da disciplina |
| `cargaHoraria` | `Integer` | `INTEGER` | Carga horária em horas |
| `professor` | `Professor` | `BIGINT` (FK) | Professor responsável (`@ManyToOne`) |

---

### Entidade: MatriculaAluno

Tabela: `matricula_aluno`

| Campo | Tipo Java | Tipo SQL | Descrição |
|---|---|---|---|
| `id` | `Long` | `BIGINT` (PK, auto) | Identificador único |
| `aluno` | `Aluno` | `BIGINT` (FK) | Aluno matriculado (`@ManyToOne`) |
| `disciplina` | `Disciplina` | `BIGINT` (FK) | Disciplina da matrícula (`@ManyToOne`) |
| `nota1` | `Double` | `FLOAT8` | Primeira nota (pode ser nula) |
| `nota2` | `Double` | `FLOAT8` | Segunda nota (pode ser nula) |
| `status` | `MatriculaAlunoStatusEnum` | `VARCHAR` | Status atual da matrícula |

---

### Enum: MatriculaAlunoStatusEnum

| Valor | Descrição |
|---|---|
| `MATRICULADO` | Estado inicial ao criar a matrícula |
| `APROVADO` | Média das notas ≥ 7.0 |
| `REPROVADO` | Média das notas < 7.0 |
| `TRANCADO` | Matrícula trancada manualmente |
| `DESLIGADO` | Aluno desligado da disciplina |

---

## Endpoints da API

Base URL: `http://localhost:8080`

---

### Alunos `/alunos`

#### `POST /alunos` — Criar aluno
- **Status:** `201 Created`
- **Body:**
```json
{
  "nome": "João da Silva",
  "email": "joao@email.com",
  "cpf": "123.456.789-00"
}
```

---

#### `GET /alunos` — Listar todos os alunos
- **Status:** `200 OK`
- **Response:**
```json
[
  { "id": 1, "nome": "João da Silva", "email": "joao@email.com", "cpf": "123.456.789-00" }
]
```

---

#### `GET /alunos/{id}` — Buscar aluno por ID
- **Status:** `200 OK`
- **Parâmetro de URL:** `id` (Long)

---

#### `PUT /alunos/{id}` — Atualizar aluno
- **Status:** `204 No Content`
- **Parâmetro de URL:** `id` (Long)
- **Body:** mesmo formato do POST

---

#### `DELETE /alunos/{id}` — Deletar aluno
- **Status:** `204 No Content`
- **Parâmetro de URL:** `id` (Long)

---

### Professores `/professores`

#### `POST /professores` — Criar professor
- **Status:** `201 Created`
- **Body:**
```json
{
  "nome": "Maria Oliveira",
  "email": "maria@email.com",
  "cpf": "987.654.321-00"
}
```

---

#### `GET /professores` — Listar todos os professores
- **Status:** `200 OK`

---

#### `GET /professores/{id}` — Buscar professor por ID
- **Status:** `200 OK`

---

#### `PUT /professores/{id}` — Atualizar professor
- **Status:** `204 No Content`

---

#### `DELETE /professores/{id}` — Deletar professor
- **Status:** `204 No Content`

---

### Disciplinas `/disciplinas`

#### `POST /disciplinas` — Criar disciplina
- **Status:** `201 Created`
- **Body:**
```json
{
  "nome": "Banco de Dados",
  "cargaHoraria": 60,
  "professor": { "id": 1 }
}
```

---

#### `GET /disciplinas` — Listar todas as disciplinas
- **Status:** `200 OK`
- **Response:**
```json
[
  {
    "id": 1,
    "nome": "Banco de Dados",
    "cargaHoraria": 60,
    "professor": {
      "id": 1,
      "nome": "Maria Oliveira",
      "email": "maria@email.com",
      "cpf": "987.654.321-00"
    }
  }
]
```

---

#### `GET /disciplinas/{id}` — Buscar disciplina por ID
- **Status:** `200 OK`

---

#### `PUT /disciplinas/{id}` — Atualizar disciplina
- **Status:** `204 No Content`

---

#### `DELETE /disciplinas/{id}` — Deletar disciplina
- **Status:** `204 No Content`

---

### Matrículas `/matriculas`

#### `POST /matriculas` — Criar matrícula
Matricula um aluno em uma disciplina. O status é definido automaticamente como `MATRICULADO`.

- **Status:** `201 Created`
- **Body:**
```json
{
  "aluno": { "id": 1 },
  "disciplina": { "id": 1 }
}
```

---

#### `PATCH /matriculas/atualizar-notas/{id}` — Atualizar notas
Lança nota1 e/ou nota2 de uma matrícula. Aceita atualização parcial (apenas uma nota por vez). Quando ambas as notas estiverem preenchidas, o status é calculado automaticamente:

- Média ≥ 7.0 → `APROVADO`
- Média < 7.0 → `REPROVADO`

- **Status:** `204 No Content`
- **Parâmetro de URL:** `id` (Long) — ID da matrícula
- **Body:**
```json
{
  "nota1": 8.5,
  "nota2": 7.0
}
```
> Os campos são opcionais. Enviar apenas `nota1` atualiza somente esse campo.

---

#### `PATCH /matriculas/trancar/{id}` — Trancar matrícula
Só é permitido trancar uma matrícula com status `MATRICULADO`. Caso contrário, retorna `400 Bad Request`.

- **Status:** `204 No Content`
- **Parâmetro de URL:** `id` (Long)

---

#### `PATCH /matriculas/destrancar/{id}` — Destrancar matrícula
Só é permitido destrancar uma matrícula com status `TRANCADO`. Caso contrário, retorna `400 Bad Request`.

- **Status:** `204 No Content`
- **Parâmetro de URL:** `id` (Long)

---

## Regras de Negócio

**Cálculo de aprovação:**
```
média = (nota1 + nota2) / 2
média >= 7.0  →  status = APROVADO
média <  7.0  →  status = REPROVADO
```

**Trancamento:**
- Apenas matrículas com status `MATRICULADO` podem ser trancadas.
- Matrículas com status `TRANCADO`, `APROVADO`, `REPROVADO` ou `DESLIGADO` retornam `400 Bad Request` ao tentar trancar.

**Destrancamento:**
- Apenas matrículas com status `TRANCADO` podem ser desligadas de volta para `MATRICULADO`.

**Status inicial:**
- Toda matrícula criada recebe o status `MATRICULADO` automaticamente, independente do payload enviado.

**PATCH parcial de notas:**
- Campos `null` no DTO não sobrescrevem os valores já salvos, permitindo lançar as notas em momentos distintos.

---

## Configuração do Banco de Dados

As configurações ficam em `src/main/resources/application.properties`:

```properties
# Nome da aplicação
spring.application.name=Aluno Online

# Conexão com o Banco de Dados PostgreSQL
spring.datasource.url=jdbc:postgresql://localhost:5432/aluno_online
spring.datasource.username=postgres
spring.datasource.password=sua_senha
spring.datasource.driver-class-name=org.postgresql.Driver

# Hibernate — cria/atualiza tabelas automaticamente
spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

server.port=8080
```

> ⚠️ **Antes de executar**, crie o banco e ajuste as credenciais conforme seu ambiente:

```sql
CREATE DATABASE aluno_online;
```

O Hibernate criará as tabelas automaticamente (`ddl-auto=update`) ao iniciar a aplicação.

---

## Pré-requisitos

- [Java 21+](https://www.oracle.com/java/technologies/downloads/)
- [PostgreSQL 14+](https://www.postgresql.org/download/)
- [Maven 3.8+](https://maven.apache.org/download.cgi) — ou usar o Maven Wrapper incluído (`./mvnw`)
- Ferramenta para testar a API: [Insomnia](https://insomnia.rest/), [Postman](https://www.postman.com/) ou `curl`

---

## Como Executar

**1. Clone o repositório:**
```bash
git clone https://github.com/elyaquim05/aluno_online_elyaquim.git
cd aluno_online_elyaquim/api
```

**2. Configure o banco de dados:**

Crie o banco no PostgreSQL e edite as credenciais em `src/main/resources/application.properties`.

**3. Execute a aplicação:**

```bash
# Com Maven Wrapper (sem precisar do Maven instalado)
./mvnw spring-boot:run

# Ou com Maven instalado globalmente
mvn spring-boot:run
```

**4. Acesse a API:**

```
http://localhost:8080
```

**Exemplos com `curl`:**

```bash
# Criar um professor
curl -X POST http://localhost:8080/professores \
  -H "Content-Type: application/json" \
  -d '{"nome":"Maria Oliveira","email":"maria@email.com","cpf":"987.654.321-00"}'

# Criar uma disciplina vinculada ao professor 1
curl -X POST http://localhost:8080/disciplinas \
  -H "Content-Type: application/json" \
  -d '{"nome":"Banco de Dados","cargaHoraria":60,"professor":{"id":1}}'

# Criar um aluno
curl -X POST http://localhost:8080/alunos \
  -H "Content-Type: application/json" \
  -d '{"nome":"João Silva","email":"joao@email.com","cpf":"123.456.789-00"}'

# Matricular o aluno 1 na disciplina 1
curl -X POST http://localhost:8080/matriculas \
  -H "Content-Type: application/json" \
  -d '{"aluno":{"id":1},"disciplina":{"id":1}}'

# Lançar notas na matrícula 1
curl -X PATCH http://localhost:8080/matriculas/atualizar-notas/1 \
  -H "Content-Type: application/json" \
  -d '{"nota1":8.0,"nota2":9.0}'

# Trancar matrícula 1
curl -X PATCH http://localhost:8080/matriculas/trancar/1
```

---

## Autor

Desenvolvido por **Elyaquim** — [@elyaquim05](https://github.com/elyaquim05)
