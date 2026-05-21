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
  - Agenda: calendário mensal com cores por status (fundo do dia), lista com cards por período, botões visíveis com texto
  - Novo agendamento: formulário passo a passo com seleção visual
  - Procedimento: abas (produtos/financeiro), grid de seleção, desconto R$ e % sincronizados
  - Clientes: grid de cards com gradientes, página de detalhes (próximos + histórico)
  - Setup: simplificado, só profissional
  - Sidebar: destaque na aba ativa com `usePathname`
  - Serviços: página de editar com vinculo de produtos
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
