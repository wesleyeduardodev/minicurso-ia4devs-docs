# RU IFMA - Restaurante Universitario

Base URL: https://ru-ifma-backend-production.up.railway.app

## Stack

- **Backend:** Spring Boot 4.0.3, Java 21, PostgreSQL 17
- **Frontend:** React 19, Vite, TailwindCSS v4
- **Banco:** PostgreSQL 17 via Docker Compose (container `ru-ifma-db`)

## Estrutura do workspace

```
ru-ifma/
  ru-ifma-backend/   -> API REST (Spring Boot)
  ru-ifma-frontend/  -> SPA (React + Vite)
```

## Como rodar

1. Banco: `cd ru-ifma-backend && docker compose up -d`
2. Backend: abrir `ru-ifma-backend` no IntelliJ e rodar a classe `RuIfmaBackendApplication` (porta 8080)
3. Frontend: `cd ru-ifma-frontend && npm run dev` (porta 5173)

Admin padrao seed: `admin@ifma.edu.br` / `admin123`

## Convencoes obrigatorias

- Todo codigo em portugues (variaveis, metodos, classes, componentes, nomes de arquivos Java)
- NAO adicionar comentarios em nenhum lugar. Codigo autoexplicativo (Clean Code)
- NAO usar emojis em nenhum lugar (nem em UI, nem em codigo, nem em commits)
- Imports sempre no topo do arquivo, organizados. Nunca inline
- SOLID: responsabilidade unica, classes e funcoes pequenas e coesas
- Todos os textos visiveis ao usuario devem ter acentuacao correta em portugues

## Boas praticas

- Preferir editar arquivos existentes a criar novos
- Nao criar abstracoes prematuras
- Nao adicionar features alem do solicitado
- Backend roda pelo IntelliJ, Docker so pro banco
- Jackson 3 (Spring Boot 4): `WRITE_DATES_AS_TIMESTAMPS` ja e false por padrao, nao configurar manualmente
