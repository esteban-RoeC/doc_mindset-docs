# 02 — Integraciones

## Mapa de integraciones

```
Sitio Mindset (Next.js / Vercel)
    │
    ├── Calendly          → Agendamiento + correos automáticos
    ├── Wompi             → Pagos Colombia
    ├── Stripe            → Pagos internacionales (opcional fase 2)
    ├── Formspree         → Formulario de contacto general
    ├── Mailchimp / Brevo → Newsletter + entrega de recursos PDF
    ├── Sanity.io         → CMS blog y contenido
    ├── Supabase          → Base de datos + storage + auth
    ├── Claude API        → Chatbot de triaje (edge function)
    └── Plausible         → Analytics sin datos personales
```

Ninguna de estas integraciones requiere servidor backend propio. Todo corre en edge functions de Vercel o como servicios externos embebidos.

---

## Calendly — Agendamiento

**Función:** Permite al paciente elegir día y hora de cita. Sincroniza con el calendario de la psicóloga y envía correos de confirmación automáticamente.

**Configuración requerida:**

1. Crear cuenta en Calendly y conectar Google Calendar de la psicóloga
2. Crear tipo de evento: "Primera sesión" (50 min)
3. Agregar preguntas de pre-sesión en el flujo de agendamiento:
   - ¿Cuál es el motivo principal por el que buscas apoyo? (texto libre)
   - ¿Has tenido experiencia previa con psicología? (sí/no)
   - ¿Prefieres sesión virtual o presencial?
4. Configurar correos automáticos: confirmación + recordatorio 24h antes

**Implementación en Next.js:**

```tsx
// components/CalendlyEmbed.tsx
'use client'
import { useEffect } from 'react'

export function CalendlyEmbed({ url }: { url: string }) {
  useEffect(() => {
    const script = document.createElement('script')
    script.src = 'https://assets.calendly.com/assets/external/widget.js'
    script.async = true
    document.body.appendChild(script)
    return () => { document.body.removeChild(script) }
  }, [])

  return (
    <div
      className="calendly-inline-widget"
      data-url={url}
      style={{ minWidth: '320px', height: '700px' }}
    />
  )
}
```

**Variable de entorno:**
```
NEXT_PUBLIC_CALENDLY_URL=https://calendly.com/[nombre-psicologa]/primera-sesion
```

**Notas:**
- Usar embed **inline** (no popup) — el usuario no sale de la página `/agendar`
- Plan Basic de Calendly es gratuito para 1 tipo de evento
- Cuando se necesiten múltiples tipos de sesión (individual, pareja, seguimiento), actualizar a Standard ($10/mes)

---

## Wompi — Pagos Colombia

**Función:** Pasarela de pago local. Acepta tarjetas débito/crédito, PSE, Nequi y Bancolombia a la mano.

**Flujo de pago:**

```
Paciente agenda en Calendly
    → Correo de confirmación de Calendly incluye link de pago de Wompi
    → Paciente paga
    → Wompi envía webhook de confirmación
    → Correo final con link de videollamada (si es sesión virtual)
```

**Implementación:**

Wompi ofrece dos opciones:

1. **Link de pago** (más simple para empezar): se genera desde el dashboard de Wompi y se incluye manualmente en el template del correo de Calendly. Sin código.

2. **Botón embebido** (fase 2): integración con el sitio para cobrar directamente en `/agendar`.

```html
<!-- Botón de pago Wompi (opción embebida) -->
<script
  src="https://checkout.wompi.co/widget.js"
  data-render="button"
  data-public-key="pub_prod_XXXXXXXXXXXXXXXX"
  data-currency="COP"
  data-amount-in-cents="15000000"
  data-reference="SESION-{id}"
  data-signature:integrity="{hash-sha256}"
/>
```

**Credenciales:**
- `data-public-key`: clave pública de Wompi — puede estar en el frontend
- La firma de integridad (`integrity`) debe generarse en el servidor con la clave privada
- La clave privada de Wompi va en variables de entorno de Vercel — nunca en el cliente

**Comisión:** 2.9% + $900 COP por transacción exitosa. Sin costo fijo mensual.

---

## Stripe — Pagos internacionales (fase 2)

**Función:** Complemento a Wompi para pacientes con tarjetas internacionales o fuera de Colombia.

**Implementación:**

```bash
npm install @stripe/stripe-js @stripe/react-stripe-js
```

```typescript
// app/api/create-payment-intent/route.ts (edge function)
import Stripe from 'stripe'

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!)

export async function POST(req: Request) {
  const { amount } = await req.json()
  const paymentIntent = await stripe.paymentIntents.create({
    amount,
    currency: 'cop',
  })
  return Response.json({ clientSecret: paymentIntent.client_secret })
}
```

**Variables de entorno:**
```
STRIPE_SECRET_KEY=sk_live_...     # Solo en servidor (Vercel env vars)
NEXT_PUBLIC_STRIPE_KEY=pk_live_... # Cliente (con prefijo NEXT_PUBLIC_)
```

---

## Formspree — Formulario de contacto

**Función:** Recibe mensajes del formulario de contacto general y los envía al correo de la psicóloga. Sin backend propio.

**Implementación:**

```tsx
// components/ContactForm.tsx
async function handleSubmit(formData: FormData) {
  const res = await fetch('https://formspree.io/f/{FORM_ID}', {
    method: 'POST',
    body: formData,
    headers: { Accept: 'application/json' }
  })
  // manejar respuesta
}
```

**Configuración:**
- Crear formulario en formspree.io
- El `FORM_ID` es público — puede estar en el código (no es sensible)
- Configurar en Formspree: correo destinatario, mensaje de confirmación, protección spam (reCAPTCHA)

---

## Mailchimp / Brevo — Newsletter

**Función:** Gestión de suscriptores al newsletter y entrega automática de recursos PDF.

**Herramienta preferida:** Brevo (ex-Sendinblue) — interfaz más simple y plan gratuito más generoso.

**Flujo de suscripción:**

```
Usuario llena formulario en /recursos
    → POST a Brevo API → crea contacto + agrega a lista "Mindset Newsletter"
    → Trigger automático de correo de bienvenida con link de descarga del PDF
```

**Implementación (Brevo API):**

```typescript
// lib/brevo.ts
export async function subscribeToNewsletter(email: string, name: string, pdfKey: string) {
  await fetch('https://api.brevo.com/v3/contacts', {
    method: 'POST',
    headers: {
      'api-key': process.env.BREVO_API_KEY!,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      email,
      attributes: { FIRSTNAME: name },
      listIds: [2], // ID de la lista en Brevo
      updateEnabled: true,
    }),
  })
  // Trigger del correo de bienvenida con el PDF correspondiente
}
```

**Variable de entorno:**
```
BREVO_API_KEY=xkeysib-...   # Solo servidor — nunca cliente
```

---

## Plausible — Analytics

**Función:** Analítica web sin cookies y sin datos personales. Cumple Ley 1581 y GDPR sin banner de cookies.

**Instalación en Next.js:**

```tsx
// app/layout.tsx
<Script
  defer
  data-domain={process.env.NEXT_PUBLIC_PLAUSIBLE_DOMAIN}
  src="https://plausible.io/js/script.js"
/>
```

**Eventos personalizados a trackear:**

```typescript
// Completación del test
window.plausible('quiz_completed', { props: { result: 'anxiety' } })

// Apertura del chatbot
window.plausible('chatbot_opened')

// Clic en Calendly
window.plausible('calendly_click')

// Descarga de PDF
window.plausible('pdf_download', { props: { resource: 'breathing-guide' } })
```

---

*Documento: Técnico | Versión 1.0 | Abril 2026*
