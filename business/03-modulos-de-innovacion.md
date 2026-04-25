# 03 — Módulos de innovación

Estos cuatro módulos diferencian Mindset de una página de psicología estándar. Cada uno reduce una barrera específica en el camino del paciente hacia su primera cita.

---

## 1. Video de pre-consulta

**Objetivo:** Reducir la ansiedad del paciente antes del primer contacto. Escuchar la voz y ver los gestos de la psicóloga genera más confianza que cualquier texto.

**Especificaciones:**

| Parámetro | Valor |
|---|---|
| Duración | 60 a 90 segundos |
| Contenido | Presentación personal, enfoque terapéutico, qué puede esperar el paciente |
| Tono | Cálido, cercano, no clínico |
| Producción | Luz natural, fondo neutro, ropa profesional pero accesible |
| Hosting | Vimeo (sin publicidad) o YouTube (privado, embed sin sugerencias relacionadas) |
| Ubicación en el sitio | Hero de `/inicio` (autoplay silenciado) y `/sobre-mi` |
| Comportamiento | Autoplay silenciado al cargar — el usuario activa el audio manualmente |

**Notas de implementación:**
- Usar `<iframe>` de Vimeo con parámetro `?muted=1&autoplay=1`
- En móvil, el autoplay puede estar bloqueado por el navegador — prever fallback con thumbnail + botón de play
- El video no se almacena en el repo ni en Supabase — se embebe desde plataforma externa

---

## 2. Test de auto-exploración

**Objetivo:** Ayudar al paciente a entender qué necesita antes de agendar, y pre-seleccionar el tipo de sesión más adecuada.

**Flujo:**

```
Usuario inicia el test
    → 6 preguntas de selección (escala 1–4 o sí/no)
    → Puntuación ponderada en tiempo real
    → Pantalla de resultado con 1 de 3 categorías
    → CTA personalizado: "Agendar sesión de [tipo]"
```

**Preguntas del test (borrador):**

1. ¿Con qué frecuencia sientes que las situaciones te superan?
2. ¿Tu sueño se ha visto afectado en las últimas semanas?
3. ¿Hay algo que te preocupa constantemente y no puedes soltar?
4. ¿Sientes que tus relaciones personales generan malestar?
5. ¿Has vivido una pérdida reciente (persona, trabajo, relación)?
6. ¿Buscas desarrollarte personalmente sin una crisis específica?

> **Nota:** Las preguntas definitivas las define la psicóloga. El equipo implementa la lógica, no el contenido clínico.

**Categorías de resultado:**

| Categoría | Descripción | CTA |
|---|---|---|
| Bienestar y crecimiento | Sin crisis aguda, busca desarrollo personal | "Agenda una sesión de crecimiento personal" |
| Ansiedad y estrés | Señales de sobrecarga emocional | "Agenda una sesión de manejo de ansiedad" |
| Apoyo profundo | Pérdidas, crisis o malestar intenso | "Agenda una sesión de apoyo emocional" |

**Disclaimer obligatorio en pantalla de resultado:**
> "Este test es una orientación general, no un diagnóstico. Solo un profesional de salud mental puede evaluar tu situación real."

**Implementación técnica:**
- React component con estado local (`useState`)
- Sin base de datos — el resultado no se almacena
- Evento de analítica al completar el test (Plausible custom event)
- Ver detalle técnico en [`tech/01-stack-tecnologico.md`](../tech/01-stack-tecnologico.md)

---

## 3. Chatbot de triaje con IA

**Objetivo:** Humanizar el proceso de orientación. El chatbot ayuda al paciente a identificar qué tipo de terapia necesita, responde preguntas frecuentes y lo guía al agendamiento.

**Comportamiento esperado:**

```
Usuario abre el chat
    → Saludo cálido + pregunta abierta: "¿Qué te trae por acá hoy?"
    → 2–3 preguntas de seguimiento según la respuesta
    → Orientación sobre qué tipo de sesión podría ayudarle
    → Link directo a Calendly con el tipo de sesión pre-seleccionado
```

**Límites del chatbot (no negociables):**

- ❌ No emite diagnósticos
- ❌ No recomienda medicación
- ❌ No reemplaza la terapia
- ❌ No promete resultados
- ✅ Ante señales de crisis o riesgo: responde con empatía + link a **Línea 106** (salud mental Colombia) + sugiere contacto urgente con la psicóloga

**Tono:** Cálido, empático, no clínico. Definido en el system prompt por la psicóloga.

**Ubicación:** Burbuja flotante en esquina inferior derecha. Visible en todas las páginas. No se abre automáticamente — solo al hacer clic.

**Implementación técnica:** Ver [`tech/03-modulo-ia-chatbot.md`](../tech/03-modulo-ia-chatbot.md)

---

## 4. Recursos PDF descargables + newsletter

**Objetivo:** Capturar leads cualificados (personas interesadas en bienestar mental) y nutrirlos con contenido de valor hasta que estén listos para agendar.

**Recursos iniciales:**

| Recurso | Descripción | Páginas |
|---|---|---|
| Guía de respiración 4-7-8 | Técnica paso a paso para reducir ansiedad aguda | 1 |
| Diario de emociones | Plantilla semanal de registro emocional | 4 |
| Grounding — 5 ejercicios | Técnicas de anclaje al presente para momentos difíciles | 2 |

**Flujo de entrega:**

```
Usuario elige recurso en /recursos
    → Formulario: nombre + correo (Formspree o integración directa con Mailchimp)
    → Se suscribe al newsletter automáticamente
    → Email automático con link de descarga del PDF
    → PDF disponible desde CDN de Vercel o Supabase Storage
```

**Newsletter mensual:**

| Sección | Contenido |
|---|---|
| Técnica del mes | 1 ejercicio de bienestar explicado brevemente |
| Del blog | Link al artículo más reciente |
| Recordatorio | "Las sesiones están disponibles — agenda cuando estés listo/a" |

**Herramientas:**
- **Mailchimp** (gratuito hasta 2.000 contactos y 12.000 emails/mes) — opción principal
- **Brevo** — alternativa si se supera el límite de Mailchimp
- PDFs diseñados en Canva Pro o Figma — paleta y tipografía coherentes con el sitio

**Notas de implementación:**
- Los PDFs se almacenan en Supabase Storage o en `/public` de Next.js (si son documentos públicos)
- El link de descarga en el email debe ser directo (sin login, sin pasos extra)
- El formulario incluye checkbox de consentimiento para cumplir Ley 1581

---

*Documento: Negocio | Versión 1.0 | Abril 2026*
