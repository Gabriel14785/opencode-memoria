# Lições Aprendidas

Cada sessão com o opencode gera aprendizado. Aqui registro tudo que pode ser relevante no futuro.

## Formato
```
## YYYY-MM-DD — Título
- Contexto:
- Ação:
- Resultado:
- Lição:
```

---

## 2026-05-19 — Configuração inicial do opencode
- **Contexto:** Gabriel queria instalar o ruflo, mas ele só funciona no Claude Code
- **Ação:** Instalamos e depois removemos o ruflo; criamos repositório de memória próprio
- **Resultado:** Repositório `opencode-memoria` no GitHub com preferências, projetos e decisões
- **Lições:**
  - Ruflo/Claude Flow só funciona no ecossistema Claude Code
  - Paperclip é um orquestrador de agentes, não um plugin
  - opencode suporta `instructions` remotos via `opencode.json`

## 2026-05-19 — Stack do Gabriel
- **Contexto:** Identificamos as stacks dos projetos
- **Lições:**
  - Projetos novos: pnpm + TypeScript + Vitest
  - Projetos existentes (Nuxt): npm
  - Desktop cheio de projetos em andamento
  - Usa Supabase como backend (dashboard_financeiro_def)

## 2026-05-19 — Descobertas do Gabriel sobre stack
- **Contexto:** Gabriel contou mais sobre o que faz
- **Lições:**
  - Faz landing pages pra pessoas do bairro
  - Landing simples: HTML + CSS + JS puro
  - Landing complexas: Next.js
  - Próximo projeto grande: microsaas para clínica estética (agenda, estoque, finanças)
  - Prefere Tailwind CSS pra estilização

## 2026-05-19 — Lint e testes
- **Contexto:** Configuramos ESLint + Vitest em dois projetos
- **Lições:**
  - Nuxt 3 usa `@nuxt/eslint` com flat config
  - Projetos novos usar `@eslint/js` + `typescript-eslint` diretamente
  - Sempre rodar `lint` e `typecheck` antes de finalizar
  - `no-explicit-any` virou warn em projetos existentes (migração gradual)

## 2026-05-19 — Projeto da clínica em funcionamento
- **Contexto:** Criamos o microsaas da clínica estética do zero
- **Ações:**
  - Projeto Next.js 16 + Supabase + Drizzle ORM + Tailwind + pnpm
  - Migrations com 9 tabelas + enums
  - Auth com login/register
  - Módulo de Agenda (listar, criar, status)
  - Setup inicial (profissionais, serviços, clientes)
- **Lições:**
  - Supabase MCP não troca de projeto no meio da sessão
  - Server Actions com `form action` direto funcionam bem pra CRUD simples
  - Importante tratar `clinica_id` em toda query para multi-tenant

## 2026-05-19 — Módulos Estoque e Finanças concluídos
- **Contexto:** Gabriel pediu para criar os módulos restantes do microsaas
- **Ações:**
  - Estoque: CRUD de produtos + movimentação (entrada/saída) + alerta de estoque baixo
  - Finanças: CRUD de transações + resumo mensal (receitas/despesas/saldo)
  - Validação de estoque insuficiente antes de dar saída
- **Resultado:** Sistema completo com 10 rotas, 3 módulos funcionais
- **Lições:**
  - Manter consistência nos imports entre pastas de rota (subpastas vs nível atual)

## 2026-05-19 — Reforma visual + módulo Clientes
- **Contexto:** Gabriel pediu visual mais bonito e clientes estava quebrado
- **Ações:**
  - Design system completo (cores, botões, inputs, cards, badges)
  - Nova sidebar com ícones e hover roxo
  - Login/Register com tela dividida e gradiente
  - Página de Clientes criada (listar, criar, excluir)
  - Setup, Agenda, Estoque, Finanças atualizados visualmente
- **Resultado:** 12 rotas, visual profissional, tudo funcional

## 2026-05-19 — Melhorias finais
- **Contexto:** Gabriel pediu toast com som, loading, migrations e integração estoque→finanças
- **Ações:**
  - Toast com notificação sonora (beep suave) em todo o sistema
  - Loading state com SubmitButton + spinner
  - Drizzle migrations versionadas geradas do schema
  - Movimentação de estoque cria despesa automaticamente nas finanças
  - Rota de procedimento com desconto em R$ e %
- **Status final:** 13 rotas, RLS configurado, MCP novo projeto, skills instaladas

## 2026-05-19 — Sessão intensiva de design e UX
- **Contexto:** Gabriel pediu melhorias visuais e de experiência em várias telas
- **Ações:**
  - Agenda: calendário mensal com cores por status (fundo do dia), lista com cards por período
  - Novo agendamento: formulário passo a passo com seleção visual
  - Procedimento: abas (produtos/financeiro), grid de seleção, desconto R$ e % sincronizados
  - Clientes: grid de cards com gradientes, página de detalhes (próximos + histórico)
  - Setup: simplificado, só profissional
  - Sidebar: destaque na aba ativa com `usePathname`
  - Serviços: página de editar com vínculo de produtos
  - Migrations: `servico_produtos` + `agendamento_id` em movimentações
- **Resultado:** 16 rotas, design consistente, UX melhorada
- **Lições:**
  - Chaves React (`key`) precisam incluir mês pra evitar stale closures em calendário
  - Para estado compartilhado entre Server Action e client, retornar `{success/error}` em vez de throw
  - Links dentro de `<a>` precisam de `e.preventDefault()` no botão interno

## 2026-05-20 — Security Hardening Fase 1
- **Contexto:** Auditoria de segurança revelou 17 vulnerabilidades críticas (IDOR, cross-tenant, RLS)
- **Ações:**
  - Criada camada central de segurança (`lib/auth/guards.ts`): requireUser, getClinicaId, requireSuperAdmin
  - 12 arquivos de Server Actions corrigidos com .eq("clinica_id", clinicaId) em TODAS as queries
  - Admin actions com requireSuperAdmin() em todas as 8 funções
  - Mensagens de erro genéricas no login/register
  - Removido todos os `data!` e substituídos por validação segura
  - Correção de `resetarSenha` com redirectTo apontando pro app, não Supabase
- **Resultado:** 0 build errors, 17 vulnerabilidades críticas resolvidas
- **Lições:**
  - Server Actions são endpoints HTTP — middleware protege páginas mas não actions
  - Sempre usar camada central de auth, nunca confiar só no middleware
  - `.eq("clinica_id", clinicaId)` é obrigatório em toda query multi-tenant

---

## 2026-05-22 — Sistema de Convites (Invite-Only Auth)
- **Contexto:** Fluxo de cadastro público era inseguro: signUp direto causava usuários órfãos, sem clínica, enumeração de emails
- **Ação:**
  - Criada tabela `user_invites` com token criptográfico, expiração 24h, vinculado à clínica
  - Removido `signUp` público; página `/register` → redirect `/login`
  - `inviteUser()` → gera link único; `acceptInvite()` → cria conta via service_role
  - OAuth Google para app de produção
- **Resultado:** Sistema enterprise-grade, invite-only, sem cadastro público
- **Lições:**
  - `supabase.auth.admin.createUser()` com service_role é o jeito correto de criar usuários server-side
  - `window.location.href` é mais confiável que `router.push()` pós Server Action (evita "Router action before initialization")
  - `randomBytes(32).toString("hex")` para tokens seguros
  - Sempre confirmar email via `email_confirm: true` ao criar usuário via admin API

## 2026-05-22-24 — Auditoria de Performance Completa
- **Contexto:** Sistema estava lento — múltiplas causas raiz
- **Ação:**
  - **16 indexes FK** criados: todas as `clinica_id`, `agendamento_id`, `produto_id` etc.
  - **40+ RLS policies** otimizadas: `auth.uid()` → `(select auth.uid())` (subquery avaliada 1x por query, não por linha)
  - **N+1 queries resolvidas:** `finalizarProcedimento` (3N → 3), `criarServico/atualizarServico` (N → 1 bulk insert)
  - **`getDashboard`:** 5 queries sequenciais → `Promise.all` paralelo
  - **`relatorioFinanceiro`:** 12 queries (1/mês) → 1 query do ano + agrupamento em JS
  - **`guards.ts`:** `createClient()` duplicado removido (requireSuperAdmin reusa getClinicaId)
  - **`loading.tsx`** adicionado em dashboard, agenda, admin
- **Resultado:** Ganho de 3-12x em várias operações
- **Lições:**
  - RLS `auth.uid()` inline é reavaliado por linha — sempre usar `(select auth.uid())` subquery
  - Índices FK são o maior quick win de performance em sistemas multi-tenant
  - `select("*")` em 4 tabelas é desnecessário — colunas específicas + `*` só nos joins aninhados
  - **NÃO usar RLS com subquery auto-referenciada** (ex: `SELECT clinica_id FROM usuarios WHERE id = auth.uid()` dentro da própria policy de `usuarios`) — causa redirect loop no middleware. Usar `auth.users` como tabela de referência
  - RLS permissiva (`USING (true) TO authenticated`) é aceitável quando segurança é feita no código com `.eq("clinica_id", clinicaId)`

## 2026-05-24 — Admin Panel Melhorado
- **Contexto:** Gabriel pediu visual do painel admin mais completo
- **Ação:**
  - Nome do super_admin logado no dashboard
  - Alertas visuais de trials expirados (vermelho) e ativos (amarelo)
  - Cards de clínica mostram: trial badge, usuários por nome+role, badge "Permanente"
  - Busca/filtro de clínicas por nome
  - Botão 🗑 Excluir clínica (com validação: não exclui se tem usuários/agendamentos)
- **Resultado:** Admin profissional, visão completa do estado de cada clínica
- **Lições:**
  - `typeof corDia !== "string"` para checar tipos union em retorno condicional
  - Sempre validar exclusão antes de deletar (contar usuários, agendamentos vinculados)

## 2026-05-24 — Google Calendar Integration
- **Contexto:** Conectar agenda do app com Google Calendar dos profissionais
- **Ação:**
  - Tabela `google_tokens` vinculada a `profissional_id`
  - OAuth 2.0 via Google Cloud Console (Web Application)
  - `lib/google/auth.ts` — getAuthUrl, exchangeCode, refreshAccessToken
  - `lib/google/calendar.ts` — createEvent, updateEvent, deleteEvent (fetch direto, sem lib pesada)
  - Callback em `/api/auth/google/callback`
  - Sincronização em `criarAgendamento` (cria evento) e `atualizarStatusAgendamento` (cancela → deleta evento)
  - Botão "🔗 Conectar Google Calendar" por profissional no `/setup`
- **Resultado:** Eventos sincronizados automaticamente
- **Lições:**
  - Google OAuth precisa de "Usuários de teste" adicionados na Tela de Consentimento OAuth antes de funcionar
  - `fetch` direto à Google Calendar API é suficiente — não precisa de `googleapis` (evita dependência pesada)
  - Sempre pedir `access_type: "offline"` e `prompt: "consent"` pra ter refresh_token
  - Callback OAuth é uma Route Handler em `/app/api/.../route.ts` — funciona com `export async function GET`

## 2026-05-24-25 — Tipos de Produto e Revenda
- **Contexto:** Separar produtos usados no paciente (cera) de produtos de uso geral (álcool) e revenda (creme)
- **Ação:**
  - Coluna `tipo` em `produtos`: `procedimento`, `geral`, `revenda`
  - Coluna `modulo_revenda` em `clinicas` (ativado pelo admin)
  - `ProdutoForm`: dropdown de tipo dinâmico; se `geral`, esconde preço de venda
  - `Procedimento`: filtra `tipo = procedimento` (não polui com álcool/papel)
  - `movimentarEstoque`: se produto `revenda` e saída → gera receita (não despesa)
  - Página dedicada `/revenda/vender` com busca de cliente, adição de produtos, desconto, total
  - Integração com procedimento: checkbox "🛍️ Vender produtos após finalizar" → cliente pré-selecionado na revenda
- **Resultado:** Três tipos de produto com regras de negócio distintas, tudo funcionando integrado
- **Lições:**
  - `window.location.href` com query params é mais simples que gerenciar estado cross-page
  - Após venda bem-sucedida, limpar estado do form (cliente, itens, desconto) em vez de redirecionar
  - Dropdown condicional baseado em módulo da clínica: buscar `modulo_revenda` no server component e passar como prop

## 2026-05-25 — Agenda: Cores e Conflitos
- **Contexto:** Agenda não mostrava cores dos profissionais nem validava horário duplicado no servidor
- **Ação:**
  - Calendário: dots coloridos por profissional no dia (substitui fundo colorido por status)
  - Cards: barra esquerda colorida com `cor_agenda` do profissional
  - `novo-form`: avatar do profissional com cor real (remove hardcoded âmbar)
  - `criarAgendamento`: overlap check server-side antes do INSERT
  - `atualizarAgendamento`: mesmo check, excluindo próprio id
  - Trava de procedimento futuro: confirmação "Deseja adiantar o procedimento para hoje?"
- **Resultado:** Identificação visual instantânea de qual profissional está em cada slot; sem conflitos de horário
- **Lições:**
  - Overlap de horário: `SELECT WHERE profissional_id = X AND status != 'cancelado' AND data_hora_inicio < fim AND data_hora_fim > inicio`
  - Dots coloridos: `[...new Set(dados.filter(...).map(...))]` para deduplicar cores no calendário
  - Confirm dialog precisa de título e label customizáveis — `confirm(message, { title, actionLabel })` é mais UX que "Confirmar exclusão" genérico

## 2026-05-25 — Dark Mode
- **Contexto:** Gabriel queria modo escuro no sistema
- **Ação:**
  - Script inline no `<head>` pra evitar flash (lê localStorage antes do render)
  - `@custom-variant dark` no Tailwind
  - CSS variables duplicadas em `.dark` com cores invertidas
  - Regras CSS globais `.dark .bg-white { ... }` pra não precisar alterar dezenas de componentes
  - Toggle 🌙 Escuro / ☀️ Claro na sidebar
  - `suppressHydrationWarning` no `<html>`
- **Resultado:** Dark mode completo com zero alterações nos componentes individuais
- **Lições:**
  - CSS variables + regras globais `.dark .classe` são a forma mais rápida de adicionar dark mode sem refatorar cada componente
  - Script inline no `<head>` é essencial pra evitar flash branco
  - Tailwind v4 usa `@custom-variant dark (&:where(.dark, .dark *))`

## 2026-05-25 — Sentry e Monitoramento
- **Contexto:** Conectar Sentry ao projeto e analisar erros
- **Ação:**
  - MCP Sentry configurado via `@sentry/mcp-server` com `SENTRY_ACCESS_TOKEN`
  - Org corrigida: `clinica-estetica` → `lauchpages` (project: `javascript-nextjs`)
  - `router.push` → `window.location.href` em 7 arquivos (eliminou "Router action before init")
  - Hydration em `/clientes`: `<a>` WhatsApp dentro de `<Link>` → `<span>` com `window.open`
  - `next.config.ts` corrigido: `org: "lauchpages"`, `project: "javascript-nextjs"`
- **Resultado:** 0 erros nas últimas 24h; todos os 10 issues antigos resolvidos
- **Lições:**
  - Sentry MCP usa env `SENTRY_ACCESS_TOKEN`, não `--access-token` como arg
  - `<a>` dentro de `<Link>` causa nested `<a>` (HTML inválido) → hydration mismatch
  - `setTimeout(() => router.push(...))` → erro "Router action dispatched before initialization"
  - API do Sentry: `/api/0/organizations/{org}/issues/` com Bearer token
