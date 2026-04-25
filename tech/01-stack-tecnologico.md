# 01 — Stack tecnológico

## Visión general

Arquitectura JAMstack: sitio estático generado en build time, sin servidor backend propio. Reduce costos operativos, aumenta velocidad de carga y simplifica el mantenimiento a largo plazo.

```
Cliente (browser)
    ↕ HTTPS
Vercel CDN (edge network global)
    ↕
Next.js 14 (App Router)
    ├── Páginas estáticas (SSG)
    ├── Edge functions (API routes — solo para chatbot)
    └── Sanity CMS (blog y contenido dinámico)
         ↕
Servicios externos (Calendly, Wompi, Supabase, Claude API)
```

---

## Framework — Next.js 14

| Parámetro | Valor |
|---|---|
| Versión | Next.js 14 con App Router |
| Lenguaje | TypeScript — tipado estricto en todo el proyecto |
| Renderizado | SSG (Static Site Generation) por defecto — SSR solo donde sea necesario |
| Estilos | Tailwind CSS v3 |
| Componentes UI | shadcn/ui — componentes accesibles y personalizables |
| Iconos | Lucide React |
| Animaciones | Framer Motion — solo para transiciones de página y micro-interacciones |

**Por qué Next.js sobre Astro:**
- Mejor soporte de React para los módulos interactivos (test, chatbot)
- Edge functions nativas para la integración con Claude API
- Ecosistema más amplio de integraciones con Sanity, Vercel y Supabase
- El equipo tiene mayor experiencia con React

---

## Hosting — Vercel

| Parámetro | Valor |
|---|---|
| Plan inicial | Hobby (gratuito) |
| Deploy | Automático desde rama `main` en GitHub |
| Preview deploys | Automático en cada PR |
| CDN | Red global de edge — tiempo de carga < 200ms para Colombia |
| Dominio | Dominio propio configurado en Vercel (comprar en Namecheap o GoDaddy) |
| Variables de entorno | Gestionadas en el dashboard de Vercel — nunca en el repo |
| Analytics | Vercel Analytics + Speed Insights (incluido en plan Hobby) |

**Variables de entorno requeridas (configurar en Vercel, nunca en `.env` en el repo):**

```
ANTHROPIC_API_KEY=          # Claude API — solo accesible desde edge functions
NEXT_PUBLIC_CALENDLY_URL=   # URL del calendario de Calendly (pública)
SUPABASE_URL=               # URL del proyecto Supabase
SUPABASE_ANON_KEY=          # Clave pública de Supabase
SUPABASE_SERVICE_ROLE_KEY=  # Clave privada — solo en server-side
FORMSPREE_ENDPOINT=         # Endpoint del formulario de contacto
NEXT_PUBLIC_PLAUSIBLE_DOMAIN= # Dominio para Plausible Analytics
```

---

## CMS — Sanity.io

| Parámetro | Valor |
|---|---|
| Plan | Free (hasta 3 usuarios, 10GB assets) |
| Uso | Blog, contenido de servicios, testimonios, tarifas |
| Studio | Sanity Studio v3 — editor visual para la psicóloga |
| Integración | `@sanity/client` + `next-sanity` |
| Imágenes | Sanity CDN con optimización automática |
| Preview | Live preview en Next.js para la psicóloga |

**Schemas de Sanity (types principales):**

```typescript
// Post (artículos del blog)
{
  title: string
  slug: slug
  excerpt: string
  body: portableText
  category: reference → Category
  publishedAt: datetime
  seoTitle: string
  seoDescription: string
}

// Service (tipos de terapia)
{
  title: string
  description: portableText
  duration: number  // minutos
  priceRange: string  // "desde $X" — no precio exacto
  isActive: boolean
}

// Testimonial
{
  quote: string
  initials: string  // solo iniciales — nunca nombre completo
  sessionType: string
}
```

---

## Base de datos — Supabase

Ver detalle completo en [`tech/04-base-de-datos.md`](./04-base-de-datos.md).

| Parámetro | Valor |
|---|---|
| Plan | Free (500MB DB, 1GB Storage, 50.000 MAU) |
| Motor | PostgreSQL |
| Auth | Supabase Auth — solo para la psicóloga (panel admin) |
| Storage | PDFs descargables, assets privados |
| RLS | Row Level Security activado en todas las tablas |

---

## Analytics — Plausible

| Parámetro | Valor |
|---|---|
| Herramienta | Plausible Analytics |
| Por qué no Google Analytics | Plausible no recopila datos personales — sin cookies, sin GDPR/Ley 1581 concerns |
| Plan | Starter ($9 USD/mes) o self-hosted (gratuito, requiere servidor) |
| Eventos personalizados | Completación del test, apertura del chatbot, clics en Calendly, descargas de PDF |

---

## SEO técnico

| Elemento | Implementación |
|---|---|
| Sitemap | Generado automáticamente con `next-sitemap` |
| Robots.txt | Configurado via `next-sitemap` |
| Meta tags | `generateMetadata()` en cada página de Next.js |
| Open Graph | Imágenes dinámicas con `next/og` |
| Schema.org | JSON-LD en cada página — tipos: `LocalBusiness`, `Physician`, `Article`, `FAQPage` |
| Canonical URLs | Configurados automáticamente |
| Core Web Vitals | Monitoreado con Vercel Speed Insights |

---

## Estructura del repositorio de código

> Este es el repo de **código**, diferente al repo de documentación actual.

```
mindset-web/
├── app/                        # Next.js App Router
│   ├── (site)/                 # Grupo de rutas del sitio público
│   │   ├── page.tsx            # /inicio
│   │   ├── servicios/
│   │   ├── sobre-mi/
│   │   ├── agendar/
│   │   ├── blog/
│   │   ├── recursos/
│   │   ├── faq/
│   │   └── privacidad/
│   └── api/
│       └── chat/
│           └── route.ts        # Edge function — chatbot Claude API
├── components/
│   ├── ui/                     # shadcn/ui components
│   ├── quiz/                   # Test de auto-exploración
│   ├── chatbot/                # Widget de chatbot
│   └── shared/                 # Navbar, Footer, etc.
├── lib/
│   ├── sanity/                 # Cliente y queries de Sanity
│   └── supabase/               # Cliente de Supabase
├── sanity/
│   └── schemas/                # Schemas del CMS
├── public/
│   └── pdfs/                   # PDFs públicos (recursos descargables)
└── .env.local                  # Variables locales — nunca en Git
```

---

## Convenciones de código

- **Componentes:** PascalCase (`QuizComponent.tsx`)
- **Funciones y hooks:** camelCase (`useQuizState.ts`)
- **Archivos de rutas:** kebab-case en carpetas (`/sobre-mi/page.tsx`)
- **Variables de entorno públicas:** prefijo `NEXT_PUBLIC_`
- **Sin comentarios en español en el código** — el código en inglés, la documentación en español
- **Commits:** Conventional Commits (`feat:`, `fix:`, `docs:`, `chore:`)

---

*Documento: Técnico | Versión 1.0 | Abril 2026*
