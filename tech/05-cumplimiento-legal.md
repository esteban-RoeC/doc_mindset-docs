# 05 — Cumplimiento legal

## Marco legal aplicable

El sitio opera en Colombia y maneja datos de personas naturales. La ley aplicable es:

- **Ley 1581 de 2012** — Protección de Datos Personales (Habeas Data)
- **Decreto 1377 de 2013** — Reglamentación de la Ley 1581
- **Resolución SIC** — Directrices de la Superintendencia de Industria y Comercio

---

## Datos que SÍ maneja la web

| Dato | Dónde se captura | Para qué se usa | Dónde se almacena |
|---|---|---|---|
| Nombre | Formulario de recursos / contacto | Personalizar comunicación | Mailchimp / Brevo + Supabase |
| Correo electrónico | Formulario de recursos / contacto / Calendly | Envío de recursos, confirmaciones, newsletter | Mailchimp / Brevo + Supabase |
| Motivo general de consulta | Preguntas de pre-sesión en Calendly | Que la psicóloga llegue preparada a la primera sesión | Calendly (sistema externo) |
| Preferencia de modalidad | Calendly | Coordinar virtual o presencial | Calendly (sistema externo) |
| Resultado del test | Supabase (anónimo, sin identificar al usuario) | Analítica de producto | Supabase |

---

## Datos que NO maneja la web

- ❌ Historia clínica — responsabilidad exclusiva de la psicóloga
- ❌ Diagnósticos o impresiones clínicas
- ❌ Notas de sesión o evolución terapéutica
- ❌ Datos de salud sensibles (categoría especial bajo Ley 1581)
- ❌ Número de identificación, dirección física, teléfono (no se solicitan)
- ❌ Datos de menores de edad (el sitio no atiende menores)
- ❌ Datos bancarios o de tarjeta (los maneja Wompi / Stripe en sus sistemas)

---

## Requisitos obligatorios en el sitio

### 1. Página `/privacidad`

Debe incluir:

- Identidad y datos de contacto del responsable del tratamiento (la psicóloga / su consultorio)
- Finalidad del tratamiento de datos
- Datos que se recopilan y por qué
- Derechos del titular (ARCO): Acceso, Rectificación, Cancelación, Oposición
- Forma de ejercer esos derechos (correo de contacto)
- Política de retención: cuánto tiempo se guardan los datos
- Uso de cookies (si aplica)
- Fecha de vigencia de la política

### 2. Consentimiento informado en formularios

Todo formulario que captura datos personales debe incluir:

```tsx
<label className="flex items-start gap-2 text-sm text-gray-600">
  <input type="checkbox" required className="mt-1" />
  <span>
    Acepto el tratamiento de mis datos personales según la{' '}
    <a href="/privacidad" className="underline">política de privacidad</a>{' '}
    de Mindset, de acuerdo con la Ley 1581 de 2012.
  </span>
</label>
```

El checkbox debe ser:
- No pre-marcado
- Obligatorio (`required`) — no se puede enviar el formulario sin marcarlo

### 3. Derechos ARCO

Incluir en `/privacidad` un correo o formulario específico para:
- **Acceso:** el paciente puede pedir qué datos tenemos sobre él/ella
- **Rectificación:** puede pedir corregir datos incorrectos
- **Cancelación:** puede pedir que eliminemos sus datos
- **Oposición:** puede oponerse a ciertos usos de sus datos

**Tiempo de respuesta:** 10 días hábiles según la ley.

### 4. Cookies

El sitio usa Plausible Analytics, que **no usa cookies** y no recopila datos personales. Por esto, no se requiere banner de cookies bajo Ley 1581.

Si en el futuro se agrega Google Analytics u otra herramienta con cookies, se debe implementar un banner de consentimiento previo.

### 5. Retención de datos

Política recomendada:

| Dato | Retención |
|---|---|
| Suscriptores al newsletter | Mientras estén activos. Se eliminan si piden baja o tras 2 años de inactividad |
| Resultados del test (anónimos) | 1 año para analítica |
| Mensajes de contacto | 6 meses |

---

## Checklist de cumplimiento para el equipo

Antes del primer deploy en producción, verificar:

- [ ] Página `/privacidad` publicada con todos los elementos requeridos
- [ ] Checkbox de consentimiento en formulario de recursos PDF
- [ ] Checkbox de consentimiento en formulario de contacto
- [ ] Correo de ARCO configurado y respondiendo
- [ ] RLS activado en todas las tablas de Supabase
- [ ] Variables de entorno con datos sensibles configuradas solo en Vercel (no en repo)
- [ ] Ningún `console.log` con datos de usuario en producción
- [ ] Plausible configurado (sin cookies, sin datos personales)
- [ ] El sistema prompt del chatbot incluye la restricción de no emitir diagnósticos
- [ ] El test de auto-exploración muestra el disclaimer de "no es un diagnóstico"

---

## Consideraciones éticas adicionales

Más allá del cumplimiento legal, el equipo debe tener en cuenta:

**Sobre el chatbot:**
- El system prompt debe ser revisado y aprobado por la psicóloga antes de activar en producción
- Ante cualquier señal de crisis, el chatbot siempre prioriza la derivación a recursos de emergencia (Línea 106) sobre continuar la conversación de orientación

**Sobre el test:**
- El resultado nunca debe presentarse como diagnóstico
- El disclaimer debe ser visible — no en letra pequeña

**Sobre los testimonios:**
- Solo con consentimiento escrito del paciente
- Solo iniciales o anónimo — nunca nombre completo ni foto

**Sobre el blog:**
- Disclaimer al final de cada artículo de salud mental: "Este contenido es informativo y no reemplaza la consulta con un profesional."

---

*Documento: Técnico | Versión 1.0 | Abril 2026*
