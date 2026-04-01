# DevRoast

**Cole seu código. Leve um roast.**

O DevRoast é um analisador de qualidade de código que devolve uma nota **brutalmente honesta** de 0 a 10. Envie qualquer trecho, ative o *roast mode* para sarcasmo no máximo e descubra o quão ruim (ou bom) o seu código realmente é.


---

## Visão geral

```mermaid
flowchart LR
  subgraph client["Cliente"]
    UI["Next.js App Router"]
  end
  subgraph api["API"]
    TRPC["tRPC + React Query"]
    AI["Vercel AI SDK"]
  end
  subgraph data["Dados"]
    PG[("PostgreSQL 16")]
  end
  UI --> TRPC
  TRPC --> PG
  TRPC --> AI
  AI -->|"GPT-4o-mini"| OpenAI[(OpenAI)]
```

O fluxo principal de uma análise funciona assim:

```mermaid
sequenceDiagram
  participant U as Usuário
  participant App as App Next.js
  participant API as tRPC
  participant LLM as Modelo (OpenAI)
  participant DB as PostgreSQL

  U->>App: Cola código + idioma + roast mode
  App->>API: mutation roast.create
  API->>LLM: Prompt estruturado (Zod)
  LLM-->>API: Score, itens, sugestão de fix
  API->>DB: Persiste roast + análise
  API-->>App: Resultado
  App-->>U: Nota, feedback e leaderboard
```

---

## Funcionalidades

| Área | O que faz |
|------|-----------|
| **Submissão de código** | Cole um trecho e receba nota instantânea de qualidade (0–10). |
| **Roast mode** | Ativa tom sarcástico na análise. |
| **Análise detalhada** | Itens com severidade `critical`, `warning` e `good`. |
| **Sugestão de fix** | Diff textual de como o código poderia melhorar. |
| **Shame leaderboard** | Ranking dos piores (menor nota) — compare o seu com o resto. |

---

## Stack

| Camada | Tecnologia |
|--------|------------|
| Framework | Next.js 16 (App Router, React Compiler, Turbopack) |
| API | tRPC v11 + TanStack React Query v5 |
| Banco | Drizzle ORM + PostgreSQL 16 |
| IA | Vercel AI SDK + `@ai-sdk/openai` (GPT-4o-mini) |
| Validação | Zod |
| Estilo | Tailwind CSS v4, `tailwind-variants` |
| Qualidade | Biome 2.4 |
| Pacotes | pnpm |

---

## Pré-requisitos

- [Node.js](https://nodejs.org/) (versão compatível com o projeto)
- [pnpm](https://pnpm.io/)
- [Docker](https://www.docker.com/) (apenas para subir o PostgreSQL local)
- Conta **OpenAI** com chave de API para as análises

---

## Como rodar

### 1. Banco de dados (PostgreSQL)

Na raiz do repositório:

```bash
docker compose up -d
```

Isso sobe o Postgres com usuário, senha e banco `devroast` (veja `docker-compose.yml`).

### 2. Variáveis de ambiente

Crie um arquivo `.env.local` na raiz com pelo menos:

| Variável | Obrigatória | Descrição |
|----------|-------------|-----------|
| `DATABASE_URL` | Sim | URL de conexão Postgres (ex.: `postgresql://devroast:devroast@localhost:5432/devroast`) |
| `OPENAI_API_KEY` | Sim | Chave da API OpenAI |

Opcional (integração de formulário):

| Variável | Descrição |
|----------|-----------|
| `HUBSPOT_PORTAL_ID` | ID do portal HubSpot |
| `HUBSPOT_FORM_ID` | ID do formulário HubSpot |

### 3. Dependências e schema

```bash
pnpm install
pnpm db:push
```

Opcional — dados de exemplo:

```bash
pnpm db:seed
```

### 4. Desenvolvimento

```bash
pnpm dev
```

Abra [http://localhost:3000](http://localhost:3000).

### 5. Build de produção

```bash
pnpm build
pnpm start
```

O script `build` aplica o schema (`drizzle-kit push`) antes do `next build`.

---

## Scripts úteis

| Comando | Descrição |
|---------|-----------|
| `pnpm dev` | Servidor de desenvolvimento |
| `pnpm build` | Push do banco + build Next.js |
| `pnpm start` | Servidor de produção |
| `pnpm lint` | Biome (check) |
| `pnpm format` | Biome (check + write) |
| `pnpm db:push` | Sincroniza schema Drizzle com o banco |
| `pnpm db:generate` | Gera migrações |
| `pnpm db:migrate` | Executa migrações |
| `pnpm db:studio` | Drizzle Studio |
| `pnpm db:seed` | Popula dados de seed |

---

## Estrutura do repositório (resumo)

```
specs/          # Especificações de features
src/
  app/          # Rotas e layouts (App Router)
  api/trpc/     # Handler HTTP tRPC
  components/   # Componentes de feature
  ui/           # Primitivos de UI reutilizáveis
  db/           # Schema Drizzle, cliente, seed
  trpc/         # Contexto tRPC, routers, cliente/servidor
  lib/          # Utilitários (IA, integrações, etc.)
```

---

## Licença e créditos

Projeto educacional ligado ao **NLW Rocketseat**. Ajuste a licença aqui se publicar com uma licença explícita no repositório.
Projeto construído durante o evento **NLW** da [Rocketseat](https://rocketseat.com.br)

