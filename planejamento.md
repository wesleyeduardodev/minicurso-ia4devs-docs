# Plano: Aplicação Web do Restaurante Universitário - IFMA

## Contexto

Criar uma aplicação web para o RU do IFMA com duas frentes: uma página pública onde alunos consultam o cardápio do dia, e uma área administrativa para cadastro de cardápios e administradores. Backend em Spring Boot 4.0.3 (Java 21) e frontend em React + Vite + TailwindCSS. Banco PostgreSQL 17 via Docker Compose.

**Ordem de execução: backend completo primeiro, depois frontend.**

---

## PARTE 1: BACKEND (`ru-ifma-backend/`)

### 1.1 — Estrutura do projeto e pom.xml

Criar o projeto Maven com a seguinte estrutura de pacotes: `br.edu.ifma.ru`

**Arquivo:** `ru-ifma-backend/pom.xml`
- Parent: `spring-boot-starter-parent` 4.0.3
- Java 21
- Dependências:
  - `spring-boot-starter-web`
  - `spring-boot-starter-data-jpa`
  - `spring-boot-starter-security`
  - `spring-boot-starter-validation`
  - `org.postgresql:postgresql` (runtime)
  - `spring-boot-starter-test` (test)

**Arquivo:** `ru-ifma-backend/src/main/java/br/edu/ifma/ru/RuIfmaBackendApplication.java`
- Classe main com `@SpringBootApplication`

**Arquivo:** `ru-ifma-backend/.gitignore`
- Ignorar: `target/`, `.idea/`, `*.iml`, `*.class`, `*.jar`, `*.log`

### 1.2 — Docker Compose (só banco de dados)

**Arquivo:** `ru-ifma-backend/docker-compose.yml`
- PostgreSQL 17, container `ru-ifma-db`
- Banco: `ru_ifma`, user: `postgres`, senha: `postgres`
- Porta: `5432:5432`
- Volume nomeado `pgdata` para persistência

### 1.3 — application.properties

**Arquivo:** `ru-ifma-backend/src/main/resources/application.properties`
```properties
server.port=8080
spring.datasource.url=${DB_URL:jdbc:postgresql://localhost:5432/ru_ifma}
spring.datasource.username=${DB_USER:postgres}
spring.datasource.password=${DB_PASSWORD:postgres}
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
spring.jackson.date-format=yyyy-MM-dd
spring.jackson.serialization.write-dates-as-timestamps=false
```

### 1.4 — Entidades (Model)

**`model/TipoRefeicao.java`** — Enum: `ALMOCO`, `JANTAR`

**`model/Cardapio.java`** — Entidade JPA
- Campos: `id` (Long, auto), `data` (LocalDate), `tipoRefeicao` (enum STRING), `pratoPrincipal`, `acompanhamento`, `salada`, `sobremesa`, `suco`
- Constraint única em `(data, tipo_refeicao)` para evitar duplicatas
- Getters/setters explícitos (JPA exige classe mutável, não pode ser Record)

**`model/Administrador.java`** — Entidade JPA
- Campos: `id` (Long, auto), `nome`, `email` (unique), `senha` (BCrypt hash)
- Getters/setters explícitos

### 1.5 — Repositories

**`repository/CardapioRepository.java`** — extends `JpaRepository<Cardapio, Long>`
- `findByDataOrderByTipoRefeicao(LocalDate data)` — cardápios do dia
- `findByDataBetweenOrderByDataAscTipoRefeicaoAsc(LocalDate inicio, LocalDate fim)` — cardápios por período

**`repository/AdministradorRepository.java`** — extends `JpaRepository<Administrador, Long>`
- `findByEmail(String email)` — para autenticação
- `existsByEmail(String email)` — para validação de duplicata

### 1.6 — Records (Request/Response)

Todos em `br.edu.ifma.ru.dto`, usando Java Records:

| Record | Campos |
|--------|--------|
| `CardapioRequest` | data, tipoRefeicao, pratoPrincipal, acompanhamento, salada, sobremesa, suco (com `@NotNull`/`@NotBlank`) |
| `CardapioResponse` | id, data, tipoRefeicao, pratoPrincipal, acompanhamento, salada, sobremesa, suco |
| `AdministradorRequest` | nome, email (`@Email`), senha (`@Size(min=6)`) |
| `AdministradorResponse` | id, nome, email (sem senha!) |
| `DashboardResponse` | totalCardapios, totalAdministradores, cardapiosHoje, cardapiosSemana |

### 1.7 — Services

**`service/CardapioService.java`**
- `listarPorData(LocalDate)` → público
- `listarTodos()` → admin
- `buscarPorId(Long)` → admin
- `criar(CardapioRequest)` → admin
- `atualizar(Long, CardapioRequest)` → admin
- `deletar(Long)` → admin
- Métodos privados `toResponse()` e `toEntity()` para conversão
- Erros com `ResponseStatusException` (404, etc.)

**`service/AdministradorService.java`**
- CRUD completo, mesma estrutura
- Senha sempre codificada com `BCryptPasswordEncoder` antes de salvar
- Validação de email duplicado (409 Conflict)

### 1.8 — Controllers (REST API)

**`controller/CardapioController.java`** — `@RequestMapping("/api/cardapios")`

| Método | Rota | Auth | Descrição |
|--------|------|------|-----------|
| GET | `/api/cardapios/public?data=yyyy-MM-dd` | Não | Cardápios do dia (público) |
| GET | `/api/cardapios` | Sim | Listar todos (admin) |
| GET | `/api/cardapios/{id}` | Sim | Buscar por ID (admin) |
| POST | `/api/cardapios` | Sim | Criar (admin) |
| PUT | `/api/cardapios/{id}` | Sim | Atualizar (admin) |
| DELETE | `/api/cardapios/{id}` | Sim | Excluir (admin) |

**`controller/AdministradorController.java`** — `@RequestMapping("/api/administradores")`
- CRUD completo (GET lista, GET por id, POST, PUT, DELETE) — todos autenticados

**`controller/DashboardController.java`** — `@RequestMapping("/api/dashboard")`
- GET `/api/dashboard` — retorna `DashboardResponse` com estatísticas

### 1.9 — Segurança (Basic Auth)

**`config/SecurityConfig.java`**
- CSRF desabilitado (API REST stateless)
- CORS habilitado via `Customizer.withDefaults()`
- Regras:
  - `GET /api/cardapios/public/**` → `permitAll()`
  - `/api/**` → `authenticated()`
  - Resto → `permitAll()`
- HTTP Basic Auth habilitado
- Sessão STATELESS
- `UserDetailsService` que busca admin no banco por email
- Bean `PasswordEncoder` → `BCryptPasswordEncoder`

### 1.10 — CORS

**`config/CorsConfig.java`**
- Origem permitida: `http://localhost:5173`
- Métodos: GET, POST, PUT, DELETE, OPTIONS
- `allowCredentials(true)` (necessário para Basic Auth)

### 1.11 — Seed do admin padrão

**`config/DataSeeder.java`** — implements `CommandLineRunner`
- Se não existe nenhum admin, cria:
  - Nome: "Administrador"
  - Email: `admin@ifma.edu.br`
  - Senha: `admin123` (codificada com BCrypt)
- Idempotente — não duplica em restarts

### 1.12 — Dockerfile (multi-stage)

**Arquivo:** `ru-ifma-backend/Dockerfile`
- **Stage 1 (build):** `eclipse-temurin:21-jdk` + Maven → `mvn clean package -DskipTests`
- **Stage 2 (runtime):** `eclipse-temurin:21-jre-alpine` → copia só o `.jar`
- `EXPOSE 8080`
- `ENTRYPOINT ["java", "-jar", "app.jar"]`
- Usado pelo Railway no deploy; localmente o backend roda pelo IntelliJ

---

## PARTE 2: FRONTEND (`ru-ifma-frontend/`)

### 2.1 — Inicialização do projeto

```bash
npm create vite@latest . -- --template react
npm install axios react-router-dom react-datepicker
npm install -D tailwindcss @tailwindcss/vite
```

### 2.2 — Configuração do Vite e Tailwind

**`vite.config.js`** — plugins: `react()`, `tailwindcss()`

**`src/index.css`** — Tailwind v4 com cores IFMA:
```css
@import "tailwindcss";

@theme {
  --color-ifma-green: #006633;
  --color-ifma-green-light: #00994D;
  --color-ifma-green-dark: #004D26;
}
```

### 2.3 — Axios config

**`src/services/api.js`**
- `API_URL = "http://localhost:8080"` (constante fácil de trocar)
- Interceptor de request: adiciona header `Authorization: Basic <base64>` do localStorage
- Interceptor de response: redireciona para login em caso de 401

### 2.4 — Estrutura de pastas

```
src/
  services/api.js
  contexts/AuthContext.jsx
  components/
    layout/Header.jsx, Footer.jsx, AdminLayout.jsx
    common/Loading.jsx, DateNavigator.jsx, ProtectedRoute.jsx
  pages/
    public/HomePage.jsx
    admin/LoginPage.jsx, DashboardPage.jsx,
          CardapioListPage.jsx, CardapioFormPage.jsx,
          AdministradorListPage.jsx, AdministradorFormPage.jsx
```

### 2.5 — Autenticação

**`contexts/AuthContext.jsx`**
- `login(email, senha)` — testa credenciais chamando `/api/dashboard`, salva base64 no localStorage
- `logout()` — limpa localStorage
- `isAuthenticated` — estado reativo

**`components/common/ProtectedRoute.jsx`**
- Redireciona para `/admin/login` se não autenticado

### 2.6 — Rotas (`App.jsx`)

| Rota | Página | Auth |
|------|--------|------|
| `/` | HomePage (cardápio público) | Não |
| `/admin/login` | LoginPage | Não |
| `/admin` | DashboardPage | Sim |
| `/admin/cardapios` | CardapioListPage | Sim |
| `/admin/cardapios/novo` | CardapioFormPage | Sim |
| `/admin/cardapios/editar/:id` | CardapioFormPage | Sim |
| `/admin/administradores` | AdministradorListPage | Sim |
| `/admin/administradores/novo` | AdministradorFormPage | Sim |
| `/admin/administradores/editar/:id` | AdministradorFormPage | Sim |

Rotas admin ficam dentro de `<AdminLayout>` com sidebar fixa.

### 2.7 — Páginas públicas

**HomePage** — Página principal
- Mostra cardápio do dia (almoço e jantar) em cards
- Navegação por data: botões anterior/próximo + calendário (react-datepicker)
- Chama `GET /api/cardapios/public?data=yyyy-MM-dd`
- Mensagem amigável quando não há cardápio
- Responsivo: cards empilham no mobile, lado a lado no desktop
- Usar o skill `frontend-design` para o visual

### 2.8 — Páginas admin

**LoginPage** — Formulário email + senha, fundo verde IFMA, card branco centralizado
**DashboardPage** — 4 cards com estatísticas do `/api/dashboard`
**CardapioListPage** — Tabela com todos os cardápios + botões editar/excluir
**CardapioFormPage** — Formulário compartilhado criar/editar (detecta por `useParams`)
**AdministradorListPage** — Tabela com admins + ações
**AdministradorFormPage** — Formulário compartilhado criar/editar

### 2.9 — Layout admin

**AdminLayout** — Sidebar verde com navegação (Dashboard, Cardápios, Administradores, Sair) + área principal com `<Outlet />`

**Header** (público) — Logo IFMA, título "Restaurante Universitário - IFMA", link para admin
**Footer** (público) — "IFMA - Instituto Federal do Maranhão" + ano

---

## Verificação

### Backend
1. Subir PostgreSQL: `docker compose up -d` dentro de `ru-ifma-backend/`
2. Rodar o backend pelo IntelliJ (ou `mvn spring-boot:run`)
3. Verificar seed: log "Admin padrão criado" no console
4. Testar endpoint público: `GET http://localhost:8080/api/cardapios/public?data=2026-02-26` → 200 (array vazio)
5. Testar auth: `GET http://localhost:8080/api/dashboard` sem header → 401
6. Testar auth: `GET http://localhost:8080/api/dashboard` com `Authorization: Basic YWRtaW5AaWZtYS5lZHUuYnI6YWRtaW4xMjM=` → 200
7. Testar CRUD: criar cardápio via POST, listar, editar, excluir

### Frontend
1. `npm run dev` dentro de `ru-ifma-frontend/`
2. Acessar `http://localhost:5173` → página do cardápio
3. Navegar entre datas, usar calendário
4. Acessar `/admin` → redireciona para login
5. Logar com `admin@ifma.edu.br` / `admin123`
6. Testar CRUD de cardápios e administradores no painel
