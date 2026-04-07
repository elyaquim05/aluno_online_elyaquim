# 🎓 Aluno Online API

API RESTful para gerenciamento de alunos e professores de uma instituição de ensino, desenvolvida com **Spring Boot**, **PostgreSQL** e seguindo a arquitetura em camadas (Controller → Service → Repository).

---

## 📋 Índice

- [Sobre o Projeto](#sobre-o-projeto)
- [Tecnologias Utilizadas](#tecnologias-utilizadas)
- [Arquitetura do Projeto](#arquitetura-do-projeto)
- [Estrutura de Diretórios](#estrutura-de-diretórios)
- [Modelos de Dados](#modelos-de-dados)
- [Endpoints da API](#endpoints-da-api)
  - [Alunos](#alunos)
  - [Professores](#professores)
- [Configuração do Banco de Dados](#configuração-do-banco-de-dados)
- [Como Executar o Projeto](#como-executar-o-projeto)
- [Pré-requisitos](#pré-requisitos)

---

## Sobre o Projeto

O **Aluno Online** é uma API backend desenvolvida em Java com Spring Boot, criada para gerenciar o cadastro de **alunos** e **professores** de uma plataforma educacional. A API oferece operações completas de CRUD (Create, Read, Update, Delete) para ambas as entidades, com persistência de dados em um banco de dados PostgreSQL.

---

## Tecnologias Utilizadas

| Tecnologia | Versão | Descrição |
|---|---|---|
| Java | 21 | Linguagem principal |
| Spring Boot | 4.0.3 | Framework principal da aplicação |
| Spring Data JPA | — | Camada de persistência e ORM |
| Spring Web MVC | — | Camada de apresentação REST |
| PostgreSQL | — | Banco de dados relacional |
| Lombok | — | Redução de boilerplate (getters, setters, construtores) |
| Hibernate | — | Implementação JPA para mapeamento objeto-relacional |
| Maven | — | Gerenciamento de dependências e build |

---

## Arquitetura do Projeto

O projeto segue a arquitetura em **3 camadas** clássica do Spring:

```
Request HTTP
     │
     ▼
┌─────────────┐
│ Controller  │  ← Recebe requisições HTTP, define rotas e status codes
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   Service   │  ← Contém a lógica de negócio
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ Repository  │  ← Acessa o banco de dados via Spring Data JPA
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ PostgreSQL  │  ← Banco de dados relacional
└─────────────┘
```

---

## Estrutura de Diretórios

```
aluno_online_elyaquim/
└── api/
    ├── pom.xml                          # Configurações Maven e dependências
    ├── mvnw / mvnw.cmd                  # Maven Wrapper
    └── src/
        ├── main/
        │   ├── java/br/com/alunoonline/api/
        │   │   ├── AlunoOnlineApplication.java    # Classe principal (entry point)
        │   │   ├── controller/
        │   │   │   ├── AlunoController.java       # Endpoints REST de Aluno
        │   │   │   └── ProfessorController.java   # Endpoints REST de Professor
        │   │   ├── model/
        │   │   │   ├── Aluno.java                 # Entidade JPA Aluno
        │   │   │   └── Professor.java             # Entidade JPA Professor
        │   │   ├── repository/
        │   │   │   ├── AlunoRepository.java       # Interface JPA de Aluno
        │   │   │   └── ProfessorRepository.java   # Interface JPA de Professor
        │   │   └── service/
        │   │       ├── AlunoService.java          # Regras de negócio de Aluno
        │   │       └── ProfessorService.java      # Regras de negócio de Professor
        │   └── resources/
        │       └── application.properties         # Configurações da aplicação
        └── test/
            └── java/br/com/alunoonline/api/
                └── AlunoOnlineApplicationTests.java
```

---

## Modelos de Dados

### Aluno

Tabela no banco: `aluno`

| Campo | Tipo | Descrição |
|---|---|---|
| `id` | Long (PK) | Identificador único, gerado automaticamente |
| `nome` | String | Nome completo do aluno |
| `email` | String | Endereço de e-mail do aluno |
| `cpf` | String | CPF do aluno |

### Professor

Tabela no banco: `professor`

| Campo | Tipo | Descrição |
|---|---|---|
| `id` | Long (PK) | Identificador único, gerado automaticamente |
| `nome` | String | Nome completo do professor |
| `email` | String | Endereço de e-mail do professor |
| `cpf` | String | CPF do professor |

> As tabelas são criadas/atualizadas automaticamente pelo Hibernate (`ddl-auto=update`) na inicialização da aplicação.

---

## Endpoints da API

A aplicação roda por padrão na porta **8080**.

Base URL: `http://localhost:8080`

---

### Alunos

Base path: `/alunos`

#### `POST /alunos`
Cria um novo aluno.

- **Status de sucesso:** `201 Created`
- **Body (JSON):**
```json
{
  "nome": "João da Silva",
  "email": "joao@email.com",
  "cpf": "123.456.789-00"
}
```

---

#### `GET /alunos`
Retorna a lista de todos os alunos cadastrados.

- **Status de sucesso:** `200 OK`
- **Response (JSON):**
```json
[
  {
    "id": 1,
    "nome": "João da Silva",
    "email": "joao@email.com",
    "cpf": "123.456.789-00"
  }
]
```

---

#### `GET /alunos/{id}`
Busca um aluno pelo seu ID.

- **Status de sucesso:** `200 OK`
- **Parâmetro:** `id` (Long) — ID do aluno na URL
- **Response (JSON):**
```json
{
  "id": 1,
  "nome": "João da Silva",
  "email": "joao@email.com",
  "cpf": "123.456.789-00"
}
```

---

#### `PUT /alunos/{id}`
Atualiza os dados de um aluno existente pelo ID.

- **Status de sucesso:** `204 No Content`
- **Parâmetro:** `id` (Long) — ID do aluno na URL
- **Body (JSON):**
```json
{
  "nome": "João da Silva Atualizado",
  "email": "joao.novo@email.com",
  "cpf": "123.456.789-00"
}
```

---

#### `DELETE /alunos/{id}`
Remove um aluno pelo seu ID.

- **Status de sucesso:** `204 No Content`
- **Parâmetro:** `id` (Long) — ID do aluno na URL

---

### Professores

Base path: `/professores`

#### `POST /professores`
Cria um novo professor.

- **Status de sucesso:** `201 Created`
- **Body (JSON):**
```json
{
  "nome": "Maria Oliveira",
  "email": "maria@email.com",
  "cpf": "987.654.321-00"
}
```

---

#### `GET /professores`
Retorna a lista de todos os professores cadastrados.

- **Status de sucesso:** `200 OK`
- **Response (JSON):**
```json
[
  {
    "id": 1,
    "nome": "Maria Oliveira",
    "email": "maria@email.com",
    "cpf": "987.654.321-00"
  }
]
```

---

#### `GET /professores/{id}`
Busca um professor pelo seu ID.

- **Status de sucesso:** `200 OK`
- **Parâmetro:** `id` (Long) — ID do professor na URL

---

#### `PUT /professores/{id}`
Atualiza os dados de um professor existente pelo ID.

- **Status de sucesso:** `204 No Content`
- **Parâmetro:** `id` (Long) — ID do professor na URL

---

#### `DELETE /professores/{id}`
Remove um professor pelo seu ID.

- **Status de sucesso:** `204 No Content`
- **Parâmetro:** `id` (Long) — ID do professor na URL

---

## Configuração do Banco de Dados

As configurações de conexão estão no arquivo `src/main/resources/application.properties`:

```properties
# Nome da aplicação
spring.application.name=Aluno Online

# Conexão com o Banco de Dados PostgreSQL
spring.datasource.url=jdbc:postgresql://localhost:5432/aluno_online
spring.datasource.username=postgres
spring.datasource.password=sua_senha
spring.datasource.driver-class-name=org.postgresql.Driver

# Hibernate
spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

server.port=8080
```

> ⚠️ **Atenção:** Antes de rodar o projeto, crie o banco de dados `aluno_online` no PostgreSQL e atualize as credenciais (`username` e `password`) conforme seu ambiente local.

Para criar o banco de dados:
```sql
CREATE DATABASE aluno_online;
```

---

## Pré-requisitos

Certifique-se de ter instalado:

- [Java 21+](https://www.oracle.com/java/technologies/downloads/)
- [Maven 3.8+](https://maven.apache.org/download.cgi) (ou usar o Maven Wrapper incluído `./mvnw`)
- [PostgreSQL 14+](https://www.postgresql.org/download/)

---

## Como Executar o Projeto

**1. Clone o repositório:**
```bash
git clone https://github.com/elyaquim05/aluno_online_elyaquim.git
cd aluno_online_elyaquim/api
```

**2. Configure o banco de dados:**

Crie o banco `aluno_online` no PostgreSQL e ajuste as credenciais em `src/main/resources/application.properties`.

**3. Execute a aplicação:**

Com Maven Wrapper (recomendado):
```bash
./mvnw spring-boot:run
```

Ou com Maven instalado globalmente:
```bash
mvn spring-boot:run
```

**4. Acesse a API:**

A API estará disponível em: `http://localhost:8080`

Você pode testá-la usando [Postman](https://www.postman.com/), [Insomnia](https://insomnia.rest/) ou o comando `curl`:

```bash
# Exemplo: listar todos os alunos
curl -X GET http://localhost:8080/alunos

# Exemplo: criar um aluno
curl -X POST http://localhost:8080/alunos \
  -H "Content-Type: application/json" \
  -d '{"nome": "João Silva", "email": "joao@email.com", "cpf": "123.456.789-00"}'
```

---

## Autor

Desenvolvido por **Elyaquim** — [@elyaquim05](https://github.com/elyaquim05)
