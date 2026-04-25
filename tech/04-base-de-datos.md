# 04 — Base de datos

## Plataforma: Supabase

PostgreSQL gestionado con autenticación, Row Level Security y storage incluidos. Plan gratuito cubre el primer año de operación normal del proyecto.

| Recurso | Límite plan free |
|---|---|
| Base de datos | 500 MB |
| Storage | 1 GB |
| Usuarios activos | 50.000 MAU |
| Edge functions | 500.000 invocaciones/mes |
| Backups | Automáticos diarios (7 días de retención) |

---

## Principios de diseño de datos

1. **Mínimo necesario:** solo se almacena lo que la web necesita para funcionar. No hay datos clínicos, notas de sesión ni historial terapéutico.
2. **RLS en todas las tablas:** cada tabla tiene Row Level Security activado. Sin políticas explícitas, nadie accede a nada.
3. **Sin datos sensibles en logs:** los logs de Supabase no deben contener información personal identificable.

---

## Esquema de base de datos

### Tabla: `newsletter_subscribers`

Suscriptores al newsletter y/o descargaron un recurso PDF.

```sql
create table newsletter_subscribers (
  id          uuid primary key default gen_random_uuid(),
  email       text not null unique,
  first_name  text,
  source      text,           -- 'pdf_breathing' | 'pdf_journal' | 'pdf_grounding' | 'footer'
  created_at  timestamptz default now(),
  is_active   boolean default true
);

-- RLS: solo la psicóloga autenticada puede leer/modificar
alter table newsletter_subscribers enable row level security;

create policy "Solo admin puede acceder"
  on newsletter_subscribers
  for all
  using (auth.role() = 'authenticated');
```

---

### Tabla: `quiz_results`

Resultados anónimos del test de auto-exploración. No contiene datos personales — solo analítica agregada.

```sql
create table quiz_results (
  id          uuid primary key default gen_random_uuid(),
  result      text not null,  -- 'wellness' | 'anxiety' | 'deep_support'
  answers     jsonb,          -- snapshot de respuestas (sin identificar al usuario)
  created_at  timestamptz default now()
);

-- RLS: insertable desde el sitio sin autenticación, solo lectura para admin
alter table quiz_results enable row level security;

create policy "Insertar resultado desde sitio"
  on quiz_results
  for insert
  with check (true);

create policy "Solo admin puede leer"
  on quiz_results
  for select
  using (auth.role() = 'authenticated');
```

---

### Tabla: `contact_submissions`

Mensajes del formulario de contacto general. Respaldo en base de datos además del correo via Formspree.

```sql
create table contact_submissions (
  id          uuid primary key default gen_random_uuid(),
  name        text not null,
  email       text not null,
  message     text not null,
  created_at  timestamptz default now(),
  is_read     boolean default false
);

-- RLS: insertable desde el sitio sin autenticación, solo lectura para admin
alter table contact_submissions enable row level security;

create policy "Insertar desde sitio"
  on contact_submissions
  for insert
  with check (true);

create policy "Solo admin puede leer"
  on contact_submissions
  for select
  using (auth.role() = 'authenticated');
```

---

## Autenticación

Supabase Auth se usa exclusivamente para la psicóloga — acceso al panel de administración.

```typescript
// lib/supabase/admin.ts
import { createClient } from '@supabase/supabase-js'

// Cliente para el panel admin (server-side, con service role key)
export const supabaseAdmin = createClient(
  process.env.SUPABASE_URL!,
  process.env.SUPABASE_SERVICE_ROLE_KEY! // Solo en servidor
)

// Cliente para el sitio público (client-side, con anon key)
export const supabasePublic = createClient(
  process.env.NEXT_PUBLIC_SUPABASE_URL!,
  process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
)
```

**Rol de usuario:**
- La psicóloga tiene un único usuario en Supabase Auth
- Login con email + contraseña (o magic link)
- Acceso al panel: ruta protegida `/admin` en Next.js

---

## Storage

Supabase Storage para archivos del proyecto.

| Bucket | Contenido | Acceso |
|---|---|---|
| `pdfs-public` | Recursos PDF descargables | Público (sin auth) |
| `assets-private` | Archivos internos, backups | Solo admin |

```sql
-- Bucket público para PDFs
insert into storage.buckets (id, name, public)
values ('pdfs-public', 'pdfs-public', true);

-- Política: cualquiera puede leer, solo admin puede subir
create policy "Lectura pública"
  on storage.objects for select
  using (bucket_id = 'pdfs-public');

create policy "Solo admin puede subir"
  on storage.objects for insert
  with check (bucket_id = 'pdfs-public' and auth.role() = 'authenticated');
```

---

## Variables de entorno

```
# Público (NEXT_PUBLIC_ — accesible en el cliente)
NEXT_PUBLIC_SUPABASE_URL=https://[project-id].supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJ...

# Privado (solo servidor — configurar en Vercel dashboard, nunca en el repo)
SUPABASE_SERVICE_ROLE_KEY=eyJ...
```

---

## Lo que NO está en la base de datos

Para evitar cualquier confusión sobre el scope del proyecto:

- ❌ Historias clínicas
- ❌ Notas de sesión
- ❌ Evolución terapéutica de pacientes
- ❌ Diagnósticos
- ❌ Conversaciones del chatbot (se guardan solo en memoria del navegador)
- ❌ Datos de pago (Wompi / Stripe los gestionan en sus propios sistemas)
- ❌ Calendarios o citas agendadas (Calendly los gestiona)

---

*Documento: Técnico | Versión 1.0 | Abril 2026*
