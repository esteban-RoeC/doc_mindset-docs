# 04 — Roadmap

## Resumen de fases

| Fase | Nombre | Duración estimada | Costo mensual estimado |
|---|---|---|---|
| 1 | MVP | Semanas 1–3 | $0 USD |
| 2 | Conversión | Semanas 4–6 | $0–$5 USD |
| 3 | IA | Semanas 7–8 | $5–$20 USD |
| 4 | Escala | Mes 3 en adelante | $20–$50 USD |

---

## Fase 1 — MVP (semanas 1–3)

**Objetivo:** Sitio funcional en producción con agendamiento operativo.

**Entregables:**

- [ ] Repositorio de código inicializado (Next.js 14 + TypeScript + Tailwind)
- [ ] Proyecto configurado en Vercel con dominio propio
- [ ] Proyecto en Sanity.io conectado al frontend
- [ ] Layout base: navbar, footer, tipografía, paleta de colores
- [ ] Página `/` — Inicio (hero, propuesta de valor, CTA, sección servicios, testimonios)
- [ ] Página `/servicios`
- [ ] Página `/sobre-mi`
- [ ] Página `/agendar` con embed de Calendly inline
- [ ] Formulario de contacto con Formspree
- [ ] Página `/privacidad` con política Ley 1581
- [ ] Página `/faq` con acordeón
- [ ] Deploy en Vercel funcionando

**Criterio de éxito:** Un paciente puede llegar al sitio, entender los servicios y agendar una cita sin intervención del equipo.

---

## Fase 2 — Conversión (semanas 4–6)

**Objetivo:** Agregar los módulos que aumentan la tasa de conversión y el posicionamiento en Google.

**Entregables:**

- [ ] Test de auto-exploración (React component, sin base de datos)
- [ ] Video embed de pre-consulta en hero y `/sobre-mi`
- [ ] Blog funcional con Sanity (3 artículos iniciales publicados)
- [ ] Página `/recursos` con PDFs descargables
- [ ] Integración de formulario de recursos con Mailchimp/Brevo
- [ ] Integración de Wompi para pagos (botón o link de pago)
- [ ] SEO técnico completo:
  - `sitemap.xml` generado automáticamente
  - `robots.txt`
  - Schema.org `LocalBusiness` + `Physician`
  - Schema.org `Article` en posts del blog
  - Meta tags Open Graph para compartir en redes
- [ ] Plausible Analytics instalado

**Criterio de éxito:** El sitio aparece en búsquedas locales y la tasa de completación del test supera el 50%.

---

## Fase 3 — IA (semanas 7–8)

**Objetivo:** Activar el chatbot de triaje con IA.

**Entregables:**

- [ ] Edge function en Next.js para llamadas a Claude API (la API key nunca expuesta al cliente)
- [ ] Widget de chatbot (burbuja flotante React) con diseño coherente al sitio
- [ ] System prompt revisado y aprobado por la psicóloga
- [ ] Integración chatbot → link a Calendly según resultado de la conversación
- [ ] Manejo de crisis: respuesta con link a Línea 106 Colombia ante señales de riesgo
- [ ] Rate limiting básico para controlar costos de API
- [ ] Tests de edge cases: mensajes inapropiados, intentos de jailbreak, crisis

**Criterio de éxito:** El chatbot orienta correctamente en más del 80% de conversaciones de prueba y nunca emite diagnósticos.

---

## Fase 4 — Escala (mes 3 en adelante)

**Objetivo:** Optimizar, automatizar y escalar lo que ya funciona.

**Entregables:**

- [ ] Panel de administración básico con Supabase UI para la psicóloga
- [ ] Automatización de email marketing (secuencia de bienvenida, recordatorios)
- [ ] A/B testing de CTAs principales
- [ ] Optimización Core Web Vitals (LCP < 2.5s, CLS < 0.1)
- [ ] Revisión y expansión del blog (objetivo: 12 artículos en 3 meses)
- [ ] Internacionalización ES/EN (evaluación según demanda real)

---

## Costos estimados por servicio

| Servicio | Plan | Costo mensual |
|---|---|---|
| Vercel | Hobby | $0 |
| Dominio .com.co | — | ~$1.25 USD/mes (facturado anual) |
| Calendly | Basic (1 tipo de evento) | $0 |
| Calendly | Standard (cuando escale) | $10 USD |
| Supabase | Free | $0 |
| Sanity.io | Free (hasta 3 usuarios) | $0 |
| Plausible Analytics | Starter | $9 USD |
| Claude API (chatbot) | Pay-per-use | $5–$20 USD según volumen |
| Mailchimp | Free (hasta 2.000 contactos) | $0 |
| Wompi | Por transacción (2.9% + $900 COP) | $0 fijo |
| **Total estimado mes 1–2** | | **$0–$5 USD** |
| **Total estimado mes 3+** | | **$20–$40 USD** |

---

## Métricas de éxito del proyecto

| Métrica | Herramienta de medición | Meta a 6 meses |
|---|---|---|
| Tasa de conversión visita → cita | Plausible + Calendly | > 3% |
| Posición en Google "psicóloga [ciudad]" | Google Search Console | Top 3 |
| Suscriptores al newsletter | Mailchimp / Brevo | 200+ |
| Completación del test de auto-exploración | Plausible custom events | > 60% |
| NPS post-primera sesión | Formulario manual | > 70 |
| Core Web Vitals — LCP | Vercel Analytics | < 2.5s |
| Core Web Vitals — CLS | Vercel Analytics | < 0.1 |
| Artículos de blog publicados | Sanity | 12+ |

---

*Documento: Negocio | Versión 1.0 | Abril 2026*
