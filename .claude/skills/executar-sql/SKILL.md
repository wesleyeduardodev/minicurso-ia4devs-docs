---
name: executar-sql
description: Executa comandos SQL no banco PostgreSQL do projeto RU-IFMA via container Docker
disable-model-invocation: true
argument-hint: [comando SQL ou descricao do que fazer]
---

Execute o comando SQL solicitado no banco PostgreSQL do projeto RU-IFMA.

## Acesso ao banco

Container Docker acessado via WSL:

```bash
wsl docker exec -i ru-ifma-postgres psql -U postgres -d ru_ifma -c "SQL AQUI"
```

## Dados de conexao

- Container: ru-ifma-postgres
- Database: ru_ifma
- Usuario: postgres
- Senha: postgres

## Tabelas disponiveis

### cardapios
- id (bigserial, PK)
- data (date, not null)
- tipo_refeicao (varchar - ALMOCO ou JANTAR)
- prato_principal (varchar, not null)
- acompanhamento (varchar, not null)
- salada (varchar, not null)
- sobremesa (varchar, not null)
- suco (varchar, not null)
- Constraint unica: (data, tipo_refeicao)

### administradores
- id (bigserial, PK)
- nome (varchar, not null)
- email (varchar, not null, unique)
- senha (varchar, not null - hash BCrypt)

## Regras

1. Se o usuario passou um SQL direto em $ARGUMENTS, execute-o
2. Se o usuario descreveu o que quer em linguagem natural, monte o SQL correspondente
3. SEMPRE mostre o SQL que vai executar ANTES de rodar e peca confirmacao do usuario
4. Para operacoes destrutivas (DELETE, DROP, TRUNCATE), alerte o usuario sobre o impacto
5. Para INSERT em administradores, a senha DEVE ser um hash BCrypt (nunca texto puro)
6. Respeite a constraint unica (data, tipo_refeicao) ao inserir cardapios
7. Apos executar, mostre o resultado formatado em tabela markdown
