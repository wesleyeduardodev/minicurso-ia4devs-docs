---
name: explicar-codigo
description: Explica partes do codigo do projeto RU-IFMA de forma didatica, ideal para alunos e iniciantes. Usar quando pedirem para explicar como algo funciona no projeto.
argument-hint: [arquivo ou conceito]
---

Explique **$ARGUMENTS** do projeto RU-IFMA de forma didatica para alunos.

## Como explicar

1. **Analogia simples**: comece comparando com algo do dia a dia
2. **Diagrama visual**: use ASCII art para mostrar o fluxo ou estrutura
3. **Passo a passo**: explique o que acontece em cada etapa
4. **Exemplo pratico**: mostre como o codigo e usado na pratica
5. **Armadilha comum**: destaque um erro que iniciantes costumam cometer

## Contexto do projeto

- Backend: Spring Boot 4 + Java 21 (arquitetura em camadas)
- Frontend: React 19 + Vite + TailwindCSS v4
- Banco: PostgreSQL 17
- Auth: HTTP Basic Auth com Spring Security

## Tom da explicacao

- Conversacional e acessivel
- Sem jargao desnecessario (ou explique o jargao quando usar)
- Use exemplos concretos do proprio projeto RU-IFMA
- SEM emojis

## Conceitos que os alunos costumam perguntar

- Como funciona a autenticacao (Basic Auth + Spring Security)
- Fluxo de uma requisicao (Controller -> Service -> Repository)
- Como o React se comunica com o backend (Axios + interceptor)
- Como o CORS funciona
- O que sao DTOs e por que usar Records
- Como o TailwindCSS v4 funciona sem tailwind.config.js
