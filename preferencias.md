# Preferências Pessoais

## Stack Principal
- Node.js + TypeScript
- React / Next.js 16
- Supabase (backend, auth, DB)
- PostgreSQL + Drizzle ORM
- HTML + CSS + JS puro (landing pages simples)
- Docker

## Ferramentas
- Editor: opencode
- Package manager: pnpm
- Terminal: PowerShell
- Controle de versão: Git + GitHub CLI
- Monitoramento: Sentry

## Estilo de Código
- TypeScript com strict mode
- Nomes descritivos em inglês
- Commits semânticos (feat:, fix:, chore:, docs:)
- Comentários apenas quando necessário
- Server Components por padrão, "use client" só quando necessário
- "as any" só em último caso (quando tipos do Supabase são limitados)

## Preferências Gerais
- Landing pages simples: HTML + CSS + JS puro
- Landing pages complexas: Next.js
- Clientes locais (pessoal do bairro)
- CSS: Tailwind CSS sempre que possível

## Arquitetura
- Multi-tenant por `clinica_id` em toda query
- Server Actions para mutações
- Validação com Zod em toda entrada de dados
- `createAdminClient()` (service_role) para ações cross-tenant
- `guards.ts` como camada central de auth (requireUser, getClinicaId, requireSuperAdmin)
- RLS permissiva como defesa em profundidade (segurança real no código)

## Comunicação com IA
- Direto, respostas curtas ("sim", "boa", "pode", "não")
- Confia nas sugestões técnicas, raramente questiona implementação
- Prefere que a IA apresente opções (A x B x C) em vez de decidir sozinho
- Gosta de ver o plano antes da execução
- Quando algo quebra, descreve o sintoma e confia que a IA descobre a causa

## Desenvolvimento de Features
- **"Não quebrar o que funciona"** é a regra #1
- Features incrementais: uma de cada vez, constrói em cima
- Separação visual clara: badges coloridos, cores por tipo/profissional
- Nomes em português pra coisas de negócio (procedimento, geral, revenda)
- Performance importa: auditoria completa antes de reclamar

## Design / UX
- Visual limpo, profissional, sem firula
- Informações condensadas (cards com múltiplas métricas)
- Cores têm significado: verde=receita, vermelho=exclusão, roxo=admin
- Badges e avatares coloridos chamam atenção
- Modo escuro é valorizado
- Sidebar com ícones e hover colorido
- Calendário com dots visuais por profissional

## Negócio / SaaS
- Multi-tenant: cada clínica vê só seus dados
- Planos: trial (15 dias) e permanente
- Módulos por clínica: ativar/desativar agenda, estoque, finanças, etc.
- Convite-only: sem cadastro público
- Sempre avalia custo antes de decidir ("Isso é de graça?")
- Prefere simplicidade no lançamento, complexidade depois

## Confiança na IA
- Permite mudanças estruturais (RLS, indexes, migrations)
- Delega decisões de arquitetura: "o que você acha melhor"
- Só pede explicação quando algo dá errado, não antes
- Não pede pra explicar o código — confia que funciona
- "Pode implementar sem quebrar o código" é a senha pra modo build
