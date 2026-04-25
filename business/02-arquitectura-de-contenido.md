# 02 — Arquitectura de contenido

## Mapa de sitio

```
/                           → Inicio
/servicios                  → Tipos de terapia y tarifas
/sobre-mi                   → Presentación de la psicóloga
/agendar                    → Calendly embed + FAQ rápido
/blog                       → Artículos de bienestar (CMS Sanity)
/blog/[slug]                → Artículo individual
/recursos                   → PDFs descargables + suscripción
/faq                        → Preguntas frecuentes
/privacidad                 → Política de tratamiento de datos (Ley 1581)
```

---

## Páginas principales

### `/` — Inicio

Página de mayor tráfico. Debe convertir en menos de 10 segundos de atención.

**Secciones en orden:**

1. **Hero** — Foto o video corto de la psicóloga (autoplay silenciado). Titular en 1 línea. Botón principal: "Agendar consulta".
2. **¿Cómo funciona?** — 3 pasos visuales: Agenda → Paga → Tu primera sesión. Sin texto largo.
3. **Servicios destacados** — 3–4 tarjetas con los servicios más buscados. Link a `/servicios`.
4. **Test de auto-exploración** — Llamado a acción: "¿No sabes por dónde empezar? Haz el test". Link al componente interactivo.
5. **Testimonios** — Máximo 3. Anónimos o con iniciales. Sin foto de paciente.
6. **Sobre mí (resumen)** — Párrafo breve + foto. Link a `/sobre-mi`.
7. **CTA final** — Botón repetido: "Agendar primera sesión".

---

### `/servicios` — Tipos de terapia

**Secciones:**

- Ansiedad y estrés
- Duelo y pérdidas
- Autoestima y desarrollo personal
- Terapia de pareja
- Modalidad: virtual / presencial
- Tarifas (rango de precios, no precio exacto si la psicóloga prefiere no publicarlo)
- Duración de sesión: 50 minutos estándar

> **Nota de desarrollo:** Las tarifas exactas no se hardcodean en el código. Se gestionan desde Sanity CMS para que la psicóloga pueda actualizarlas sin intervención del equipo.

---

### `/sobre-mi` — Presentación

**Contenido:**

- Video de presentación (60–90 segundos) — embed desde Vimeo o YouTube privado
- Foto profesional
- Párrafo de presentación: enfoque terapéutico, valores, qué ofrece
- Formación y certificaciones (listado limpio, sin sobrecargar)
- Frase o filosofía de trabajo

> **Nota de desarrollo:** Todo el contenido de esta página se gestiona desde Sanity. El equipo no edita texto directamente en el código.

---

### `/agendar` — Agendamiento

Página de conversión principal. Debe ser simple y directa.

**Secciones:**

1. Título claro: "Agenda tu primera sesión"
2. **Embed de Calendly inline** — no popup, no redirección. El usuario no sale de la página.
3. **FAQ rápido** (debajo del embed, acordeón):
   - ¿Cuánto dura la sesión?
   - ¿Cómo me preparo para la primera sesión?
   - ¿Qué pasa si necesito cancelar?
   - ¿Es confidencial?
   - ¿Hacen sesiones virtuales?

**Flujo post-agendamiento:**
```
Usuario agenda en Calendly
    → Calendly envía correo de confirmación automático (al paciente y a la psicóloga)
    → Correo incluye link de pago (Wompi)
    → Usuario paga
    → Correo de confirmación final con link de videollamada (Zoom/Meet) si es virtual
```

---

### `/blog` — Artículos de bienestar

**Propósito:** SEO long-tail + posicionar a la psicóloga como experta.

**Categorías iniciales:**
- Ansiedad y estrés
- Relaciones y vínculos
- Autoestima y crecimiento personal
- Herramientas de bienestar

**Estructura de cada artículo:**
- Titular optimizado para búsqueda (ej: "Cómo manejar la ansiedad en el trabajo")
- Tiempo de lectura estimado
- Contenido (800–1.500 palabras)
- Disclaimer al final: "Este artículo es informativo y no reemplaza la consulta con un profesional de salud mental."
- CTA al final: "¿Quieres trabajar esto en sesión? Agenda aquí."

> **Nota de desarrollo:** Blog gestionado desde Sanity CMS. Schema.org `Article` en cada post.

---

### `/recursos` — Descargables

**Lead magnets iniciales:**
- Guía de respiración 4-7-8 (PDF, 1 página)
- Diario de emociones — plantilla semanal (PDF, 4 páginas)
- Ejercicios de grounding para momentos de ansiedad (PDF, 2 páginas)

**Flujo de descarga:**
```
Usuario elige recurso
    → Formulario: nombre + correo
    → Suscripción automática a newsletter (Mailchimp / Brevo)
    → Email automático con link de descarga
    → PDF se entrega desde Supabase Storage o CDN de Vercel
```

---

### `/faq` — Preguntas frecuentes

Preguntas ordenadas por frecuencia real. Formato acordeón.

**Preguntas incluidas:**
- ¿Cómo es la primera sesión?
- ¿Cuánto dura cada sesión?
- ¿Cuántas sesiones voy a necesitar?
- ¿Qué pasa si necesito cancelar o reagendar?
- ¿Mis datos y lo que digo en sesión es confidencial?
- ¿Hacen sesiones virtuales? ¿Con qué plataforma?
- ¿Cuáles son los métodos de pago?
- ¿Trabajan con alguna EPS o seguro médico?

---

### `/privacidad` — Política de datos

Requerida por Ley 1581 de 2012. Ver detalle en [`tech/05-cumplimiento-legal.md`](../tech/05-cumplimiento-legal.md).

---

## Flujo completo del paciente

```
Descubrimiento
(Google, referido, redes)
        ↓
Llega a /inicio
        ↓
Ve video de la psicóloga ──────────────────────────┐
        ↓                                           │
Hace test de auto-exploración                       │ Cualquiera de estos
        ↓                                           │ pasos puede llevar
Interactúa con chatbot de triaje                    │ directamente al CTA
        ↓                                           │ de agendamiento
Lee blog o descarga recurso PDF ───────────────────┘
        ↓
Va a /agendar
        ↓
Agenda en Calendly (responde preguntas de pre-sesión)
        ↓
Recibe correo con link de pago
        ↓
Paga (Wompi / Stripe)
        ↓
Cita confirmada ✓
```

---

## Elementos globales

### Navbar
- Logo + nombre "Mindset"
- Links: Inicio / Servicios / Sobre mí / Blog / Recursos
- Botón CTA siempre visible: "Agendar" (color de acento)
- En móvil: menú hamburguesa

### Footer
- Links secundarios: FAQ / Privacidad / Contacto
- Redes sociales (Instagram principalmente)
- Texto legal mínimo: NIT / nombre profesional / tarjeta profesional
- Chatbot burbuja flotante (visible en todas las páginas)

### Chatbot flotante
- Visible en todas las páginas como burbuja en esquina inferior derecha
- Se abre al hacer clic — no aparece automáticamente (no intrusivo)
- Ver detalle en [`business/03-modulos-de-innovacion.md`](./03-modulos-de-innovacion.md)

---

*Documento: Negocio | Versión 1.0 | Abril 2026*
