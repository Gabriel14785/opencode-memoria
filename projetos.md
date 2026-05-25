# Projetos

## Ativos
- microsaas — Sistema de gestão para clínicas (em C:\Users\Gabriel\Desktop\microsaas)
  - Stack: Next.js 16 + TypeScript + Supabase + Drizzle ORM + Tailwind CSS + pnpm
  - Auth: Invite-only (user_invites) com service_role, OAuth Google Calendar
  - Monitoramento: Sentry (org: lauchpages, project: javascript-nextjs)
  - Módulos: agenda, clientes, serviços, estoque (3 tipos), finanças, relatórios, revenda, setup, admin
  - Rotas: ~28 rotas (agenda, clientes, estoque, finanças, relatórios, revenda, admin, setup)
  - DB: 12 tabelas, 16 indexes FK, RLS otimizada
  - Multi-tenant: clinica_id em toda query, módulos por clínica, trial (15 dias) e permanente
  - Testes: a configurar
  - Lint: ESLint (eslint-config-next)
  - Comandos: `pnpm dev`, `pnpm build`, `pnpm lint`

- dashboard_financeiro_def — Dashboard financeiro (em C:\Users\Gabriel\Desktop\dashboard_financeiro_def)
  - Stack: Nuxt 3 + Vue 3 + TypeScript + Supabase + TailwindCSS
  - Testes: Vitest + @nuxt/test-utils
  - Lint: ESLint + @nuxt/eslint
  - Comandos: `npm run test`, `npm run lint`

## Finalizados
- (adicione aqui projetos finalizados)

## Próximo Projeto
- Landing pages para clientes do bairro
  - Stack: HTML + CSS + JS puro (simples) ou Next.js (complexas)

## Ideias
- Logs de auditoria para admin (ações dentro das clínicas)
- Deploy na Vercel
- Notificações push / email
- App mobile (PWA)
