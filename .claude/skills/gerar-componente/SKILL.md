---
name: gerar-componente
description: Gera um componente React seguindo os padroes do projeto RU-IFMA
disable-model-invocation: true
argument-hint: [NomeDoComponente]
---

Gere o componente React chamado **$ARGUMENTS** seguindo os padroes do projeto RU-IFMA.

As convencoes gerais e de frontend estao no CLAUDE.md. Siga-as rigorosamente.

## Padroes de UI do projeto

- Cards: `border-l-4 border-ifma-green shadow-md`
- Botoes primarios: `bg-ifma-green text-white hover:bg-ifma-green-dark rounded px-4 py-2`
- Tabelas: `divide-y divide-gray-200`, header `bg-gray-50`
- Loading: `animate-spin border-b-2 border-ifma-green`
- Inputs: `focus:ring-2 focus:ring-ifma-green`

## Estrutura

- Componente reutilizavel: `ru-ifma-frontend/src/components/`
- Pagina: `ru-ifma-frontend/src/pages/admin/` ou `ru-ifma-frontend/src/pages/public/`
- Se precisar de dados da API, usar `src/services/api.js`
- Se precisar de autenticacao, usar `useAuth()` de `src/contexts/AuthContext.jsx`

## Passos

1. Pergunte se e um componente reutilizavel ou uma pagina
2. Crie o arquivo no diretorio correto
3. Implemente seguindo os padroes acima e as convencoes do CLAUDE.md
4. Mostre o resultado final
