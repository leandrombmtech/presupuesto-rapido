# CLAUDE.md

## Documentación completa del proyecto
Antes de responder cualquier pregunta, lee siempre docs/PROYECTO.md — es la fuente de verdad del proyecto con stack, schema SQL, roadmap, precios, marketing y todo el contexto.



This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Idioma
Responde siempre en español de España. Nunca uses inglés salvo para nombres técnicos de código (variables, funciones, comandos).

## Sobre el usuario
- El usuario se llama Leandro
- Trátale siempre de tú y llámale Leandro
- Es el fundador y único desarrollador de PresupuestoRápido
- No tiene experiencia previa en programación pero aprende rápido
- Prefiere explicaciones paso a paso con contexto de por qué se hace cada cosa
- Al inicio de cada sesión saluda a Leandro con una frase divertida diferente cada vez relacionada con startups, código o autónomos españoles

## What this is

PresupuestoRápido is a single-page web app for Spanish self-employed tradespeople (fontaneros, electricistas, etc.) to generate professional PDF quotes in seconds. It is deployed on Netlify.

## Running locally

No build step. Open `index.html` directly in a browser, or serve it with any static server:

```bash
npx serve .
# or
python -m http.server 8080
```

For the Netlify serverless function, use the Netlify CLI:

```bash
npm install -g netlify-cli
netlify dev
```

The Netlify function requires `ANTHROPIC_API_KEY` set as an environment variable (in Netlify dashboard or a local `.env` file when using `netlify dev`).

## Architecture

Everything lives in two files:

- **`index.html`** — The entire frontend: HTML structure, all CSS (`:root` variables + component styles), and ~200 lines of vanilla JS. No framework, no bundler, no dependencies beyond Google Fonts.
- **`netlify/functions/claude.js`** — Netlify serverless function that proxies requests to the Anthropic API, injecting `ANTHROPIC_API_KEY` server-side and overriding the model to `claude-haiku-4-5-20251001`.

### Important: AI call routing mismatch

The `askAI()` function in `index.html` (line 830) calls `https://api.anthropic.com/v1/messages` **directly from the browser** with no API key in the headers. This will return a 401 unless the call is rerouted to the Netlify function at `/.netlify/functions/claude`. The serverless function at `netlify/functions/claude.js` is the intended secure proxy — it injects the API key from env. When fixing AI functionality, change the `fetch` URL in `askAI()` to `/.netlify/functions/claude`.

### JS data flow

- `items[]` — in-memory array of line items `{id, desc, qty, price}`. Mutated by `addItem()`, `removeItem()`, `updateItem()`.
- `renderItems()` — full re-render of the items table; calls `updateTotals()`.
- `generatePDF()` — reads all form fields + `items[]`, builds an HTML string, injects it into `#pdf-content`, and switches to the preview tab. Print/save PDF uses `window.print()`.
- `sendWhatsApp()` — builds a `wa.me` deep-link with a text summary and opens it.

### CSS conventions

All colors and radii are CSS custom properties on `:root` (`--brand`, `--accent`, `--accent2`, `--light`, etc.). Responsive breakpoint is `max-width: 768px`.

Añade al final del archivo CLAUDE.md, sin borrar nada, el siguiente contenido:

## Contexto del negocio
- Producto: Micro-SaaS para autónomos de oficios en España
- URL producción: presupuestorapido.pro
- Hosting: Netlify (plan gratuito)
- Dominio: Porkbun → registro A → 75.2.60.5

## Planes y precios
- Gratuito: 3 presupuestos/mes (NO implementado aún)
- Pro: 7€/mes — Stripe Payment Link activo: https://buy.stripe.com/14A5kF2INeQff179zO9R600
- Empresa: 19€/mes

## Variables de entorno en Netlify
- ANTHROPIC_API_KEY: configurada ✅
- STRIPE_SECRET_KEY: pendiente ⏳
- STRIPE_WEBHOOK_SECRET: pendiente ⏳
- SUPABASE_URL: pendiente ⏳
- SUPABASE_SERVICE_KEY: pendiente ⏳

## Estado actual (Abril 2026)
### Completado ✅
- Web publicada y funcionando en presupuestorapido.pro
- IA integrada con claude-haiku-4-5-20251001
- Generador de PDF profesional
- Envío por WhatsApp
- Stripe configurado con Payment Link y 14 días de prueba
- Migrado de Netlify a Vercel (créditos Netlify agotados por auto-commit)
- Dominio apuntado a Vercel via Porkbun (A @ → 216.198.79.1)
- SSL activo en Vercel
- Proyecto Supabase creado (pendiente de integrar en código)
- Auto-commit eliminado — commits manuales

### Pendiente ⏳ en orden de prioridad
1. Conectar Supabase MCP a Claude Code
2. Conectar Vercel MCP a Claude Code
3. Implementar login con Supabase Auth
4. Ajustar prompt de la IA — nunca sugerir Excel/Word/otras herramientas, siempre enfocado en la web; mejorar formato de respuestas
5. Supabase Auth — login y registro de usuarios
6. Base de datos — tablas: users, subscriptions, budgets
7. Limitar plan gratuito a 3 presupuestos/mes
8. Marca de agua en PDFs del plan gratuito
9. Stripe webhooks — conectar pago con acceso Pro
10. Panel de usuario — historial de presupuestos
11. Política de privacidad y términos legales
12. SEO básico — meta tags, sitemap, Search Console
13. Marketing — grupos Facebook, TikTok, email

## Stack objetivo completo
- Frontend: HTML/CSS/JS vanilla (index.html)
- Functions: api/ (claude.js, auth.js, stripe-hook.js, user.js) — formato Vercel
- Base de datos: Supabase (PostgreSQL)
- Auth: Supabase Auth
- Pagos: Stripe + webhooks
- Analytics: Plausible (RGPD compliant)
- Email: Brevo

## Reglas de desarrollo
- Siempre hacer commit y push a GitHub después de cada cambio
- Nunca poner API keys en el código — siempre en variables de entorno de Netlify
- Mantener todo en un solo index.html hasta que la complejidad lo justifique
- Cada función nueva va en netlify/functions/ como archivo separado

## Despliegue a producción
- Los commits automáticos usan [skip ci] para no disparar deploy en Vercel
- Para desplegar a producción, Leandro ejecuta manualmente: vercel --prod
- Solo desplegar cuando un bloque de funcionalidad esté completo y probado

## Protocolo de trabajo
- Al inicio de cada sesión: leer CLAUDE.md y resumir estado actual + proponer siguiente paso
- Al final de cada sesión: actualizar CLAUDE.md con lo hecho, lo pendiente y decisiones tomadas
- Antes de tocar código: explicar qué vas a hacer y por qué
- Después de cada cambio: confirmar que funciona en presupuestorapido.pro
- Si algo falla: documentarlo en CLAUDE.md para no repetir el error

## Historial de sesiones
### Sesión 1 — 27 Abril 2026
- Instalado Claude Code y conectado al proyecto
- Configurado auto-commit y push a GitHub
- CLAUDE.md creado con contexto completo del negocio y técnico
- Web funcionando en presupuestorapido.pro con IA, PDF y Stripe
- Próximo paso: implementar Supabase Auth (login/registro)
- Al inicio de cada sesión revisar este archivo y preguntar qué toca hacer

### Sesión 2 — 28 Abril 2026
#### Completado ✅
- Convertido documentacion_maestra_presupuestorapido_v2.docx a docs/PROYECTO.md (fuente de verdad completa)
- Añadida referencia a docs/PROYECTO.md en CLAUDE.md para que se lea al inicio de cada sesión
- Auto-commit reactivado con [skip ci] para no disparar deploy en Vercel en cada guardado
- Creado .gitignore (faltaba — ahora protege .env y credenciales)
- Creado .claude/settings.local.json con credenciales de Supabase (gitignoreado, solo en local)
- Proyecto Supabase creado: rylbibqoxpzggmxwazde.supabase.co (región: West EU Ireland)
- MCP de Supabase configurado en settings.local.json

#### Próximo paso ⏳
- Reiniciar Claude Code para cargar el MCP de Supabase
- Verificar que el MCP está activo
- Ejecutar el schema SQL completo en Supabase (está en docs/PROYECTO.md sección 5.1)
- Implementar modal de login/registro en index.html (Día 3 del roadmap)