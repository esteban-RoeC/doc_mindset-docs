# 01 — Visión y objetivos

## Descripción del proyecto

**Mindset** es una plataforma web para una psicóloga independiente. Su propósito es presentar los servicios profesionales, generar confianza en el visitante y convertirlo en paciente mediante un flujo de onboarding inteligente — desde el primer contacto hasta la primera sesión pagada.

El sitio no es un sistema clínico. No gestiona historias clínicas ni evolución terapéutica. Su función termina cuando el paciente agenda y paga su primera cita.

---

## Problema que resuelve

Buscar ayuda psicológica tiene una barrera de entrada alta: vergüenza, desconocimiento del proceso, miedo al juicio y fricción para encontrar un profesional de confianza. Una web genérica con foto y número de teléfono no baja esa barrera.

Mindset resuelve esto con un flujo progresivo de confianza:

```
Llega al sitio
    → Ve video de la psicóloga (voz, gestos, ambiente)
    → Hace test de auto-exploración (entiende qué necesita)
    → Chatbot lo orienta (proceso humanizado, no formulario frío)
    → Agenda en Calendly (sin fricción, con preguntas de pre-sesión)
    → Paga (Wompi / Stripe)
    → Cita confirmada
```

Cada paso reduce una barrera específica antes de pedir el compromiso económico.

---

## Público objetivo

- **Principal:** Adultos en Colombia (25–50 años) buscando terapia individual por ansiedad, estrés, duelo o desarrollo personal
- **Secundario:** Parejas buscando terapia de pareja
- **Canal:** Mayoría llega por búsqueda orgánica en Google o recomendación directa
- **Dispositivo:** Más del 70% del tráfico en Colombia llega desde móvil — diseño mobile-first

---

## Objetivos de negocio

| Objetivo | Métrica | Meta a 6 meses |
|---|---|---|
| Convertir visitantes en pacientes | Tasa de conversión visita → cita | > 3% |
| Posicionamiento local en Google | Ranking "psicóloga [ciudad]" | Top 3 |
| Construir base de leads | Suscriptores al newsletter | 200+ |
| Fidelización | NPS post-primera sesión | > 70 |
| Completación del test | Usuarios que terminan el test | > 60% |

---

## Principios de diseño del producto

1. **Calma visual** — paleta de verdes suaves, tipografía humanista, espaciado generoso. El sitio debe sentirse como el consultorio.
2. **Fricción mínima** — el usuario llega a una cita en máximo 3 clics desde cualquier sección.
3. **Confianza progresiva** — no se pide nada antes de haber dado valor (video → test → orientación → agendar).
4. **Mobile-first** — diseño y rendimiento optimizados para móvil desde el inicio.
5. **SEO desde el día uno** — estructura semántica, blog, schema.org de profesional de salud.
6. **Sin datos innecesarios** — solo se captura lo mínimo para agendar. Nada más.

---

## Alcance del proyecto (scope)

### ✅ Dentro del scope

- Presentación de la psicóloga y sus servicios
- Agendamiento de citas (Calendly)
- Procesamiento de pagos (Wompi / Stripe)
- Captura de: nombre, correo, motivo general de consulta
- Test de auto-exploración de bienestar emocional
- Chatbot de triaje para orientar al paciente
- Blog de bienestar mental (SEO)
- Recursos PDF descargables (lead magnets)
- Newsletter mensual

### ❌ Fuera del scope

- **Historias clínicas** — responsabilidad exclusiva de la psicóloga con sus propias herramientas
- Notas de sesión o evolución clínica
- Diagnósticos de cualquier tipo (ni el chatbot ni el test los emiten)
- Datos médicos o de salud sensibles
- Sistema multi-profesional o multi-consultorio
- Videollamadas o plataforma de sesión virtual (se usa Zoom / Google Meet externo)

---

*Documento: Negocio | Versión 1.0 | Abril 2026*
