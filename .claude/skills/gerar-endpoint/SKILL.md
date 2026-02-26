---
name: gerar-endpoint
description: Gera um endpoint CRUD completo com Controller, Service, Repository e DTOs seguindo a arquitetura do RU-IFMA
disable-model-invocation: true
argument-hint: [NomeDaEntidade]
---

Gere um endpoint CRUD completo para a entidade **$ARGUMENTS** seguindo a arquitetura do projeto RU-IFMA.

As convencoes de backend (camadas, DTOs, validacao) estao no CLAUDE.md. Siga-as rigorosamente.

## Pacote base

`br.edu.ifma.ru`

## Arquivos a criar (nesta ordem)

1. `model/$ARGUMENTS.java` - Entity JPA
2. `repository/$ARGUMENTSRepository.java` - Interface JpaRepository
3. `dto/$ARGUMENTSRequest.java` - Record com Bean Validation
4. `dto/$ARGUMENTSResponse.java` - Record (sem dados sensiveis)
5. `service/$ARGUMENTSService.java` - Logica de negocio
6. `controller/Admin$ARGUMENTSController.java` - Endpoints REST

## Endpoints gerados

```
GET    /api/admin/{entidade}       - Lista todos (200)
GET    /api/admin/{entidade}/{id}  - Busca por ID (200)
POST   /api/admin/{entidade}       - Cria (201)
PUT    /api/admin/{entidade}/{id}  - Atualiza (200)
DELETE /api/admin/{entidade}/{id}  - Remove (204)
```

## Passos

1. Pergunte quais campos a entidade precisa ter
2. Crie os arquivos na ordem acima
3. Atualize o SecurityConfig se necessario
4. Mostre um resumo dos endpoints criados
