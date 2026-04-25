# Mindset — Documentación del Proyecto

Repositorio de documentación técnica y de negocio para la plataforma web de psicología **Mindset**.

> Este repositorio es de uso exclusivo del equipo de desarrollo. No contiene información sensible de pacientes ni de la psicóloga más allá de lo estrictamente necesario para el desarrollo.

---

## Estructura del repositorio

```
doc_mindset-docs/
│
├── README.md               ← Este archivo — índice general
├── PROGRESS.md             ← Estado actual del proyecto y continuidad
│
├── business/               ← Documentación de negocio y producto
│   ├── 01-vision-y-objetivos.md
│   ├── 02-arquitectura-de-contenido.md
│   ├── 03-modulos-de-innovacion.md
│   └── 04-roadmap.md
│
└── tech/                   ← Documentación técnica
    ├── 01-stack-tecnologico.md
    ├── 02-integraciones.md
    ├── 03-modulo-ia-chatbot.md
    ├── 04-base-de-datos.md
    └── 05-cumplimiento-legal.md
```

---

## Documentación de negocio

| Archivo | Contenido |
|---|---|
| [01 — Visión y objetivos](./business/01-vision-y-objetivos.md) | Qué es el proyecto, para quién, metas de negocio |
| [02 — Arquitectura de contenido](./business/02-arquitectura-de-contenido.md) | Mapa de sitio, páginas, flujo del paciente |
| [03 — Módulos de innovación](./business/03-modulos-de-innovacion.md) | Test de auto-exploración, chatbot, video, recursos PDF |
| [04 — Roadmap](./business/04-roadmap.md) | Fases de desarrollo, tiempos y métricas de éxito |

## Documentación técnica

| Archivo | Contenido |
|---|---|
| [01 — Stack tecnológico](./tech/01-stack-tecnologico.md) | Framework, hosting, CMS y justificaciones |
| [02 — Integraciones](./tech/02-integraciones.md) | Calendly, Wompi, Formspree, Mailchimp |
| [03 — Módulo IA / Chatbot](./tech/03-modulo-ia-chatbot.md) | Arquitectura del chatbot, edge function, costos |
| [04 — Base de datos](./tech/04-base-de-datos.md) | Esquema Supabase, tablas, RLS |
| [05 — Cumplimiento legal](./tech/05-cumplimiento-legal.md) | Ley 1581, scope de datos, lo que NO maneja la web |

---

## Convenciones del repo

- Todo en **español**
- Formato **Markdown** (.md)
- Sin credenciales, API keys ni datos personales en ningún archivo
- El archivo `PROGRESS.md` se actualiza al final de cada sesión de trabajo

---

## Contacto del proyecto

- **Repositorio:** https://github.com/esteban-RoeC/doc_mindset-docs
- **Estado actual:** ver [PROGRESS.md](./PROGRESS.md)
