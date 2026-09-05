# GESCOL

Sistema de gereciamento escolar.

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
   git clone [https://github.com/<seu-usuario>/<seu-repo>.git](https://github.com/denisiorodrigues/aluracheck-escola.git)
   cd aluracheck-escola
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



## Proposta para a Futura Arquitetura

```
gescol/
├── client/ # Aplicação React (frontend)
│ ├── src/
│ │ ├── components/ # Componentes reutilizáveis
│ │ ├── pages/ # Páginas da aplicação
│ │ ├── services/ # Comunicação com API
│ │ ├── hooks/ # Custom hooks
│ │ ├── context/ # Context API
│ │ └── utils/ # Funções auxiliares
│ └── public/
│
├── server/ # API .NET (backend)
│ ├── src/
│ │ ├── Gescol.API/ # Projeto principal da API
│ │ │ ├── Controllers/ # Controladores API
│ │ │ ├── Models/ # ViewModels/DTOs
│ │ │ ├── Services/ # Serviços de aplicação
│ │ │ ├── Middleware/ # Middlewares customizados
│ │ │ ├── Filters/ # Filtros de ação
│ │ │ └── Program.cs # Ponto de entrada
│ │ │
│ │ ├── Gescol.Core/ # Camada de domínio
│ │ │ ├── Entities/ # Entidades de domínio
│ │ │ ├── Interfaces/ # Interfaces de repositório
│ │ │ ├── Services/ # Serviços de domínio
│ │ │ └── Specifications/ # Padrão Specification
│ │ │
│ │ ├── Gescol.Infrastructure/ # Camada de infraestrutura
│ │ │ ├── Data/ # Contexto EF e configurações
│ │ │ ├── Repositories/ # Implementações de repositório
│ │ │ ├── Migrations/ # Migrações do banco
│ │ │ └── Identity/ # Configuração de autenticação
│ │ │
│ │ └── Gescol.Tests/ # Projeto de testes
│ │
│ ├── Gescol.sln # Solução .NET
│ └── docker-compose.yml # Docker para PostgreSQL
│
├── shared/ # Código compartilhado (opcional)
│
└── README.md
```

## Gestão
### Requisitos funcionais (o que a API deve fazer)

- [ ] Autenticar usuários (registro/login) e emitir JWT.
- [ ] Controlar acesso por papéis: Admin, Instructor, Student.
- [ ] Cursos: criar (Admin/Instructor), listar com paginação e filtros (público), detalhar (público), - [] atualizar (Admin/Instructor), remover (Admin).
- [ ] Estudantes: criar perfil vinculado ao usuário (Admin), listar (Admin), detalhar/atualizar (Admin ou o próprio), desativar/remover (Admin).
- [ ] Matrículas: matricular estudante autenticado em curso, impedir matrícula duplicada, listar  matrículas do próprio estudante (ou Admin).
- [ ] Validações: título de curso ≥ 3 caracteres; e-mail de estudante válido e único.
- [ ] Erros padronizados com status e mensagem clara.
- [ ] Documentação: Swagger com esquema Bearer e exemplos; README com como rodar/testar/autenticar.

### Requisitos técnicos (como o projeto deve ser construído)

- [ ] .NET 8 + ASP.NET Core Web API (Controllers ou Minimal APIs).
- [ ] EF Core para persistência (SQLite em dev; SQL Server/Postgres em ambientes maiores).
- [ ] ASP.NET Core Identity + JWT Bearer.
- [ ] Configurações por variáveis de ambiente/user-secrets; nenhum segredo no repositório.
- [ ] Migrations aplicadas e seed mínimo (papéis + usuário admin) de forma idempotente.
- [ ] Índices/constraints: e-mail único; unicidade de matrícula (student+course).
- [ ] DTOs separados das entidades; paginação e filtros via query string documentados.
- [ ] Swagger/OpenAPI com Security Scheme Bearer.
- [ ] Repositório GitHub com README de setup/execução.
- [ ] HTTPS habilitado e CORS restrito às origens necessárias.



## Contribuindo

Veja [`CONTRIBUTING.md`](CONTRIBUTING.md) para o fluxo de branches (`main` + `feature/*`), padrão de commits (Conventional Commits) e como abrir Pull Requests.
