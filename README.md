# Auth API

API REST de autenticação e autorização, construída em NestJS. O projeto implementa cadastro de usuários, login, autenticação via JWT com refresh token, logout com revogação e controle de acesso por roles (admin/user).

## Stack utilizada

- NestJS (Node.js + TypeScript)
- PostgreSQL
- Prisma ORM
- Passport + JWT
- bcrypt (hash de senha e de refresh token)
- Docker (banco de dados local)

## Funcionalidades

- Cadastro de usuário, com senha armazenada via hash (bcrypt)
- Validação de dados de entrada (DTOs com class-validator)
- Prevenção de cadastro duplicado por email
- Login com geração de access token (JWT) e refresh token
- Refresh token com rotação: cada renovação gera um novo par de tokens e invalida o anterior
- Logout com revogação real do refresh token no banco de dados
- Rotas protegidas por autenticação (JWT)
- Controle de acesso por role (admin/user), via Guards e decorator customizado

## Arquitetura

O projeto é dividido em três módulos principais:

- `prisma/` - gerencia a conexão com o banco de dados
- `users/` - cadastro e listagem de usuários
- `auth/` - autenticação, geração e validação de tokens, autorização por role

A autenticação segue o padrão Strategy do Passport, com três estratégias distintas: login por email/senha, validação de access token e validação de refresh token.

## Como rodar o projeto localmente

### Pré-requisitos

- Node.js
- Docker

### Passos

1. Clone o repositório
```bash
git clone <url-do-repositorio>
cd auth-api
```

2. Instale as dependências
```bash
npm install
```

3. Configure as variáveis de ambiente

Crie um arquivo `.env` na raiz do projeto com as seguintes variáveis:
```
DATABASE_URL="postgresql://postgres:postgres@localhost:5433/auth_api"
JWT_SECRET=<chave aleatória>
JWT_REFRESH_SECRET=<outra chave aleatória, diferente da anterior>
```

4. Suba o banco de dados
```bash
docker compose up -d
```

5. Rode as migrations
```bash
npx prisma migrate dev
```

6. Inicie a aplicação
```bash
npm run start:dev
```

A API estará disponível em `http://localhost:3000`.

## Rotas disponíveis

| Método | Rota | Descrição | Autenticação |
|---|---|---|---|
| POST | /users | Cadastra um novo usuário | Não |
| GET | /users/me | Retorna os dados do usuário autenticado | Access token |
| GET | /users | Lista todos os usuários | Access token + role admin |
| POST | /auth/login | Autentica e retorna access token e refresh token | Não |
| POST | /auth/refresh | Gera um novo par de tokens a partir do refresh token | Refresh token |
| POST | /auth/logout | Revoga o refresh token do usuário | Access token |

## Próximos passos

- Testes automatizados (unitários e end-to-end)
- Documentação via Swagger
- Deploy da aplicação
- Frontend em React/Next.js consumindo esta API
