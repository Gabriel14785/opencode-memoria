# Microsaas Clínica Estética — Arquitetura v1

## Stack
| Camada | Tecnologia |
|--------|-----------|
| Frontend | Next.js 14+ (App Router) |
| Estilo | Tailwind CSS |
| Banco | PostgreSQL (Supabase) |
| Auth | Supabase Auth |
| ORM | Drizzle ORM |
| Validação | Zod |
| Testes | Vitest |
| Package | pnpm |

## Módulos
```
┌─────────────────────────────────────────────┐
│                  NEXT.JS APP                │
│  ┌──────────┐ ┌──────────┐ ┌──────────────┐ │
│  │  Agenda   │ │  Estoque  │ │   Finanças   │ │
│  │          │ │          │ │              │ │
│  │agendar   │ │produtos  │ │receitas      │ │
│  │profission│ │entradas  │ │despesas      │ │
│  │servicos  │ │saidas    │ │fluxo de caixa│ │
│  │calendario│ │alertas   │ │relatorios    │ │
│  └──────────┘ └──────────┘ └──────────────┘ │
│                                             │
│  ┌──────────────────────────────────────┐   │
│  │         Shared (core)                │   │
│  │  auth, clients, layout, ui, db      │   │
│  └──────────────────────────────────────┘   │
└─────────────────────────────────────────────┘
         │
         ▼
┌─────────────────────┐
│     SUPABASE        │
│  PostgreSQL + Auth  │
│  RLS (multi-tenant) │
└─────────────────────┘
```

## Estrutura de Pastas
```
src/
├── app/                    # Next.js App Router
│   ├── (auth)/             # Login / registro
│   ├── (dashboard)/        # Layout principal (logado)
│   │   ├── agenda/
│   │   ├── estoque/
│   │   ├── financas/
│   │   ├── clientes/
│   │   └── configuracoes/
│   └── api/                # API routes
│       ├── agenda/
│       ├── estoque/
│       ├── financas/
│       └── auth/
├── components/             # Componentes compartilhados
│   ├── ui/                 # Botões, inputs, modais
│   ├── agenda/             # Componentes específicos
│   ├── estoque/
│   └── financas/
├── lib/                    # Lógica compartilhada
│   ├── db/                 # Drizzle schema + client
│   ├── auth/               # Auth helpers
│   └── utils/              # Utilitários
├── schemas/                # Zod schemas
└── types/                  # TypeScript types
```

## Modelagem do Banco
```
clientes
  id, nome, email, telefone, created_at

profissionais
  id, nome, especialidade, cor_agenda, ativo

servicos
  id, nome, duracao_min, preco, comissao_profissional

agendamentos
  id, cliente_id, profissional_id, servico_id,
  data_hora_inicio, data_hora_fim, status,
  observacao, created_at

produtos
  id, nome, categoria, preco_venda, preco_custo,
  estoque_atual, estoque_minimo

movimentacoes_estoque
  id, produto_id, tipo (entrada/saida), quantidade,
  valor_unitario, observacao, created_at

transacoes
  id, tipo (receita/despesa), categoria,
  valor, descricao, data, forma_pagamento,
  agendamento_id (opcional), created_at
```

## Decisões Arquiteturais
1. **Server Components** por padrão, Client só quando precisar de interação
2. **Multi-tenant via RLS** — cada clínica tem seus dados isolados pelo Supabase RLS
3. **API routes do Next.js** para lógica que precisa de servidor
4. **Server Actions** para mutações simples (criar agendamento, etc)
5. **Zod** na validação de entrada em toda API route e Server Action
6. **Drizzle** como ORM — tipado, performático, funciona bem com Supabase

## Próximos Passos
1. Criar projeto Next.js com estrutura de pastas
2. Configurar Supabase + Drizzle
3. Criar migrations do banco
4. Implementar autenticação + multi-tenant
5. Módulo de agenda (MVP)
6. Módulo financeiro
7. Módulo de estoque
