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
