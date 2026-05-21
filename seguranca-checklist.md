# Checklist de Segurança — Novos Projetos

Usar este checklist ao iniciar QUALQUER projeto SaaS multi-tenant.
Implementar em 3 fases desde o dia 1.

## Regra de ouro
- Camada central de auth (`lib/auth/guards.ts`) antes de qualquer Server Action
- `.eq("clinica_id", clinicaId)` em TODA query
- Zod validation em TODA entrada de dados
- RLS habilitado antes do primeiro deploy
- Nunca expor `error.message` do Supabase pro usuário

---

## Fase 1 — Fundação (dia 1-2)
- [ ] Criar `lib/auth/guards.ts`: requireUser, getClinicaId, requireSuperAdmin
- [ ] Criar `lib/errors.ts`: AuthError, ValidationError
- [ ] Toda Server Action usa getClinicaId() e filtra por clinica_id
- [ ] Nenhum `data!` no código — sempre validar null
- [ ] Mensagens de erro genéricas no login/register
- [ ] Admin actions com requireSuperAdmin() em todas

## Fase 2 — Banco (dia 2-3)
- [ ] RLS enabled em TODAS as tabelas
- [ ] Policies criadas para SELECT, INSERT, UPDATE, DELETE em cada tabela
- [ ] Tabela `admin_logs` com FK para usuarios
- [ ] Campo `role` validado no código
- [ ] Admin tem policy separada com bypass

## Fase 3 — Produção (antes do deploy)
- [ ] Schemas Zod em todos os módulos
- [ ] next.config.ts com CSP + security headers
- [ ] Rate limiting no login (5 tentativas/15min) e register (3/hora)
- [ ] Middleware matcher excluindo `_next/` inteiro
- [ ] `SUPABASE_SERVICE_ROLE_KEY` no `.env.local` (sem NEXT_PUBLIC_)
- [ ] Sanitização anti-XSS em campos de texto livre
- [ ] Security headers: CSP, X-Frame-Options, X-Content-Type, Referrer, Permissions
- [ ] Cookie security review (HttpOnly, Secure, SameSite)
- [ ] Nenhuma env var sensível com prefixo NEXT_PUBLIC_

---

## Arquitetura de segurança (copiar pra todo projeto)

```
src/lib/auth/guards.ts        ← requireUser, getClinicaId, requireSuperAdmin
src/lib/errors.ts              ← AuthError, ValidationError, NotFoundError
src/lib/security/rate-limit.ts ← In-memory rate limiter
src/lib/utils/sanitize.ts      ← sanitize(), stripTags() anti-XSS
src/lib/supabase/admin.ts      ← Cliente service_role (bypass RLS)
src/schemas/*.ts               ← Zod schemas por módulo
next.config.ts                 ← Security headers + CSP
src/middleware.ts              ← Matcher otimizado (_next/ excluído)
```

## Padrão de Server Action segura

```ts
"use server";
import { getClinicaId } from "@/lib/auth/guards";
import { meuSchema } from "@/schemas/modulo";

export async function minhaAction(formData: FormData) {
  // 1. Auth
  const { clinicaId } = await getClinicaId();
  
  // 2. Validação
  const parsed = meuSchema.safeParse({ ... });
  if (!parsed.success) throw new Error("Dados inválidos");
  
  // 3. Query com clinica_id
  const { error } = await supabase.from("tabela")
    .insert({ ...parsed.data, clinica_id: clinicaId });
  
  if (error) throw new Error("Erro interno");
  revalidatePath("/rota");
  return { success: true };
}
```

## RLS Policy padrão

```sql
ALTER TABLE tabela ENABLE ROW LEVEL SECURITY;

CREATE POLICY "tabela_select" ON tabela FOR SELECT USING (
  clinica_id IN (SELECT clinica_id FROM usuarios WHERE email = auth.email())
);
CREATE POLICY "tabela_insert" ON tabela FOR INSERT WITH CHECK (
  clinica_id IN (SELECT clinica_id FROM usuarios WHERE email = auth.email())
);
CREATE POLICY "tabela_update" ON tabela FOR UPDATE USING (
  clinica_id IN (SELECT clinica_id FROM usuarios WHERE email = auth.email())
);
CREATE POLICY "tabela_delete" ON tabela FOR DELETE USING (
  clinica_id IN (SELECT clinica_id FROM usuarios WHERE email = auth.email())
);
```
