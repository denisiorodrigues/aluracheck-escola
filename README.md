# API de Cursos e Matrículas

API REST para gerenciamento de cursos, estudantes e matrículas, com autenticação via JWT e controle de acesso por papéis (`Admin`, `Instructor`, `Student`).

> Requisitos completos do projeto em [`docs/requisitos.md`](docs/requisitos.md). Fluxo de contribuição (branches, commits, PRs) em [`CONTRIBUTING.md`](CONTRIBUTING.md).

## Stack

- .NET 10 + ASP.NET Core Web API
- EF Core (SQLite em dev; SQL Server/PostgreSQL em produção)
- ASP.NET Core Identity + JWT Bearer
- Swagger / OpenAPI

## Pré-requisitos

- [.NET 10 SDK](https://dotnet.microsoft.com/download)
- (Opcional) SQL Server ou PostgreSQL, se não for usar SQLite

## Como rodar o projeto

1. Clone o repositório:
   ```bash
   git clone https://github.com/<seu-usuario>/<seu-repo>.git
   cd <seu-repo>
   ```

2. Configure os segredos locais (nunca commitar segredos no repositório):
   ```bash
   dotnet user-secrets init
   dotnet user-secrets set "Jwt:Key" "sua-chave-secreta-com-pelo-menos-32-caracteres"
   dotnet user-secrets set "Jwt:Issuer" "api-cursos"
   dotnet user-secrets set "Jwt:Audience" "api-cursos-clients"
   dotnet user-secrets set "ConnectionStrings:DefaultConnection" "Data Source=app.db"
   ```

   Em produção, use variáveis de ambiente equivalentes (ex.: `Jwt__Key`, `ConnectionStrings__DefaultConnection`).

3. Aplique as migrations (cria o banco e já roda o seed de papéis + usuário admin):
   ```bash
   dotnet ef database update
   ```

4. Rode a aplicação:
   ```bash
   dotnet run --project src/Api
   ```

5. Acesse o Swagger:
   ```
   https://localhost:5001/swagger
   ```

## Seed inicial

Ao aplicar as migrations, o seed cria (de forma idempotente — pode rodar várias vezes sem duplicar):

- Papéis: `Admin`, `Instructor`, `Student`
- Usuário admin padrão:
  - **E-mail:** `admin@example.com`
  - **Senha:** `Admin@123` *(altere após o primeiro login em qualquer ambiente que não seja local)*

## Como autenticar

1. **Login** para obter o token:
   ```http
   POST /api/auth/login
   Content-Type: application/json

   {
     "email": "admin@example.com",
     "password": "Admin@123"
   }
   ```

   Resposta:
   ```json
   {
     "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
   }
   ```

2. **Use o token** nas rotas protegidas, no header `Authorization`:
   ```
   Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
   ```

3. **Pelo Swagger**: clique em `Authorize` (ícone de cadeado no topo), cole `Bearer <token>` e todas as chamadas autenticadas passam a incluir o header automaticamente.

## Papéis e permissões (resumo)

| Ação | Admin | Instructor | Student | Público |
|---|---|---|---|---|
| Listar/detalhar cursos | ✅ | ✅ | ✅ | ✅ |
| Criar/atualizar curso | ✅ | ✅ | ❌ | ❌ |
| Remover curso | ✅ | ❌ | ❌ | ❌ |
| Criar/listar estudantes | ✅ | ❌ | ❌ | ❌ |
| Ver/editar o próprio perfil de estudante | ✅ | ❌ | ✅ (só o próprio) | ❌ |
| Matricular-se em curso | ✅ | ❌ | ✅ | ❌ |
| Listar matrículas | ✅ (todas) | ❌ | ✅ (só as próprias) | ❌ |

## Como rodar os testes

```bash
dotnet test
```

## Paginação e filtros

Endpoints de listagem (ex.: `GET /api/courses`) aceitam query string:

```
GET /api/courses?page=1&pageSize=10&title=introducao
```

Parâmetros documentados no Swagger de cada endpoint.

## Tratamento de erros

Erros seguem um formato padronizado:

```json
{
  "status": 400,
  "message": "O título do curso deve ter no mínimo 3 caracteres."
}
```

## Estrutura do projeto

```
.
├── src/
│   └── Api/                # Projeto ASP.NET Core Web API
├── tests/                  # Testes automatizados
├── docs/
│   └── requisitos.md       # Requisitos funcionais e técnicos
├── CONTRIBUTING.md         # Fluxo de branches, commits e PRs
└── README.md
```

## Contribuindo

Veja [`CONTRIBUTING.md`](CONTRIBUTING.md) para o fluxo de branches (`main` + `feature/*`), padrão de commits (Conventional Commits) e como abrir Pull Requests.