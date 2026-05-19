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
