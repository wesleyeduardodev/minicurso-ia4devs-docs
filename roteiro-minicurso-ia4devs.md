# Mini Curso: IA para Devs — Roteiro Completo com Claude Code

## Sistema do Restaurante Universitário do IFMA

> **Formato:** Demonstração ao vivo — o instrutor executa, os alunos acompanham na tela.
>
> **Objetivo:** Construir do zero um sistema de cardápio para o Restaurante Universitário usando Claude Code como agente de codificação.
>
> **Stack:** React (frontend) · Spring Boot 4.0.3 / Java 21 (API) · PostgreSQL 17 via Docker
>
> **Terminal:** Git Bash (Windows)

---

## MÓDULO 01 — Setup e Primeira Interação

> ~20min de prática | (os slides conceituais vêm antes — ~40min)

---

### Passo 1 — Instalar o Claude Code

> **Pré-requisito:** Ter o [Git for Windows](https://git-scm.com/downloads/win) instalado.
> Ele fornece o Git Bash, que é o terminal que vamos usar durante todo o mini-curso.
> Não precisa de Node.js — o instalador nativo do Claude Code já inclui tudo.

**Abra o Git Bash e rode:**

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

> Após instalar, **feche e reabra o Git Bash** para o comando `claude` ser reconhecido.

**Verificar instalação:**

```bash
claude --version
```

**Verificar saúde da instalação:**

```bash
claude doctor
```

---

### Passo 2 — Fazer login

```bash
claude
```

> Na primeira execução, o Claude Code abre o navegador para autenticação OAuth.
> Faça login com sua conta Claude (Pro, Max ou Console).
> Depois de autenticado, volte ao terminal — já está pronto.

---

### Passo 3 — Criar a estrutura de pastas do workspace

> Crie manualmente no Windows Explorer a pasta `ru-ifma` onde preferir (ex: `Documentos/projetos-git/`).
>
> **NÃO rode `git init` nessa pasta.** Ela é apenas o workspace.
> Os repositórios Git vão ficar dentro de cada sub-projeto.

---

### Passo 4 — Criar os repositórios no GitHub e clonar

> Crie dois repositórios no GitHub (via browser):
>
> 1. `ru-ifma-backend`
> 2. `ru-ifma-frontend`
>
> Depois abra o Git Bash na pasta `ru-ifma` e clone ambos:

```bash
git clone https://github.com/SEU-USUARIO/ru-ifma-backend.git
git clone https://github.com/SEU-USUARIO/ru-ifma-frontend.git
```

**Resultado:**

```
ru-ifma/
├── ru-ifma-backend/
└── ru-ifma-frontend/
```

---

### Passo 5 — Iniciar o Claude Code no workspace

> Existem duas formas de usar o Claude Code:
>
> **Opção A — Terminal (Git Bash):**
> Abra o Git Bash na pasta `ru-ifma` e rode:

```bash
claude
```

> **Opção B — VS Code com a extensão Claude Code:**
> Instale a extensão "Claude Code" no VS Code (busque por "Claude" no marketplace).
> Abra a pasta `ru-ifma` no VS Code e use o Claude Code direto pelo editor,
> com o painel integrado. É uma boa opção pra quem prefere ficar no VS Code
> e ter o código e o agente lado a lado.
>
> As duas opções funcionam da mesma forma — escolha a que for mais confortável.
> Neste curso vamos usar o terminal, mas fiquem à vontade pra usar o VS Code.

> O Claude Code agora está na raiz e enxerga ambos os sub-projetos.

---

## MÓDULO 02 — Planejamento e Construção da V1

> ~2h30 | Plugin + Plan mode + Memória + Build da V1 + Teste + Commit + Hands-on

---

### Passo 6 — Instalar o plugin frontend-design

> **O que são plugins?**
>
> O Claude Code tem um sistema de plugins que adiciona habilidades extras ao agente.
> Vamos instalar o **frontend-design** — ele ajuda o Claude a criar interfaces mais bonitas
> e com padrão de produção, ao invés de gerar aquele frontend genérico.

**Dentro do Claude Code, digite:**

```
/plugin
```

> Vai abrir o menu de plugins. Siga os passos:
>
> 1. Selecione a aba **"Install"**
> 2. No campo de busca, digite: `anthropics/claude-code`
> 3. Na lista que aparece, navegue até **frontend-design [development]**
> 4. Aperte **Space** para marcar o plugin
> 5. Aperte **Enter** para instalar
>
> Pronto! O plugin já está ativo na sessão.

> **Sobre plugins:** Plugins funcionam como especializações do agente.
> O frontend-design, por exemplo, faz o Claude gerar interfaces com mais cuidado visual —
> layouts melhores, uso correto de espaçamento, tipografia e cores.

---

### Passo 7 — Entrar em modo de planejamento (plan mode)

> **O que é o plan mode?**
>
> O Claude Code tem um modo de planejamento nativo. Quando você pede pra ele planejar, ele entra num modo onde **analisa, arquiteta e propõe** — mas **não executa nada**.
> Ele apresenta o plano completo e espera sua aprovação antes de escrever qualquer código.
>
> Isso replica o fluxo real de trabalho: primeiro você pensa, depois executa.

> Antes de colar o prompt, ative o plan mode apertando **Shift+Tab** (o indicador no rodapé muda para "Plan").
> Isso garante que o Claude só vai planejar, sem executar nada.
>
> O prompt abaixo foi gerado com ajuda do próprio Claude — dei um prompt inicial descrevendo o que eu queria e pedi para ele gerar um prompt detalhado para o modo de planejamento.
>
> Cole este prompt no Claude Code:

```
Preciso criar uma aplicação web para o Restaurante Universitário do IFMA.

A ideia é simples: uma página pública onde os alunos veem o cardápio do dia,
e uma área admin onde o pessoal do RU cadastra os cardápios.

Página pública (qualquer pessoa acessa):
- Ver o cardápio do dia (almoço e jantar)
- Navegar entre datas (anterior / próximo)
- Escolher uma data no calendário
- Cada cardápio tem: data, tipo de refeição (almoço ou jantar), prato principal,
  acompanhamento, salada, sobremesa e suco

Área admin (acessa por /admin → redireciona pro login se não estiver autenticado):
- Login simples com email e senha
- CRUD de cardápios (criar, editar, excluir)
- CRUD de administradores
- Um dashboard simples com visão geral

Stack:
- Frontend: React na versão mais atual e estável, com Vite e TailwindCSS → pasta ru-ifma-frontend/
- Backend: Spring Boot 4.0.3, Java 21 → pasta ru-ifma-backend/
- Banco: PostgreSQL 17 via Docker Compose (docker-compose.yml dentro de ru-ifma-backend/)
- Cada pasta é um repositório Git separado

Arquitetura do backend simples e direta:
- Camadas: Controller → Service → Repository (sem camadas extras)
- Sem segurança complexa por enquanto — só um Basic Auth simples pro admin
- Seed com admin padrão: admin@ifma.edu.br / admin123
- Classes de entrada da API com sufixo Request (ex: CardapioRequest)
- Classes de saída da API com sufixo Response (ex: CardapioResponse)
- Sufixo DTO só pra classes de uso interno entre camadas
- Usar Java Records pra Request, Response e DTO — sem boilerplate
- O backend vai ser rodado pelo IntelliJ, não precisa de Docker pro backend

Dockerfile (dentro de ru-ifma-backend/):
- Criar um Dockerfile multi-stage otimizado para produção
- Stage 1 (build): usar imagem Maven com Eclipse Temurin JDK 21, buildar o .jar
- Stage 2 (runtime): usar imagem Eclipse Temurin JRE 21 Alpine (leve), copiar só o .jar
- Expor porta 8080
- Esse Dockerfile é usado pelo Railway no deploy — localmente o backend roda pelo IntelliJ

Regras:
- Docker Compose só pro banco de dados (PostgreSQL 17), backend roda pelo IntelliJ
- Backend na porta 8080, frontend na 5173
- CORS liberado pra localhost:5173 (depois no deploy vamos trocar)
- No application.properties, usar variáveis de ambiente com defaults pro local:
  spring.datasource.url=${DB_URL:jdbc:postgresql://localhost:5432/ru_ifma}
  spring.datasource.username=${DB_USER:postgres}
  spring.datasource.password=${DB_PASSWORD:postgres}
- Design responsivo com as cores do IFMA (verde e branco)
- No frontend, use o plugin frontend-design que está instalado para ajudar no visual
- A base URL da API no frontend deve ficar numa constante no arquivo de
  configuração do axios (ex: src/services/api.js), fácil de trocar:
  const API_URL = "http://localhost:8080"

Não pesquise versões — use exatamente as versões especificadas acima.

Quero fazer por partes: primeiro o backend completo e funcionando, depois o frontend.
Organiza o plano nessa ordem.

Me apresenta o plano completo antes de executar qualquer coisa.
```

> **Quando o Claude apresentar o plano:**
>
> 1. Revise com os alunos a arquitetura proposta
> 2. Discuta as entidades, endpoints e telas
> 3. Se algo não estiver bom, converse com o Claude Code para ajustar
> 4. Se tudo estiver ok, aprove o plano e aguarde o Claude finalizar a execução
> 5. Depois de finalizado, peça para ele criar o Passo 8 (a seguir)
>
> **Discussão com a turma:** Vamos olhar o plano que ele montou. Faltou alguma entidade?
> Por que será que ele separou em Controller, Service e Repository?
> E se a gente fosse montar esse plano na mão, quanto tempo levaria?

> **Durante a execução (após aprovar o plano):**
>
> A partir daqui, o Claude Code vai executar task por task.
> **Deixe ele trabalhar.** Acompanhe com os alunos o que ele está fazendo.
> Comente em voz alta: "Vejam, agora ele está criando as entidades...", "Olha ele criando os Records de Request e Response...", etc.
>
> **Se ele perguntar algo:** responda e deixe ele continuar.
> **Se ele travar ou der erro:** é oportunidade de ensino! Mostre como corrigir.
> **Se a sessão ficar longa:** use `/compact` para compactar o contexto.
>
> **Perguntas pro chat durante o build (jogue enquanto o Claude trabalha):**
> - "Ele tá criando as entidades agora — alguém sabe o que é o @Entity do JPA?"
> - "Repararam que ele usou Record em vez de class? Por que será?"
> - "Ele criou um service separado do controller — qual a vantagem disso?"
> - "Olha ele configurando o CORS — alguém já teve problema com CORS antes?"
> - "Agora ele tá no frontend — reparem como ele organiza os componentes React"

---

### Passo 8 — Criar os arquivos de memória (CLAUDE.md)

> **Por que fazer isso agora?**
>
> O Claude Code é **stateless** — se a sessão travar, ele perde todo o contexto.
> Os arquivos `CLAUDE.md` funcionam como a **memória persistente** do agente.
> Se precisarmos reiniciar, ele lê esses arquivos e retoma de onde parou.
>
> Vamos criar 3 arquivos de memória: um na raiz (visão global) e um em cada sub-projeto (contexto específico).

> Cole este prompt no Claude Code (na mesma sessão, após a implementação):

```
Agora que a implementação está pronta, crie 3 arquivos CLAUDE.md como memória do projeto.
Se a sessão cair, esses arquivos permitem retomar de onde parou.

Crie baseado no código que acabamos de implementar:

1. ru-ifma/CLAUDE.md (visão global):
   - Stack, estrutura do workspace, como rodar cada parte
   - Boas práticas e convenções do projeto

2. ru-ifma-backend/CLAUDE.md (contexto do backend):
   - Endpoints, entidades, arquitetura, como rodar

3. ru-ifma-frontend/CLAUDE.md (contexto do frontend):
   - Telas, integração com API, design
   - REGRA OBRIGATÓRIA: todos os textos visíveis ao usuário (labels, botões, mensagens,
     placeholders, títulos, erros) devem ter acentuação correta em português.
     Exemplos: "Cardápio", "Refeição", "Próximo", "Não encontrado", "Descrição"

Regras importantes pra incluir nos CLAUDE.md:
- Todo código em português (variáveis, métodos, classes, componentes)
- NÃO adicionar comentários — código autoexplicativo (Clean Code)
- NÃO usar emojis em nenhum lugar
- Imports sempre no topo, organizados (nunca inline)
- SOLID: responsabilidade única, classes/funções pequenas e coesas
- Backend: Controller → Service → Repository (controller nunca acessa repository direto)
- Backend: sufixo Request/Response pra API, sufixo DTO só pra uso interno, tudo com Java Records
- Frontend: componentes funcionais com hooks, lógica de API separada em services
- Backend roda pelo IntelliJ, Docker só pro banco

Crie os 3 arquivos agora.
```

> **Mostre os 3 arquivos criados e explique:**
>
> **Reflexão:** Reparem que criamos 3 arquivos de memória. Isso funciona como uma hierarquia:
> o CLAUDE.md da raiz é o contexto global — ele é lido sempre. Já o de cada sub-pasta
> é o contexto específico, lido só quando o agente trabalha naquela pasta.
> Mas pra que serve isso se o agente já sabe o que a gente pediu?
> E o que acontece se a sessão cair agora e a gente não tivesse criado esses arquivos?

---

### Verificando o uso da sessão — `/context`

> Agora que fizemos a implementação inteira e criamos os arquivos de memória,
> vamos ver quanto dessa sessão a gente já consumiu.

No Claude Code, digite:

```
/context
```

> O comando `/context` mostra o uso de tokens da sessão atual — quanto do contexto
> já foi consumido e quanto ainda resta. É como olhar o "medidor de combustível" da sessão.
>
> **Mostre o resultado pros alunos e comente:**
>
> "Olhem quanto a gente já usou só com o build da V1 e a criação dos CLAUDE.md.
> Quando esse medidor chega perto do limite, a sessão fica mais lenta e o agente
> pode começar a 'esquecer' coisas do início da conversa. Por isso que existe o
> `/compact` — ele compacta o histórico pra liberar espaço e a gente poder continuar."
>
> **Reflexão:** Isso explica por que criamos os CLAUDE.md. Se a sessão estourar
> o contexto ou precisar reiniciar, os arquivos de memória garantem que nada se perde.

---

### Se a sessão travar ou precisar reiniciar

> Isso é **normal** em sessões longas. Mostre pros alunos como lidar:

```bash
# 1. Feche o Claude Code (Ctrl+C ou feche o terminal)

# 2. Reabra o Git Bash na pasta raiz
cd ru-ifma
claude

# 3. Cole este prompt para retomar:
```

```
Leia os arquivos CLAUDE.md (raiz, ru-ifma-backend/ e ru-ifma-frontend/).
Eles contêm todo o contexto do projeto.

Analise o estado atual do código em ambos os repositórios.
Identifique o que já foi feito e o que falta.
Continue a implementação de onde parou.
```

> **Reflexão:** É exatamente por isso que a gente criou os CLAUDE.md logo após implementar. Eles funcionam como um checkpoint — um save game do projeto. Se cair, a gente retoma de onde parou.

---

### Passo 9 — Subir o ambiente LOCAL e testar a V1 que foi gerada pelo Claude Code

> Depois que o Claude Code terminar, teste tudo:

```
A implementação terminou. Vamos testar tudo.

1. Suba o banco: cd ru-ifma-backend && docker compose up -d
2. Verifique se o banco está rodando: docker compose ps
3. Rode o backend pelo IntelliJ (Run na classe principal)
4. Rode o frontend: cd ru-ifma-frontend && npm run dev

Teste os seguintes fluxos:
- Abra http://localhost:5173 → deve mostrar a página de cardápio (vazia)
- Vá para /admin/login → faça login com admin@ifma.edu.br / admin123
- Cadastre um cardápio para hoje (almoço e jantar)
- Volte para a página principal → deve mostrar os cardápios
- Navegue entre datas
- Crie um novo admin
- Teste no celular (responsividade)

Se encontrar algum erro, corrija.
```

---

### Passo 10 — Corrigir bugs da V1 (se houver)

> É muito provável que algo não funcione de primeira. **Isso é parte do processo.**
>
> Exemplos comuns de problemas:
> - CORS bloqueando requests
> - Basic Auth não enviando credenciais corretamente
> - Frontend chamando endpoint errado
> - Docker Compose com porta em uso
> - Tailwind não aplicando estilos
>
> Para cada bug, descreva o problema pro Claude Code:

```
Estou com o seguinte erro: [DESCREVA O ERRO]

[COLE O LOG DE ERRO SE TIVER]

Investigue e corrija.
```

> **Reflexão:** O agente é muito bom em debugar quando a gente dá contexto suficiente —
> a mensagem de erro e onde aconteceu. Como vocês costumam debugar? Googlar o erro?
> Stack Overflow? Agora imagina colar o erro direto pro agente e ele corrigir sozinho.

---

### Testando a V1 com Playwright (MCP)

> A V1 está rodando e os bugs foram corrigidos. Agora vamos usar o Claude Code
> pra **testar a aplicação como um usuário real** — navegando pelo browser.
>
> **O que é MCP?** MCP (Model Context Protocol) é um protocolo aberto que conecta
> o Claude Code a ferramentas externas. Aqui vamos usar o MCP do **Playwright**
> pra dar ao agente a capacidade de controlar o browser.
>
> **Importante:** Playwright não é um agente de IA nem uma skill — é um **framework de automação e testes de aplicações web** criado pela **Microsoft**. O agente de IA (Claude Code) usa o Playwright via MCP como uma **ferramenta**, ganhando a capacidade de controlar o browser.
>
> O Playwright MCP permite que o agente abra o navegador, clique em botões, preencha formulários
> e valide se tudo está funcionando — sem a gente escrever nenhum script de teste.

**Instalar o Playwright MCP e os browsers:**

> No terminal (fora do Claude Code), rode:

```bash
# 1. Adicionar o Playwright MCP ao Claude Code
claude mcp add playwright npx @playwright/mcp@latest

# 2. Instalar o Playwright e baixar os browsers
npm init -y && npm install @playwright/test && npx playwright install
```

> **Nota:** O `npm init -y` cria um package.json temporário na pasta —
> sem ele o Playwright não consegue baixar os browsers. Pode ignorar o package.json depois.

> Verifique se conectou. Dentro do Claude Code, digite:

```
/mcp
```

> Deve mostrar o servidor `playwright` como ativo.

**Testar — peça pro Claude testar a aplicação pelo browser:**

> Com o backend e frontend rodando, cole este prompt:

```
Usando o Playwright MCP, teste a aplicação do RU IFMA:

1. Abra http://localhost:5173 e verifique se a página pública carrega
2. Navegue até /admin/login
3. Faça login com admin@ifma.edu.br / admin123
4. Verifique se o dashboard admin carrega após o login
5. Me diga o que encontrou em cada passo — se algo falhou, descreva o erro
```

> **Reflexão:** O agente não está só lendo código — ele está **usando a aplicação**
> como um usuário faria. Isso é poderoso pra testes exploratórios e pra encontrar bugs visuais
> que não aparecem no código. E a gente não escreveu nenhum script de teste.
>
> Vamos ver mais sobre MCP (conectando ao banco de dados) no MÓDULO 04.

**Commit:** não precisa — MCP é configuração local, não vai pro repositório.

---


## MÓDULO 03 — Deploy

> ~20min | Frontend na Vercel + Backend e banco no Railway


### Passo 11 — Commit da V1

> Com tudo funcionando, faça o primeiro commit:

```
Faça o commit inicial do projeto com tudo que temos até agora.

No backend (ru-ifma-backend/):
- git add, commit com mensagem "feat: V1 completa da API do RU IFMA" e push

No frontend (ru-ifma-frontend/):
- git add, commit com mensagem "feat: V1 completa do frontend do RU IFMA" e push
```

> **Reflexão:** O Claude Code sabe fazer git add, commit e push sozinho.
> E é boa prática commitar a V1 antes de começar as melhorias — assim a gente tem um ponto de retorno seguro.
> Saímos do zero até uma V1 commitada. Quanto tempo vocês acham que levaria fazer isso na mão?


---

### Passo 12 — Deploy (frontend na Vercel, backend + banco no Railway)

> **Dica:** Se a demo ao vivo travou em algum ponto e você não conseguiu terminar a V1,
> agora é a hora de usar o branch `backup`. Faça `git checkout backup` nos dois repos
> e siga com o deploy normalmente. Os alunos não precisam saber — diga
> "vou pular pra versão estável pra gente seguir com o deploy".

> **Essa parte é operada pelo instrutor manualmente**, sem o Claude Code.
> O objetivo é mostrar pros alunos que o projeto sai do localhost e vai pro ar em minutos.

**Antes do deploy, peça pro Claude Code criar o arquivo de configuração da Vercel:**

```
Crie o arquivo vercel.json na raiz de ru-ifma-frontend/ com a configuração
de rewrite para SPA (todas as rotas redirecionam pra index.html).
Faça o commit e push.
```

---

**Deploy do banco + backend — Railway (primeiro):**

> 1. Acesse [railway.app](https://railway.app) e faça login com GitHub
> 2. Crie um novo projeto
> 3. Adicione um serviço **PostgreSQL** (Railway provisiona automaticamente)
> 4. Adicione um novo serviço importando o repositório `ru-ifma-backend`
>    - Railway detecta o Dockerfile e builda automaticamente
>    - Agora configure as variáveis de ambiente no **serviço do backend**:
>
> **Como pegar os valores:** clique no serviço **PostgreSQL** no Railway → aba **Variables**.
> Vai ter um monte de variável parecida. Ignore a maioria. Só use estas 3:
>
> | Variável do PostgreSQL (só consultar) | Exemplo de valor |
> |---|---|
> | **PGHOST** | `postgres.railway.internal` |
> | **PGUSER** | `postgres` |
> | **PGPASSWORD** | `rDAObUomuKvXoLAqKXfcWqXThXNowpVX` |
>
> **Como montar a DB_URL:** pegue o valor de **PGHOST** e encaixe nesse modelo:
>
> ```
> jdbc:postgresql://VALOR_DO_PGHOST:5432/railway
> ```
>
> Exemplo real:
> ```
> jdbc:postgresql://postgres.railway.internal:5432/railway
> ```
>
> **Agora vá no serviço do backend** → aba **Variables** → crie estas 3 variáveis:
>
> | Nome da variável (criar no backend) | Valor (copiar do PostgreSQL) |
> |---|---|
> | `DB_URL` | `jdbc:postgresql://postgres.railway.internal:5432/railway` |
> | `DB_USER` | Copie o valor de **PGUSER** |
> | `DB_PASSWORD` | Copie o valor de **PGPASSWORD** |
>
> **Atenção:** a porta é sempre `5432` e o banco é sempre `railway` no Railway.
> O que muda entre projetos é o PGHOST e o PGPASSWORD.
> 5. Após o deploy, copie a **URL pública** do backend
>
> **IMPORTANTE — Ajustar o CORS no backend:**
> O CORS está liberado só pra `localhost:5173`. Antes de deployar o front,
> atualize o CORS no backend pra aceitar também o domínio da Vercel
> (ou libere `*` pra simplificar durante a demo). Commit e push.

---

**Deploy do frontend — Vercel (depois):**

> 1. Troque a base URL da API no código
>    (em `src/services/api.js`) de `http://localhost:8080` para a URL do backend no Railway
> 2. Commit e push
> 3. Acesse [vercel.com](https://vercel.com) e faça login com GitHub
> 4. Clique em **"Add New Project"**
> 5. Importe o repositório `ru-ifma-frontend`
> 6. A Vercel detecta o Vite automaticamente — só confirme e clique em **Deploy**

---

> **Mostre a aplicação rodando na URL pública. Abra no celular pra mostrar responsividade.
> Cadastre um cardápio pelo admin e mostre aparecendo na página pública.**
>
> **Resultado:** Tudo online. Saímos do zero ao deploy em uma aula. Isso é o poder de um agente de IA no workflow de um dev.

---

### Passo 13 — Mão na massa dos alunos: Gemini CLI e Codex CLI

> **Por que agora?**
>
> Os alunos assistiram a construção inteira da V1. Agora é a vez deles experimentarem
> um agente de codificação na prática. Como o Claude Code é pago, vamos usar duas
> alternativas gratuitas: **Gemini CLI** (Google) e **Codex CLI** (OpenAI).
> Cada aluno escolhe qual quer testar — a lógica é a mesma.
>
> Vocês viram o que o Claude Code fez. Agora é a vez de vocês experimentarem.
> Escolham entre o Gemini CLI ou o Codex CLI — ambos são gratuitos e funcionam de forma parecida.

> **Pré-requisito para ambos:** Node.js 20 ou superior.
> Quem não tiver, instale pelo site oficial: https://nodejs.org

---

**Opção A — Gemini CLI (Google)**

> **Pré-requisitos:** Node.js 20+ e uma conta Google (gratuita).

Rodar direto sem instalar (recomendado pra aula):

```bash
npx @google/gemini-cli
```

Ou instalar globalmente:

```bash
npm install -g @google/gemini-cli
```

> Na primeira execução, o Gemini CLI pede pra autenticar com sua conta Google.
> Basta seguir o fluxo no navegador — é gratuito, não precisa de cartão.

---

**Opção B — Codex CLI (OpenAI)**

> **Pré-requisitos:** Node.js 22+ e uma conta ChatGPT (Free funciona).

Instalar globalmente:

```bash
npm install -g @openai/codex
```

> Na primeira execução, rode `codex` e ele vai pedir pra autenticar.
> Pode usar sua conta do ChatGPT (Plus, Pro, Edu ou Free) — não precisa de API key.
> Por tempo limitado, funciona grátis mesmo no plano Free.

---

**Tarefa: Criar um site portfolio pessoal simples com cada agente**

> O instrutor cria 3 pastas no Windows Explorer: `portfolio-gemini`, `portfolio-codex` e `portfolio-claude`.
> Depois abre o Git Bash dentro de cada pasta e inicia o agente correspondente.
> O objetivo e mostrar os 3 agentes rodando o mesmo prompt lado a lado,
> pra turma comparar como cada um trabalha.

**Terminal 1 — Gemini CLI:** abra o Git Bash na pasta `portfolio-gemini` e rode `npx @google/gemini-cli`

**Terminal 2 — Codex CLI:** abra o Git Bash na pasta `portfolio-codex` e rode `codex`

**Terminal 3 — Claude Code:** abra o Git Bash na pasta `portfolio-claude` e rode `claude`

> Cole o mesmo prompt nos 3 agentes:

```
Crie um site portfolio pessoal simples com HTML, CSS e JavaScript puro (sem frameworks).

O site deve ter:
- Uma pagina unica com scroll suave entre secoes
- Secao "Sobre mim" com nome, foto placeholder e uma breve descricao
- Secao "Habilidades" com uma lista visual (barras de progresso ou cards)
- Secao "Projetos" com 3 cards de projetos placeholder (imagem, titulo, descricao e link)
- Secao "Contato" com links para GitHub, LinkedIn e email
- Header fixo com navegacao entre secoes
- Design responsivo e moderno (cores escuras, tipografia limpa)
- Animacoes suaves de entrada nas secoes (CSS only)

Meu nome: Wesley Eduardo
Meu curso: Sistemas de Informacao

Crie todos os arquivos e me diga como abrir no navegador.
```

> **Tempo:** ~15-20min. Deixe os alunos acompanharem os 3 agentes trabalhando.
> Compare os resultados: qual terminou primeiro? Qual gerou o visual mais bonito?
> Qual seguiu melhor as instrucoes?
>
> Incentive os alunos a iterarem no terminal deles: "Pede pra mudar a cor",
> "Pede pra adicionar modo escuro", "Pede pra colocar seus dados reais".
>
> **Reflexao (apos a comparacao):** A logica e a mesma independente da ferramenta.
> Voce descreve bem o que quer, o agente constroi, e voce revisa e itera.
> Isso vale pro Claude Code, pro Gemini CLI, pro Codex CLI — pra qualquer agente de codificacao.

---

**Ollama — IA local (bonus):**

> Alem de ferramentas na nuvem, existe o **Ollama** — ele roda modelos de IA direto na sua maquina,
> sem internet e sem depender de nenhum servico externo. Modelos como Llama, Mistral, CodeGemma, etc.
> Site: https://ollama.com
>
> Exemplo rapido (com o Ollama instalado e rodando):

```bash
ollama run codellama "Escreva uma funcao em Java que recebe uma lista de numeros inteiros e retorna apenas os numeros primos"
```

> Ele processa tudo localmente. E mais lento que as APIs na nuvem, mas e gratuito,
> offline e privado. Boa opcao pra experimentar no proprio PC.
>
> **Enquanto o Ollama roda o prompt:** abra o Gerenciador de Tarefas (Ctrl+Shift+Esc)
> e mostre o consumo de CPU, RAM e GPU em tempo real. Os alunos precisam ver o impacto
> de rodar um modelo localmente — isso deixa claro por que precisa de uma máquina razoável
> e por que as APIs na nuvem são mais rápidas (o processamento pesado fica no servidor deles).

---



## MÓDULO 04 — Features do Claude Code + Iterações (se der tempo)

> Já vimos o MCP do Playwright em ação testando a V1. Agora vamos explorar mais:
> MCP conectando ao banco de dados, Skills e Subagents.
> Isso completa a visão do que a ferramenta oferece.
> Depois, partimos pras iterações técnicas no projeto — melhorias isoladas que replicam o dia a dia de um dev.

---

### Iteração 1 — MCP: Conectando o Claude Code ao PostgreSQL

> Vocês já viram o MCP em ação com o Playwright (testando pelo browser).
> Agora vamos conectar outro MCP: direto ao **banco de dados PostgreSQL**.
> Em vez de copiar e colar queries SQL, o agente acessa o banco direto.
>
> **Aprofundamento fica pra próxima data** — aqui vamos só conectar e testar.

**Explicação rápida (~3min):**

> Mostre o conceito pros alunos:
> - MCP = ponte entre o agente e ferramentas externas
> - O agente ganha "superpoderes" — consegue consultar banco, ler issues do GitHub, acessar Slack, etc.
> - Existem centenas de servidores MCP prontos: PostgreSQL, GitHub, Sentry, Notion, etc.
> - Documentação oficial: https://code.claude.com/docs/en/mcp

**Conectar ao PostgreSQL do projeto (o banco já está rodando via Docker):**

> No terminal (fora do Claude Code), rode:

```bash
claude mcp add --transport stdio db -- cmd /c npx -y @bytebase/dbhub --dsn "postgresql://postgres:postgres@localhost:5432/ru_ifma"
```

> **Nota Windows:** O `cmd /c` antes do `npx` é obrigatório no Windows para o MCP funcionar.

> Verifique se conectou. Dentro do Claude Code, digite:

```
/mcp
```

> Deve mostrar o servidor `db` como ativo (junto com o `playwright` que já tínhamos).

**Testar — peça pro Claude consultar o banco:**

```
Usando o MCP do PostgreSQL, me mostra:
1. Quantos cardápios estão cadastrados no banco
2. A estrutura das tabelas (quais colunas cada uma tem)
3. Os dados do admin padrão
```

> **Reflexão:** O agente está fazendo SELECT direto no banco, sem a gente escrever
> uma linha de SQL. Ele enxerga o banco como se fosse parte dele.
> Com dois MCPs (banco + browser) o agente tem uma visão completa do sistema:
> ele lê o código, consulta os dados e navega na interface.
> Agora imaginem isso conectado ao banco de produção em modo leitura. O que vocês fariam?

**Commit:** não precisa — MCP é configuração local, não vai pro repositório.

---

### Iteração 2 — Skills: Comandos personalizados no Claude Code

> **O que são Skills?**
>
> Skills são instruções reutilizáveis que você cria pra ensinar o Claude a fazer tarefas
> específicas do seu jeito. Funciona como um slash command personalizado:
> você digita `/nome-da-skill` e o Claude segue as instruções que você definiu.
>
> **Aprofundamento fica pra próxima data** — aqui vamos criar uma skill simples e testar.

**Explicação rápida (~3min):**

> Mostre o conceito pros alunos:
> - Skill = arquivo Markdown com instruções que o Claude segue
> - Pode ser invocada manualmente (`/nome`) ou automaticamente (o Claude decide quando usar)
> - Ficam em `.claude/skills/nome-da-skill/SKILL.md`
> - Pessoais (`~/.claude/skills/`) valem pra todos os projetos
> - Do projeto (`.claude/skills/`) valem só pro projeto e podem ser commitadas
> - Documentação oficial: https://code.claude.com/docs/en/skills

**Criar uma skill de revisão de código:**

```
Crie uma skill de revisão de código no projeto.

Crie o arquivo .claude/skills/revisar/SKILL.md com o seguinte conteúdo:

---
name: revisar
description: Revisa código buscando problemas de qualidade, segurança e boas práticas. Use quando pedirem para revisar código ou após mudanças significativas.
allowed-tools: Read, Grep, Glob
---

Ao revisar código, siga esta checklist:

1. Segurança: dados sensíveis expostos, SQL injection, XSS
2. Qualidade: código duplicado, funções muito longas, nomes ruins
3. Boas práticas: tratamento de erros, validação de entrada
4. Performance: queries N+1, loops desnecessários, objetos grandes em memória

Para cada problema encontrado, informe:
- Arquivo e linha
- Severidade (critico, alerta, sugestão)
- O que está errado
- Como corrigir (com exemplo de código)
```

**Testar a skill:**

```
/revisar
```

> O Claude vai analisar o código do projeto seguindo a checklist que definimos.
>
> **Reflexão:** A skill é só um arquivo Markdown. Dá pra criar skills
> pra qualquer coisa: gerar commits padronizados, rodar deploy, criar componentes
> no padrão do time...

---

### Iteração 3 — Agentes Customizados (Subagents)

> **O que são Agentes Customizados?**
>
> Subagents são agentes especializados que rodam em contexto isolado.
> Cada um tem seu próprio prompt, ferramentas permitidas e até modelo.
> O Claude principal delega tarefas pra eles quando faz sentido.
>
> **Aprofundamento fica pra próxima data** — aqui vamos criar um agente simples e testar.

**Explicação rápida (~3min):**

> Mostre o conceito e a diferença:
>
> | | Skill | Subagent |
> |---|---|---|
> | **Onde roda** | No contexto da conversa principal | Em contexto isolado (próprio) |
> | **Pra que serve** | Instruções/checklists que o Claude segue | Tarefas complexas e independentes |
> | **Ferramentas** | Usa as mesmas da conversa | Pode ter ferramentas restritas |
> | **Modelo** | Mesmo da conversa | Pode usar modelo diferente (ex: Haiku pra tarefas rápidas) |
> | **Quando usar** | Padrões, convenções, comandos rápidos | Pesquisa pesada, revisão isolada, testes |

**Resumo visual:**

```
  ┌───────────────┬────────────────────────────┬──────────────────────────────────────┐
  │               │           Skills           │               Agentes                │
  ├───────────────┼────────────────────────────┼──────────────────────────────────────┤
  │ Como chama    │ /nome (explicito)          │ Linguagem natural (o Claude decide)  │
  ├───────────────┼────────────────────────────┼──────────────────────────────────────┤
  │ Quem controla │ Voce                       │ O Claude delega                      │
  ├───────────────┼────────────────────────────┼──────────────────────────────────────┤
  │ Contexto      │ Roda na conversa principal │ Roda em contexto isolado (subagente) │
  └───────────────┴────────────────────────────┴──────────────────────────────────────┘
```

> - Documentação oficial: https://code.claude.com/docs/en/sub-agents

**Criar um agente de documentação:**

> Dentro do Claude Code, digite:

```
/agents
```

> Vai abrir o menu de agentes. Siga os passos:
>
> 1. Selecione **"Create new agent"**
> 2. Escolha **"Project-level"** (fica no projeto, pode ser commitado)
> 3. Selecione **"Generate with Claude"**
> 4. Descreva o agente:

```
Um agente que analisa o código do projeto e gera documentação técnica.
Ele deve ler os controllers, services e entidades do backend,
e gerar um resumo em Markdown com: endpoints disponíveis,
entidades e seus campos, e fluxos principais da aplicação.
Ele só precisa de acesso de leitura.
```

> 5. Na seleção de ferramentas, marque apenas **Read-only tools**
> 6. Modelo: **Haiku** (rápido e barato pra tarefas de leitura)
> 7. Escolha uma cor e salve

**Testar o agente:**

```
Use o agente de documentação para gerar a documentação técnica do backend.
```

> O Claude vai delegar a tarefa pro subagent, que roda isolado e retorna o resultado.
>
> **Reflexão:** O agente rodou em contexto separado. Ele não poluiu a conversa
> principal com toda a análise. Fez o trabalho pesado isolado e trouxe só o resultado.
>
> **Resumo da diferença pra fechar:**
> - **Skill** = receita que o Claude segue na conversa atual (tipo um checklist)
> - **Subagent** = assistente especializado que trabalha isolado e traz o resultado
> - **MCP** = ponte com ferramentas externas (banco, GitHub, Slack...)
> - Os três se complementam e podem ser combinados

---

> **Transição:** Agora temos o kit completo do Claude Code: plan mode, memória com CLAUDE.md,
> plugins, MCP, skills e subagents. Antes de voltar pro código, vamos ver um exemplo real
> de software que usa IA — pra mostrar que agentes não servem só pra construir, mas também
> podem ser parte do produto.

---

### Iteração 4 — Demo: Argonaut — IA dentro de um software real

> **O que é:** Uma demonstração rápida de um projeto real construído com agentes de IA,
> mostrando que a IA pode ser **parte** do software, não só a ferramenta que constrói.

> **Abra o Argonaut no navegador e demonstre rapidamente.**
>
> O Argonaut é uma interface de chat que gerencia deploys no Kubernetes via ArgoCD.
> Em vez de decorar comandos e navegar painéis, o usuário conversa com uma IA
> (Claude, OpenAI ou Gemini) e ela traduz os pedidos em chamadas reais pra API do ArgoCD.
>
> Exemplos: "sincroniza a aplicação X", "qual o status do deploy?", "lista as apps com erro".
>
> O ponto aqui é: durante o curso a gente usou IA como ferramenta de terminal pra **construir** software.
> Mas a IA também pode ser **parte** do software — embutida no produto, resolvendo problemas reais do usuário.
>
> Stack: Next.js 16, React 19, TypeScript, Prisma com SQLite, streaming via SSE,
> criptografia AES-256-GCM pras credenciais, multi-tenant com isolamento por usuário.
>
> Tudo isso foi construído com a ajuda de agentes de codificação — o mesmo fluxo que vocês viram hoje.
> Agentes construindo software que usa agentes. Pensem nisso.

> **Reflexão:** A gente viu o agente construindo código. Agora vimos um software onde a IA
> é o produto. Essa é a diferença entre usar IA como ferramenta e usar IA como feature.
> As duas coisas se complementam — e vocês já sabem fazer as duas.

---

> **Transição:** Agora vamos voltar pro projeto e iterar em cima da V1 —
> cada prompt abaixo é uma melhoria isolada, como no dia a dia de um dev.

---

### Iteração 5 — Adicionar Swagger/OpenAPI no backend

> **O que é:** Documentação interativa da API. Qualquer dev que acessar `/swagger-ui.html` consegue ver e testar todos os endpoints.

```
Adicione o Swagger/OpenAPI no backend (ru-ifma-backend/).

- Use a dependência springdoc-openapi
- Configure para gerar a documentação automaticamente a partir dos controllers
- Os endpoints públicos e admin devem aparecer separados por tags
- A página do Swagger deve estar acessível em /swagger-ui.html
- Adicione descrições nos endpoints e nos DTOs

Teste se compila e se o Swagger abre no navegador.
```

> Depois de pronto, abra `http://localhost:8080/swagger-ui.html` e mostre pros alunos.
>
> **Commit:**

```
Faça o commit dessa alteração no ru-ifma-backend/ com a mensagem "feat: adiciona Swagger/OpenAPI" e push.
```

---

### Iteração 6 — Adicionar logs estruturados no backend

> **O que é:** Logs que registram o que está acontecendo na aplicação. Essencial pra debugar problemas em produção.

```
Adicione logs estruturados no backend (ru-ifma-backend/).

- Use SLF4J (já vem com Spring Boot)
- Adicione logs nos controllers: log de entrada em cada endpoint com os parâmetros recebidos
- Adicione logs nos services: log de operações importantes (criar, editar, excluir cardápio/usuario)
- Adicione log de autenticação: login bem-sucedido, login falhou, credenciais inválidas
- Use os níveis corretos: INFO para operações normais, WARN para tentativas inválidas, ERROR para exceções
- Configure o formato do log no application.yml com timestamp, nível, classe e mensagem

Teste se os logs aparecem no console quando você faz operações.
```

> Mostre os logs no terminal enquanto faz operações na aplicação.
>
> **Commit:**

```
Faça o commit no ru-ifma-backend/ com a mensagem "feat: adiciona logs estruturados com SLF4J" e push.
```

---

### Iteração 7 — Adicionar validações com Bean Validation

> **O que é:** Validação automática dos dados que chegam na API. Impede que dados inválidos entrem no sistema.

```
Adicione validações com Bean Validation no backend (ru-ifma-backend/).

- Nos DTOs de request, adicione as anotações de validação:
  - Cardápio: data obrigatória, tipo de refeição obrigatório, prato principal obrigatório (mínimo 3 caracteres)
  - Usuário: email válido e obrigatório, senha obrigatória (mínimo 6 caracteres), nome obrigatório
- Nos controllers, adicione @Valid nos parâmetros @RequestBody
- Crie um handler global de exceções (@RestControllerAdvice) que retorne erros de validação em formato padronizado:
  {
    "status": 400,
    "errors": [{"campo": "email", "mensagem": "Email é obrigatório"}]
  }

Teste enviando dados inválidos pelo Swagger e verifique se as mensagens de erro aparecem corretamente.
```

> Mostre no Swagger: envie um cardápio sem prato principal e mostre o erro formatado.
>
> **Commit:**

```
Faça o commit no ru-ifma-backend/ com a mensagem "feat: adiciona Bean Validation nos DTOs" e push.
```

---

### Iteração 8 — Adicionar paginação nos endpoints de listagem

> **O que é:** Quando o sistema tiver muitos cardápios, não dá pra retornar tudo de uma vez. Paginação retorna os dados em blocos.

```
Adicione paginação nos endpoints de listagem do backend (ru-ifma-backend/).

- GET /api/admin/cardapios → retornar paginado (page, size, sort)
- GET /api/admin/usuarios → retornar paginado (page, size, sort)
- Use Pageable do Spring Data
- Retorne no formato padrão do Spring (content, totalElements, totalPages, etc.)
- Valores padrão: page=0, size=10, sort=data,desc para cardápios

Atualize o frontend (ru-ifma-frontend/) para usar paginação nas telas de listagem admin:
- Mostrar botões de "Anterior" e "Próximo"
- Mostrar "Página X de Y"
```

> **Commit:**

```
Faça o commit no backend com "feat: adiciona paginação nos endpoints de listagem" e no frontend com "feat: adiciona paginação nas telas admin". Push em ambos.
```

---

### Iteração 9 — Melhorar o frontend (loading states e tratamento de erros)

> **O que é:** UX básica — mostrar loading enquanto carrega e mensagens de erro quando algo falha.

```
Melhore a experiência do usuário no frontend (ru-ifma-frontend/).

- Adicione loading spinners em todas as telas que fazem chamadas à API
- Adicione tratamento de erros:
  - Se a API estiver fora do ar → mensagem "Não foi possível conectar ao servidor"
  - Se o login falhar → mensagem "Email ou senha incorretos"
  - Se uma operação CRUD falhar → mensagem específica do erro
- Adicione mensagens de sucesso (toast/notificação) ao criar, editar ou excluir um cardápio
- Use um componente de Toast/Notification simples (pode criar um próprio com Tailwind, não precisa de lib externa)
```

> **Commit:**

```
Faça o commit no ru-ifma-frontend/ com a mensagem "feat: adiciona loading states e tratamento de erros" e push.
```

---

### Iteração 10 — Adicionar testes unitários no backend

> **O que é:** Testes automatizados que garantem que o código funciona como esperado.

```
Crie testes unitários no backend (ru-ifma-backend/).

- Testes para o CardapioService:
  - Criar cardápio com sucesso
  - Não permitir dois cardápios com mesma data e tipo de refeição
  - Buscar cardápios por data
  - Editar cardápio existente
  - Excluir cardápio

- Testes para o UsuarioService:
  - Criar usuário com sucesso
  - Não permitir email duplicado
  - Validar credenciais corretas (login)
  - Rejeitar credenciais inválidas (login)

- Use JUnit 5 + Mockito
- Rode os testes com: mvn test
```

> Rode `mvn test` e mostre os resultados pros alunos.
>
> **Commit:**

```
Faça o commit no ru-ifma-backend/ com a mensagem "test: adiciona testes unitários para services" e push.
```

---

### Sugestões de iterações extras (se sobrar tempo)

> Escolha conforme o tempo e interesse dos alunos:

**Modo escuro no frontend:**
```
Adicione modo escuro no frontend (ru-ifma-frontend/).
Use a estratégia de classe do Tailwind (dark:).
Adicione um botão de toggle no header.
Salve a preferência no localStorage.
```

**Exportar cardápio semanal em PDF:**
```
Adicione um endpoint GET /api/cardapios/semana/pdf que gera um PDF
com o cardápio da semana atual. Use a lib OpenPDF ou iText.
Adicione um botão "Baixar PDF da semana" na página pública do frontend.
```

**Filtro por tipo de refeição na página pública:**
```
Na página pública do frontend, adicione tabs ou botões para filtrar
entre "Almoço" e "Jantar" ao invés de mostrar os dois juntos.
```

**Health check endpoint:**
```
Adicione um endpoint GET /api/health que retorna o status da aplicação
e a conectividade com o banco. Use Spring Boot Actuator.
```

---

## MÓDULO 05 — Finalização

> ~20min | READMEs + CLAUDE.md final + Encerramento

---

### Passo 14 — Criar os READMEs

```
Crie os READMEs do projeto:

1. README.md em ru-ifma-backend/ com:
   - Descrição do projeto (API do RU IFMA)
   - Tecnologias (Spring Boot 4.0.3, Java 21, PostgreSQL 17 via Docker, Basic Auth, Swagger)
   - Pré-requisitos (Java 21, Maven, Docker)
   - Como rodar (docker compose up -d pro banco, backend pelo IntelliJ)
   - Endpoints da API documentados (ou link pro Swagger)
   - Credenciais padrão do admin
   - Como rodar os testes

2. README.md em ru-ifma-frontend/ com:
   - Descrição do projeto (Frontend do RU IFMA)
   - Tecnologias (React, Vite, TailwindCSS, Axios)
   - Pré-requisitos (Node 18+)
   - Como rodar (npm install, npm run dev)
   - Descrição das telas
   - Screenshots (placeholder — "TODO: adicionar screenshots")

Faça o commit em cada repo e push.
```

---

### Passo 15 — Atualizar os CLAUDE.md com o estado final

```
Atualize os 3 arquivos CLAUDE.md com o estado final do projeto:

- Status: "Projeto completo e funcional"
- Liste todas as features implementadas (V1 + iterações)
- Anote as decisões técnicas tomadas durante a implementação
- Liste possíveis melhorias futuras

Faça o commit em cada repo e push.
```

---

### Passo 16 — Encerramento e recapitulação

> Vamos recapitular o que fizemos hoje.

**Recapitulação (~5min):**

> Passe rapidamente por cada etapa mostrando o resultado:
>
> 1. Instalamos e configuramos o Claude Code do zero
> 2. Usamos o plan mode pra arquitetar o sistema antes de escrever uma linha de código
> 3. Criamos arquivos de memória (CLAUDE.md) como checkpoint do projeto
> 4. O agente construiu sozinho: backend com Spring Boot, frontend com React, banco com Docker
> 5. Testamos, debugamos e corrigimos bugs — com o próprio agente
> 6. Vocês criaram um portfólio pessoal usando o Gemini CLI ou Codex CLI
> 7. Fizemos deploy na Vercel e Railway — saiu do localhost pro mundo
>
> "Tudo isso em uma aula. Sem o agente, isso levaria dias."

**Pontos-chave pra levar pra casa (~3min):**

> - O agente não substitui o dev — ele **acelera**. Vocês ainda precisam saber o que estão pedindo e revisar o que ele entrega.
> - Descrever bem o que você quer é a habilidade mais importante. Prompt ruim = código ruim.
> - Sempre revise o código gerado. O agente erra, alucina e às vezes ignora instruções.
> - Comece com projetos pessoais pra pegar prática antes de usar no trabalho.

**Recursos pra continuar (~2min):**

> Compartilhe no chat:
>
> - Claude Code: https://docs.anthropic.com/en/docs/claude-code
> - Gemini CLI: https://github.com/google-gemini/gemini-cli
> - Codex CLI: https://github.com/openai/codex
> - Os dois repositórios do projeto que construímos (compartilhe os links do GitHub)

**Perguntas finais:**

> Abra espaço pra perguntas no chat/microfone.
> Se ninguém perguntar, provoque: "Se vocês pudessem pedir pro agente construir
> qualquer coisa agora, o que seria?"

---

## Comandos úteis do Claude Code (referência rápida)

| Comando | O que faz |
|---------|-----------|
| `claude` | Inicia o Claude Code no diretório atual |
| `/init` | Gera um CLAUDE.md baseado no projeto |
| `/doctor` | Verifica se está tudo ok com a instalação |
| `/help` | Lista comandos disponíveis |
| `/compact` | Compacta o contexto da conversa (útil em sessões longas) |
| `/clear` | Limpa o histórico da conversa |
| `#` seguido de texto | Adiciona uma anotação ao CLAUDE.md |
| `Shift+Tab` | Alterna entre modo plan e modo execução |
| `Esc` | Cancela/interrompe o que o Claude está fazendo |
| `Ctrl+C` | Sai do Claude Code |

---

## Dicas para a demonstração

1. **O agente vai errar** — use cada erro como oportunidade de ensino
2. **Comente em voz alta** — enquanto o Claude executa, explique o que ele está fazendo
3. **Use `/compact`** — em sessões longas o contexto fica grande, compactar ajuda
4. **Se travar, não se assuste** — é pra isso que criamos os CLAUDE.md, mostre como retomar
5. **Discuta limitações** — o Claude não testa visualmente, pode gerar código que não compila de primeira, e às vezes ignora instruções
6. **Tenha o Docker Desktop rodando** — o banco sobe via `docker compose up -d` dentro do backend
7. **Tenha os repos já criados no GitHub** — economiza tempo durante a demo
8. **Se precisar cancelar algo, use `Esc`** — cancela sem perder o contexto
9. **Cada iteração é independente** — se o tempo apertar, pule iterações sem prejuízo
10. **Commite após cada iteração** — mostra disciplina e cria pontos de retorno
11. **Tenha um branch `backup` pronto** — com o projeto V1 funcionando, pré-construído. Se algo grave travar a demo ao vivo por muito tempo, faça checkout do backup e siga sem perder a plateia. Nunca revele que é backup — diga "vou pular pra versão estável pra gente seguir"

---
*Roteiro criado para o Mini Curso de IA para Devs — IFMA 2026*
