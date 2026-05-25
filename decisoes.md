# Decisões Técnicas

## Formato
Cada decisão deve seguir:

```
## YYYY-MM-DD - Título
- Contexto: (o que motivou a decisão)
- Decisão: (o que foi decidido)
- Consequências: (impactos positivos/negativos)
```

## Histórico

## 2026-05-22 — Sistema de convites em vez de cadastro público
- Contexto: Cadastro público com `signUp()` causava usuários órfãos, sem clínica, permitia enumeração de emails
- Decisão: Remover `/register`, implementar `user_invites` com token criptográfico. Super admin convida usuários com link único de 24h. `acceptInvite()` cria conta via service_role.
- Consequências: Sistema seguro, sem usuários órfãos. Trade-off: sem self-service (admin precisa convidar manualmente).

## 2026-05-22 — Service Role para Admin Actions
- Contexto: Admin actions falhavam com RLS porque super_admin não tem clinica_id da clínica alvo
- Decisão: Todas as admin actions (`criarClinica`, `editarClinica`, `toggleClinica`, `listarClinicas`, `listarLogs`) usam `createAdminClient()` (service_role) para bypass de RLS
- Consequências: Admin funcionando corretamente. Leitura de dados usa service_role mas escrita mantém proteções no código.

## 2026-05-22 — Router.push → window.location.href
- Contexto: Erro "Router action dispatched before initialization" (23 ocorrências no Sentry)
- Decisão: Substituir todos os `setTimeout(() => router.push(...), 800)` por `setTimeout(() => window.location.href = "...", 800)` em 7 arquivos de form
- Consequências: Erro eliminado. Trade-off: full page reload em vez de SPA navigation (imperceptível pelo setTimeout de 800ms)

## 2026-05-23 — RLS permissiva + segurança no código
- Contexto: Políticas RLS complexas causaram redirect loop (circular reference no `usuarios`)
- Decisão: RLS permissiva (`USING (true) TO authenticated`) como defesa em profundidade. Segurança real feita no código com `.eq("clinica_id", clinicaId)` em toda query
- Consequências: Sistema estável, sem redirect loops. Performance melhor (menos subqueries nas policies).

## 2026-05-24 — Tipos de produto no estoque
- Contexto: Produtos de procedimento (cera), uso geral (álcool) e revenda (creme) precisavam de regras diferentes
- Decisão: Coluna `tipo` em `produtos` com 3 valores. `modulo_revenda` em `clinicas` controla se clínica pode ter produtos revenda. Revenda gera receita, não despesa.
- Consequências: Separação clara de responsabilidades. Procedimento não polui com produtos gerais. Revenda opcional por clínica.

## 2026-05-24 — Revenda como página dedicada + link no procedimento
- Contexto: Vender produtos ao cliente pode ocorrer com ou sem procedimento vinculado
- Decisão: Página `/revenda/vender` dedicada (completa: cliente, produtos, desconto, pagamento). No procedimento, link condicional que redireciona com cliente pré-selecionado via query param.
- Consequências: Flexibilidade total. Cliente com procedimento vai pra revenda já com nome preenchido. Cliente sem procedimento usa a página normal.

## 2026-05-25 — Google Calendar: fetch direto, sem lib
- Contexto: Precisava integrar Google Calendar sem adicionar dependência pesada (`googleapis` ~50MB)
- Decisão: Usar `fetch()` direto às APIs REST do Google (oauth2.googleapis.com, googleapis.com/calendar/v3). Sem dependências extras.
- Consequências: Bundle menor, sem breaking changes de versão de lib. Código ~100 linhas cobrindo OAuth + CRUD de eventos.

## 2026-05-25 — Dark mode via CSS globais
- Contexto: Adicionar dark mode sem alterar dezenas de componentes
- Decisão: CSS variables duplicadas em `.dark {}` + regras globais como `.dark .bg-white { background-color: var(--card-bg); }`. Script inline no `<head>` pra evitar flash.
- Consequências: Dark mode completo com zero alterações nos componentes. Menos manutenção, mais rápido de implementar.
