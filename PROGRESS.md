# PROGRESS — Estado del Proyecto Mindset

> **Para la IA que continúe este trabajo:** lee este archivo completo antes de hacer cualquier cosa. Contiene las decisiones ya tomadas, el contexto del proyecto y las restricciones no negociables. No las repitas ni las debatas — solo continúa desde donde se dejó.

---

## Estado general

| Área | Estado | Avance |
|---|---|---|
| Definición de negocio | ✅ Completo | 100% |
| Arquitectura técnica | ✅ Completo | 100% |
| Documentación del repo | ✅ Completo | 100% |
| Código — MVP | ⏳ Pendiente | 0% |
| Código — Módulos de innovación | ⏳ Pendiente | 0% |
| Código — Chatbot IA | ⏳ Pendiente | 0% |
| Deploy en Vercel | ⏳ Pendiente | 0% |

**Fase actual:** Pre-desarrollo — documentación completa, listo para iniciar código.

---

## Decisiones tomadas (no reabrir)

Estas decisiones ya fueron discutidas y aprobadas. No proponer alternativas sin que el usuario lo solicite explícitamente.

### Negocio
- El nombre del proyecto es **Mindset**
- Es una web de psicología para **una sola psicóloga** (no plataforma multi-profesional)
- El target es pacientes en **Colombia**, con posibilidad de sesiones virtuales internacionales
- El sitio transmite **calma, confianza y profesionalismo** — paleta suave, tipografía humanista

### Scope — qué hace y qué NO hace la web
✅ **SÍ hace la web:**
- Presentar a la psicóloga y sus servicios
- Permitir agendar citas (vía Calendly)
- Procesar pagos (vía Wompi / Stripe)
- Capturar nombre, correo y motivo general de consulta
- Test de auto-exploración de bienestar
- Chatbot de triaje para orientar al paciente
- Blog de bienestar mental (SEO)
- Recursos PDF descargables a cambio de suscripción

❌ **NO hace la web:**
- Gestionar historias clínicas — eso es responsabilidad exclusiva de la psicóloga con sus propias herramientas
- Almacenar notas de sesión ni evolución clínica
- Emitir diagnósticos (ni el chatbot ni el test)
- Manejar datos médicos sensibles

### Técnicas
- **Framework:** Next.js 14 (App Router) — elegido sobre Astro por mejor soporte de React y edge functions
- **Hosting:** Vercel — deploy automático desde Git, CDN global
- **Base de datos:** Supabase (PostgreSQL + Auth + RLS)
- **Agendamiento:** Calendly — embed inline (no popup), con preguntas de pre-sesión integradas
- **Pagos Colombia:** Wompi (Bancolombia) como principal, Stripe como alternativa internacional
- **Newsletter:** Mailchimp o Brevo (gratuito hasta 2.000 contactos)
- **CMS (Blog):** Sanity.io — editor visual para la psicóloga sin tocar código
- **Chatbot IA:** Claude API (claude-sonnet-4-6) via edge function en Next.js
- **Formulario contacto:** Formspree
- **Analytics:** Plausible (no Google Analytics — sin datos personales de usuarios)
- **Lenguaje:** TypeScript

### Documentación
- Solo en **Markdown** (.md)
- Solo en **español**
- Solo para **equipo de desarrollo** — sin información sensible
- Repositorio: https://github.com/esteban-RoeC/doc_mindset-docs

---

## Lo que se hizo en esta sesión (Abril 2026)

1. Definición completa de la arquitectura del proyecto (negocio + técnica)
2. Se estableció que la historia clínica queda completamente fuera del scope de la web
3. Se definió estructura del repositorio de documentación
4. Se crearon los 11 archivos de documentación en Markdown:
   - `README.md`
   - `PROGRESS.md` (este archivo)
   - `business/01-vision-y-objetivos.md`
   - `business/02-arquitectura-de-contenido.md`
   - `business/03-modulos-de-innovacion.md`
   - `business/04-roadmap.md`
   - `tech/01-stack-tecnologico.md`
   - `tech/02-integraciones.md`
   - `tech/03-modulo-ia-chatbot.md`
   - `tech/04-base-de-datos.md`
   - `tech/05-cumplimiento-legal.md`

---

## Próximos pasos (en orden de prioridad)

### Fase 1 — MVP (semanas 1–3)
- [ ] Inicializar repo de código con Next.js 14 + TypeScript + Tailwind
- [ ] Configurar proyecto en Vercel + dominio
- [ ] Crear cuenta en Sanity.io y conectar CMS
- [ ] Desarrollar layout base: navbar, footer, tipografía, paleta de colores
- [ ] Página `/` — Inicio (hero, propuesta de valor, CTA, testimonios)
- [ ] Página `/servicios`
- [ ] Página `/sobre-mi`
- [ ] Página `/agendar` con embed de Calendly inline
- [ ] Formulario de contacto con Formspree
- [ ] Página `/privacidad` con política Ley 1581
- [ ] Deploy inicial en Vercel

### Fase 2 — Conversión (semanas 4–6)
- [ ] Test de auto-exploración (React component, sin base de datos)
- [ ] Video embed de pre-consulta en hero y `/sobre-mi`
- [ ] Blog funcional con Sanity (3 artículos iniciales)
- [ ] Integración de Wompi para pagos
- [ ] PDF descargable + formulario de suscripción (Mailchimp/Brevo)
- [ ] SEO técnico: sitemap.xml, robots.txt, schema.org (LocalBusiness + Physician)
- [ ] Plausible Analytics

### Fase 3 — IA (semanas 7–8)
- [ ] Edge function en Next.js para Claude API
- [ ] Widget de chatbot (burbuja flotante, React)
- [ ] System prompt definido y revisado
- [ ] Integración chatbot → link a Calendly según resultado
- [ ] Manejo de crisis: link a Línea 106 Colombia
- [ ] Tests de seguridad y edge cases del chatbot

### Fase 4 — Escala (mes 3+)
- [ ] Panel de administración básico con Supabase UI
- [ ] Automatización de email marketing
- [ ] A/B testing de CTAs
- [ ] Optimización Core Web Vitals
- [ ] Internacionalización ES/EN (opcional)

---

## Contexto para continuar con otra IA

Si estás leyendo esto como IA continuando el proyecto, ten en cuenta:

1. **El usuario es el desarrollador del proyecto**, no la psicóloga. Habla en términos técnicos.
2. **No crear ningún archivo o código sin pedir aprobación explícita primero.** Propón, espera confirmación, luego ejecuta.
3. **La historia clínica está completamente fuera del scope.** No sugerir funcionalidades relacionadas.
4. **No incluir información sensible** en ningún archivo del repo (credenciales, datos personales, API keys).
5. **El tono del proyecto es cálido y profesional** — aplica a decisiones de diseño y UX.
6. **Colombia es el mercado principal** — considerar Wompi, Ley 1581, Línea 106, y referencias locales.
7. Antes de proponer algo nuevo, verificar si ya fue decidido en la sección "Decisiones tomadas".

---

*Última actualización: Abril 2026*
