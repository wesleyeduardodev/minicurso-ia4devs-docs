---
name: commit
description: Cria um commit seguindo as convencoes do projeto RU-IFMA
disable-model-invocation: true
argument-hint: [mensagem opcional]
---

Crie um commit Git seguindo as convencoes do projeto RU-IFMA.

## Convencoes de commit

- Mensagem em portugues
- SEM emojis na mensagem
- Formato: tipo seguido de descricao curta e objetiva
- Tipos: feat, fix, refactor, style, docs, chore, test

## Passos

1. Rode `git status` para ver as alteracoes
2. Rode `git diff` para entender o que mudou
3. Rode `git log --oneline -5` para ver o estilo dos commits recentes
4. Analise as mudancas e classifique o tipo (feat, fix, refactor, etc.)
5. Escreva uma mensagem de commit clara e concisa
6. Adicione apenas os arquivos relevantes (nunca `git add .` cegamente)
7. NAO inclua arquivos sensiveis (.env, credenciais)
8. Crie o commit com Co-Authored-By

## Se o usuario passou uma mensagem como argumento

Use "$ARGUMENTS" como base para a mensagem, mas ajuste se necessario para seguir as convencoes.

## Formato do commit

```
tipo: descricao curta do que foi feito

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
```
