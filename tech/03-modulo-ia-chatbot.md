# 03 — Módulo IA / Chatbot de triaje

## Descripción

Asistente conversacional integrado como burbuja flotante en el sitio. Ayuda al paciente a identificar qué tipo de terapia podría necesitar, responde preguntas frecuentes y lo guía al agendamiento. Usa la API de Claude (Anthropic).

---

## Arquitectura

```
Usuario (browser)
    ↓ POST /api/chat
Edge function Next.js (app/api/chat/route.ts)
    ↓ ANTHROPIC_API_KEY (variable de entorno — nunca expuesta al cliente)
Claude API (claude-sonnet-4-6)
    ↓ Respuesta en streaming
Edge function
    ↓ Stream
Usuario (browser)
```

**Por qué edge function y no llamada directa desde el cliente:**
- La API key de Anthropic nunca puede estar en el cliente (se expondría en el código fuente)
- La edge function actúa como proxy seguro
- Permite agregar rate limiting, logging y validación antes de llegar a la API

---

## Implementación

### Edge function (`app/api/chat/route.ts`)

```typescript
import Anthropic from '@anthropic-ai/sdk'
import { NextRequest } from 'next/server'

export const runtime = 'edge'

const client = new Anthropic({
  apiKey: process.env.ANTHROPIC_API_KEY,
})

const SYSTEM_PROMPT = `Eres un asistente de orientación para el consultorio de psicología Mindset.
Tu función es ayudar a las personas a entender qué tipo de apoyo psicológico podrían necesitar
y orientarlos hacia el agendamiento de una primera sesión.

REGLAS ESTRICTAS:
- Nunca emitas diagnósticos clínicos de ningún tipo
- Nunca recomiendes medicación
- Nunca prometas resultados terapéuticos
- No reemplazas la terapia — eres una orientación inicial
- Ante cualquier señal de crisis, riesgo o urgencia: responde con empatía,
  proporciona el número de la Línea 106 (atención en crisis de salud mental en Colombia)
  y sugiere contacto urgente con la psicóloga

TONO:
- Cálido, empático y cercano
- No clínico ni frío
- Usa lenguaje simple — no jerga psicológica
- Preguntas abiertas — deja que la persona se exprese

FLUJO SUGERIDO:
1. Saludo cálido y pregunta abierta
2. 2-3 preguntas de seguimiento para entender la situación
3. Orientación general sobre qué tipo de sesión podría ayudar
4. Invitación a agendar con link: [CALENDLY_URL]`

export async function POST(req: NextRequest) {
  // Rate limiting básico por IP
  const ip = req.headers.get('x-forwarded-for') ?? 'unknown'
  // TODO: implementar rate limiting con Vercel KV o Upstash

  const { messages } = await req.json()

  // Validación básica
  if (!messages || !Array.isArray(messages) || messages.length > 20) {
    return new Response('Invalid request', { status: 400 })
  }

  const stream = await client.messages.stream({
    model: 'claude-sonnet-4-6',
    max_tokens: 500, // respuestas cortas para un chatbot
    system: SYSTEM_PROMPT,
    messages,
  })

  return new Response(stream.toReadableStream(), {
    headers: { 'Content-Type': 'text/event-stream' },
  })
}
```

### Widget de chatbot (`components/chatbot/ChatWidget.tsx`)

```tsx
'use client'
import { useState, useRef, useEffect } from 'react'

type Message = {
  role: 'user' | 'assistant'
  content: string
}

export function ChatWidget() {
  const [isOpen, setIsOpen] = useState(false)
  const [messages, setMessages] = useState<Message[]>([])
  const [input, setInput] = useState('')
  const [isLoading, setIsLoading] = useState(false)

  async function sendMessage() {
    if (!input.trim()) return
    const userMessage: Message = { role: 'user', content: input }
    const updatedMessages = [...messages, userMessage]
    setMessages(updatedMessages)
    setInput('')
    setIsLoading(true)

    const res = await fetch('/api/chat', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ messages: updatedMessages }),
    })

    // Procesar stream de respuesta
    const reader = res.body!.getReader()
    let assistantContent = ''
    // ... lógica de streaming
    setIsLoading(false)
  }

  return (
    <>
      {/* Burbuja flotante */}
      <button
        onClick={() => setIsOpen(true)}
        className="fixed bottom-6 right-6 w-14 h-14 rounded-full bg-green-600 shadow-lg"
        aria-label="Abrir chat de orientación"
      >
        {/* Icono de chat */}
      </button>

      {/* Ventana de chat */}
      {isOpen && (
        <div className="fixed bottom-24 right-6 w-80 h-96 bg-white rounded-2xl shadow-xl flex flex-col">
          {/* Header */}
          {/* Mensajes */}
          {/* Input */}
        </div>
      )}
    </>
  )
}
```

---

## Manejo de crisis

Ante cualquiera de estas señales en el mensaje del usuario, el sistema prompt instruye al chatbot a responder con empatía + recursos de crisis:

- Menciones de autolesión o suicidio
- Expresiones de desesperanza extrema
- Urgencia o peligro inmediato

**Respuesta estándar de crisis:**

> "Gracias por contarme esto. Lo que describes suena muy difícil, y quiero que sepas que no estás solo/a. Si en este momento sientes que necesitas apoyo urgente, puedes llamar a la **Línea 106** — es gratuita, confidencial y disponible 24/7 en Colombia. También puedes escribirle directamente a la psicóloga para una atención prioritaria."

---

## Costos estimados

| Escenario | Conversaciones/mes | Tokens aprox. | Costo estimado |
|---|---|---|---|
| Lanzamiento | 50–100 | ~150K tokens | ~$1–2 USD |
| Crecimiento | 200–500 | ~600K tokens | ~$5–10 USD |
| Escala | 1.000+ | ~2M tokens | ~$20–30 USD |

*Basado en precios de `claude-sonnet-4-6` a Abril 2026. Verificar precios actuales en console.anthropic.com*

---

## Rate limiting

Para controlar costos y prevenir abuso:

```typescript
// Límites sugeridos
const LIMITS = {
  messagesPerConversation: 20,   // máximo de turnos por sesión
  requestsPerIP: 10,             // por hora
  maxTokensPerMessage: 500,      // respuesta máxima del modelo
}
```

**Herramienta recomendada:** Upstash Redis (plan gratuito) para rate limiting por IP en edge functions.

---

## Seguridad

- La `ANTHROPIC_API_KEY` solo existe en variables de entorno de Vercel — nunca en el cliente ni en el repo
- Validación de longitud y formato de mensajes entrantes antes de llamar a la API
- El historial de conversación no se almacena en base de datos — solo en memoria del cliente (se pierde al cerrar/recargar)
- No se registran datos personales durante la conversación del chatbot

---

## Variables de entorno

```
ANTHROPIC_API_KEY=sk-ant-...   # Solo servidor — configurar en Vercel dashboard
```

---

*Documento: Técnico | Versión 1.0 | Abril 2026*

