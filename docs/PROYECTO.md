# Documentación Maestra — PresupuestoRápido

> **SaaS · Presupuestos Profesionales para Autónomos de Oficios en España**
> Versión 2.0 — Abril 2026 | [presupuestorapido.pro](https://presupuestorapido.pro)

| < 30€ Inversión total | 600K+ Mercado objetivo | 0 Competidores directos | 85%+ Margen bruto |
| --- | --- | --- | --- |

| URL producción | presupuestorapido.pro |
| --- | --- |
| Repositorio | github.com/leandrombmtech/presupuesto-rapido |
| Fundador | Leandro Bermúdez |
| Stack | HTML + Vercel Functions + Supabase + Stripe + Claude AI |
| Versión documento | 2.0 — Abril 2026 |

## 1. Resumen Ejecutivo

### 1.1 El problema

Más de 600.000 autónomos en España trabajan en sectores de construcción y servicios (fontanería, electricidad, pintura, carpintería, reformas). El 80% de ellos envían presupuestos a mano, en Word mal maquetado o directamente por mensaje de WhatsApp sin ningún formato profesional.

Las consecuencias son directas: los clientes no perciben profesionalidad, regatean más el precio, y los trabajos se pierden ante competidores que sí presentan documentos serios. Además, preparar un presupuesto en Word tarda entre 15 y 30 minutos cada vez.

### 1.2 La solución

PresupuestoRápido es una herramienta web Micro-SaaS que permite generar presupuestos PDF profesionales en menos de 30 segundos. El usuario rellena un formulario intuitivo, la IA integrada ayuda a redactar las descripciones, el IVA se calcula solo, y el presupuesto se envía directamente al cliente por WhatsApp.

> Propuesta de valor en una frase
> "En 30 segundos cualquier fontanero, electricista o pintor genera un presupuesto profesional con su logo, IVA calculado y lo manda por WhatsApp — sin instalaciones, sin registro obligatorio, desde el móvil."

### 1.3 Modelo de negocio

| Plan | Precio | Incluye | Objetivo |
| --- | --- | --- | --- |
| Gratuito | 0 €/mes | 3 presupuestos/mes, PDF con marca de agua | Adquisición y validación |
| Pro | 7 €/mes | Ilimitado, sin marca de agua, IA ilimitada, historial | Monetización principal |
| Empresa | 19 €/mes | Todo Pro + 5 usuarios, firma digital, informes | Expansión B2B |

### 1.4 Tracción actual

| ✅ Web publicada | ✅ IA funcionando | ✅ Stripe activo | ✅ Dominio propio |
| --- | --- | --- | --- |

## 2. Stack Tecnológico

### 2.1 Decisión arquitectónica

La arquitectura se optimiza para que UNA persona pueda mantener y escalar el producto. Se priorizan los planes gratuitos al máximo, la simplicidad sobre la complejidad, y herramientas con MCP disponible para que Claude Code pueda interactuar directamente con cada servicio.

| Componente | Servicio | Justificación | Coste |
| --- | --- | --- | --- |
| Frontend | HTML5 + CSS3 + JS vanilla | Un solo index.html, sin frameworks, sin build step | 0 €/mes |
| Hosting | Vercel | Deploy automático desde GitHub, funciones serverless | 0 €/mes |
| IA | Anthropic Claude API | claude-haiku-4-5-20251001 via función serverless | ~2 €/mes |
| Base de datos | Supabase (PostgreSQL) | Auth + DB + Realtime, 500MB gratis | 0 €/mes |
| Pagos | Stripe | Suscripciones recurrentes, webhooks, portal cliente | 0 € + 1.5% |
| Dominio | Porkbun | presupuestorapido.pro | ~0.25 €/mes |
| Analytics | Plausible | RGPD compliant, sin cookies | 0 € (self-host) |
| Email | Brevo | Secuencias automatizadas, 300 emails/día gratis | 0 €/mes |
| Control de versiones | GitHub | Repositorio privado, CI/CD trigger | 0 €/mes |
| TOTAL INICIAL |  | ~2.25 €/mes |

### 2.2 Estructura de carpetas objetivo

```text
presupuesto-rapido/
├── index.html                  # Frontend completo (HTML + CSS + JS)
├── CLAUDE.md                   # Instrucciones para Claude Code
├── netlify.toml                # (Legacy — migrado a Vercel)
├── vercel.json                 # Configuración de Vercel
├── .claude/
│   └── settings.json          # Config de auto-commit
├── api/                        # Funciones serverless de Vercel
│   ├── claude.js              # Proxy seguro a Anthropic API
│   ├── auth.js                # Registro, login, perfil
│   ├── stripe-hook.js         # Webhooks de Stripe
│   ├── budgets.js             # CRUD de presupuestos
│   └── user.js                # Gestión de plan y usuario
├── docs/
│   ├── AGENTS.md              # Instrucciones por agente
│   └── SCHEMA.sql             # Schema completo de Supabase
└── public/
    └── assets/                # Logo, imágenes
```

## 3. Arquitectura de Agentes Claude Code

### 3.1 Por qué múltiples agentes

Claude Code permite abrir múltiples instancias en paralelo, cada una especializada en un área del proyecto. Esto evita que un agente toque código que no conoce bien y permite trabajar en frontend, backend y base de datos simultáneamente.

| Agente | Archivo de instrucciones | Área de responsabilidad |
| --- | --- | --- |
| Agente Principal | CLAUDE.md raíz | Coordinación, decisiones de arquitectura, roadmap |
| Agente Frontend | docs/AGENT_FRONTEND.md | index.html — UI, CSS, JS del navegador |
| Agente Backend | docs/AGENT_BACKEND.md | api/ — funciones serverless, lógica de negocio |
| Agente DB | docs/AGENT_DB.md | Supabase — schema, migraciones, RLS, queries |
| Agente Marketing | docs/AGENT_MARKETING.md | SEO, contenido, emails, redes sociales |

### 3.2 Cómo abrir cada agente

Cada agente se abre en una ventana de PowerShell diferente apuntando al mismo repositorio:

```bash
# Terminal 1 — Agente Principal
cd E:\Leandro\Escritorio\presupuesto-rapido
claude

# Terminal 2 — Agente Frontend (nueva ventana PowerShell)
cd E:\Leandro\Escritorio\presupuesto-rapido
claude --context docs/AGENT_FRONTEND.md

# Terminal 3 — Agente Backend
cd E:\Leandro\Escritorio\presupuesto-rapido
claude --context docs/AGENT_BACKEND.md
```

### 3.3 Protocolo de trabajo multi-agente

- El Agente Principal decide qué tarea va a hacer y en qué archivo
- Comunica al agente especializado exactamente qué debe hacer
- El agente especializado implementa, hace commit automático
- El Agente Principal verifica el resultado en presupuestorapido.pro
- Si algo falla, se revierte con: git revert HEAD
- Al final de la sesión, el Agente Principal actualiza el CLAUDE.md

### 3.4 Archivo CLAUDE.md del Agente Frontend

Crear el archivo docs/AGENT_FRONTEND.md con este contenido:

```javascript
# Agente Frontend — PresupuestoRápido

## Tu responsabilidad
Solo tocas index.html. Nunca tocas archivos de api/ ni docs/.

## Stack
- HTML5 + CSS3 + JS vanilla (sin frameworks)
- Colores: --brand:#1A1A2E --accent:#E8632A --light:#FAF8F5
- Fuentes: Fraunces (títulos) + DM Sans (cuerpo) via Google Fonts
- Breakpoint responsive: max-width: 768px

## Llamadas a API (siempre así, nunca directo a Anthropic)
fetch('/.netlify/functions/claude') // Legacy Netlify
fetch('/api/claude')               // Vercel (usar este)

## Reglas
- No uses localStorage para datos de usuario — usa Supabase
- Toda lógica de plan (free/pro) debe verificarse en el backend
- Los PDFs se generan 100% en el navegador con window.print()
- Antes de cada cambio explica qué vas a hacer y en qué línea
```

### 3.5 Archivo CLAUDE.md del Agente Backend

```javascript
# Agente Backend — PresupuestoRápido

## Tu responsabilidad
Solo tocas archivos en api/. Nunca tocas index.html.

## Variables de entorno disponibles (Vercel dashboard)
ANTHROPIC_API_KEY=sk-ant-...
STRIPE_SECRET_KEY=sk_live_...
STRIPE_WEBHOOK_SECRET=whsec_...
SUPABASE_URL=https://xxx.supabase.co
SUPABASE_SERVICE_KEY=eyJ...

## Patrón de función Vercel
export default async function handler(req, res) {
  if (req.method !== 'POST') return res.status(405).end();
  // lógica aquí
  res.status(200).json({ ok: true });
}

## Reglas
- Siempre verificar JWT de Supabase antes de dar datos
- Nunca confiar en datos del frontend para permisos
- Log de errores con console.error para ver en Vercel dashboard
```

## 4. MCPs y Herramientas Conectadas

### 4.1 Qué es MCP

MCP (Model Context Protocol) permite a Claude Code interactuar directamente con servicios externos — Supabase, Vercel, Stripe, GitHub — sin que Leandro tenga que copiar y pegar código manualmente. Claude Code puede crear tablas en Supabase, ver logs en Vercel y revisar el estado de pagos en Stripe directamente desde el terminal.

### 4.2 MCPs recomendados

| MCP | Para qué sirve | Comando instalación | Prioridad |
| --- | --- | --- | --- |
| Supabase MCP | Crear tablas, ejecutar SQL, gestionar usuarios | npx @supabase/mcp-server-supabase | Alta |
| Vercel MCP | Ver deploys, logs, variables de entorno | npx @vercel/mcp-adapter | Alta |
| Stripe MCP | Ver pagos, clientes, suscripciones | npx @stripe/agent-toolkit | Media |
| GitHub MCP | Ver PRs, issues, commits | npx @github/mcp-server | Media |
| Playwright MCP | Testear la web automáticamente | npx @playwright/mcp | Media |

### 4.3 Configuración JSON para Claude Code

Crear o editar el archivo .claude/settings.json con esta configuración completa:

```json
{
  "mcpServers": {
    "supabase": {
      "command": "npx",
      "args": ["-y", "@supabase/mcp-server-supabase"],
      "env": {
        "SUPABASE_URL": "https://TU_PROJECT.supabase.co",
        "SUPABASE_SERVICE_ROLE_KEY": "eyJ..."
      }
    },
    "vercel": {
      "command": "npx",
      "args": ["-y", "@vercel/mcp-adapter"],
      "env": {
        "VERCEL_TOKEN": "tu_token_de_vercel"
      }
    },
    "stripe": {
      "command": "npx",
      "args": ["-y", "@stripe/agent-toolkit"],
      "env": {
        "STRIPE_SECRET_KEY": "sk_live_..."
      }
    }
  },
  "hooks": {
    "PostToolUse": [{
      "matcher": "Write|Edit",
      "hooks": [{
        "type": "command",
        "command": "git add -A && git diff --cached --quiet || (git commit -m \"Auto-commit: cambios de Claude\" && git push origin main)",
        "timeout": 30,
        "statusMessage": "Haciendo commit y push a GitHub..."
      }]
    }]
  }
}
```

## 5. Modelo de Datos — Schema Supabase

### 5.1 Schema SQL completo

Ejecutar este SQL en el editor de Supabase para crear todas las tablas:

```sql
-- ══ TABLA: users (gestionada por Supabase Auth) ══
-- Supabase crea auth.users automáticamente.
-- Creamos una tabla pública para datos adicionales:

CREATE TABLE public.profiles (
  id            UUID REFERENCES auth.users(id) PRIMARY KEY,
  email         TEXT NOT NULL,
  full_name     TEXT,
  business_name TEXT,
  business_nif  TEXT,
  phone         TEXT,
  address       TEXT,
  logo_url      TEXT,
  created_at    TIMESTAMPTZ DEFAULT NOW(),
  updated_at    TIMESTAMPTZ DEFAULT NOW()
);

-- ══ TABLA: subscriptions ══
CREATE TABLE public.subscriptions (
  id                     UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id                UUID REFERENCES auth.users(id) NOT NULL,
  plan                   TEXT DEFAULT 'free' CHECK (plan IN ('free','pro','empresa')),
  status                 TEXT DEFAULT 'active' CHECK (status IN ('active','trialing','canceled','past_due')),
  stripe_customer_id     TEXT UNIQUE,
  stripe_subscription_id TEXT UNIQUE,
  trial_ends_at          TIMESTAMPTZ,
  current_period_end     TIMESTAMPTZ,
  created_at             TIMESTAMPTZ DEFAULT NOW(),
  updated_at             TIMESTAMPTZ DEFAULT NOW()
);

-- ══ TABLA: budgets (presupuestos) ══
CREATE TABLE public.budgets (
  id             UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id        UUID REFERENCES auth.users(id) NOT NULL,
  budget_number  TEXT NOT NULL,
  client_name    TEXT NOT NULL,
  client_phone   TEXT,
  client_address TEXT,
  description    TEXT,
  items          JSONB NOT NULL DEFAULT '[]',
  subtotal       DECIMAL(10,2) DEFAULT 0,
  iva_pct        INTEGER DEFAULT 21,
  iva_amount     DECIMAL(10,2) DEFAULT 0,
  total          DECIMAL(10,2) DEFAULT 0,
  status         TEXT DEFAULT 'draft' CHECK (status IN ('draft','sent','accepted','rejected')),
  notes          TEXT,
  valid_days     INTEGER DEFAULT 30,
  created_at     TIMESTAMPTZ DEFAULT NOW()
);

-- ══ TABLA: usage_counters ══
CREATE TABLE public.usage_counters (
  user_id         UUID REFERENCES auth.users(id) PRIMARY KEY,
  budgets_this_month INTEGER DEFAULT 0,
  ai_queries_this_month INTEGER DEFAULT 0,
  reset_date      DATE DEFAULT DATE_TRUNC('month', NOW()),
  updated_at      TIMESTAMPTZ DEFAULT NOW()
);

-- ══ ROW LEVEL SECURITY ══
ALTER TABLE public.profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.subscriptions ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.budgets ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.usage_counters ENABLE ROW LEVEL SECURITY;

-- Políticas: cada usuario solo ve sus propios datos
CREATE POLICY 'profiles_own' ON public.profiles FOR ALL USING (auth.uid() = id);
CREATE POLICY 'subs_own' ON public.subscriptions FOR ALL USING (auth.uid() = user_id);
CREATE POLICY 'budgets_own' ON public.budgets FOR ALL USING (auth.uid() = user_id);
CREATE POLICY 'usage_own' ON public.usage_counters FOR ALL USING (auth.uid() = user_id);

-- ══ ÍNDICES ══
CREATE INDEX idx_budgets_user_id ON public.budgets(user_id);
CREATE INDEX idx_budgets_created_at ON public.budgets(created_at DESC);
CREATE INDEX idx_subs_stripe_customer ON public.subscriptions(stripe_customer_id);

-- ══ TRIGGER: crear perfil automáticamente al registrarse ══
CREATE OR REPLACE FUNCTION public.handle_new_user()
RETURNS TRIGGER AS $$
BEGIN
  INSERT INTO public.profiles (id, email) VALUES (NEW.id, NEW.email);
  INSERT INTO public.subscriptions (user_id, plan, status) VALUES (NEW.id, 'free', 'active');
  INSERT INTO public.usage_counters (user_id) VALUES (NEW.id);
  RETURN NEW;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

CREATE TRIGGER on_auth_user_created
  AFTER INSERT ON auth.users
  FOR EACH ROW EXECUTE FUNCTION public.handle_new_user();
```

## 6. API y Contratos

| Método | Ruta | Auth | Descripción |
| --- | --- | --- | --- |
| POST | /api/claude | No | Proxy a Anthropic — asistente IA del formulario |
| POST | /api/auth/register | No | Registro de nuevo usuario con email+password |
| POST | /api/auth/login | No | Login — devuelve JWT de Supabase |
| GET | /api/user/profile | Sí | Perfil del usuario autenticado |
| PUT | /api/user/profile | Sí | Actualizar nombre, empresa, NIF, logo |
| GET | /api/user/plan | Sí | Plan actual, límites, uso del mes |
| GET | /api/budgets | Sí | Lista de presupuestos del usuario |
| POST | /api/budgets | Sí | Crear nuevo presupuesto (verifica límite plan) |
| GET | /api/budgets/:id | Sí | Detalle de un presupuesto |
| PUT | /api/budgets/:id | Sí | Actualizar presupuesto |
| DELETE | /api/budgets/:id | Sí | Eliminar presupuesto |
| POST | /api/stripe/webhook | No (firma) | Eventos de Stripe (checkout, cancelación) |
| POST | /api/stripe/portal | Sí | Generar enlace al portal de cliente Stripe |

## 7. Análisis Financiero

### 7.1 Inversión inicial única

| Concepto | Importe | Detalle | Estado |
| --- | --- | --- | --- |
| Dominio presupuestorapido.pro | 3,09 $ | Porkbun — primer año | ✅ Pagado |
| Créditos API Anthropic | 10 $ | Para desarrollo y primeros usuarios | ✅ Pagado |
| Claude Pro (1 mes) | 20 $ | Para desarrollo con Claude Code | ✅ Pagado |
| TOTAL INVERSIÓN INICIAL | ~33 $ |  |

### 7.2 Costes fijos mensuales

| Servicio | Coste | Detalle | Cuándo escalar |
| --- | --- | --- | --- |
| Vercel | 0 € | Plan gratuito — 100GB, funciones ilimitadas | Escalar a 20$/mes con >100K visitas |
| Supabase | 0 € | Plan gratuito — 500MB, 50K usuarios | Escalar a 25$/mes con >50K usuarios |
| Anthropic API | ~2 € | ~0.001€/consulta × 2.000 consultas/mes | Escala con uso — siempre barato |
| Dominio | 0,25 € | Prorrateo anual 3€/año | Fijo |
| Stripe | 0 € fijo | Solo 1,5% + 0,25€ por transacción | Variable — solo cuando cobras |
| GitHub | 0 € | Plan gratuito | Fijo |
| Brevo (email) | 0 € | 300 emails/día gratis | Escalar a 25$/mes con >9K/mes |
| TOTAL | ~2,25 €/mes |  |

### 7.3 Unit Economics

| Concepto | Importe | Notas |
| --- | --- | --- |
| Precio plan Pro | 7,00 €/mes |  |
| Coste Stripe por transacción | 0,36 € | 1,5% + 0,25€ |
| Coste API Anthropic por usuario activo | 0,10 € | ~100 consultas/mes × 0,001€ |
| Coste infraestructura por usuario | ~0,05 € | Prorrateo de hosting |
| MARGEN BRUTO POR CLIENTE | 6,49 €/mes | 92,7% de margen |
| LTV estimado (12 meses retención media) | 77,88 € |  |
| CAC objetivo | < 5 € | Solo tiempo propio en redes sociales |
| Payback period | < 1 mes |  |

### 7.4 Proyección P&L mes a mes — Año 1

| Mes | Clientes Pro | Ingresos € | Costes € | Beneficio € | Notas |
| --- | --- | --- | --- | --- | --- |
| Mes 1 | 0 | 2,25 | −2,25 | Desarrollo, 0 clientes de pago |
| Mes 2 | 5 | 35 | 2,25 | 32,75 | Primeros usuarios por Facebook/TikTok |
| Mes 3 | 15 | 105 | 2,25 | 102,75 | Boca a boca, SEO empezando |
| Mes 4 | 30 | 210 | 2,50 | 207,50 | Crecimiento orgánico |
| Mes 5 | 50 | 350 | 3,00 | 347,00 | SEO trayendo tráfico |
| Mes 6 | 80 | 560 | 4,00 | 556,00 | 100 MRR objetivo superado |
| Mes 7 | 100 | 700 | 5,00 | 695,00 | Milestone: 700€ MRR |
| Mes 8 | 120 | 840 | 5,50 | 834,50 |  |
| Mes 9 | 150 | 1.050 | 6,00 | 1.044,00 |  |
| Mes 10 | 180 | 1.260 | 7,00 | 1.253,00 |  |
| Mes 11 | 220 | 1.540 | 8,00 | 1.532,00 |  |
| Mes 12 | 280 | 1.960 | 10,00 | 1.950,00 | Objetivo año 1 |

> Break-even
Con solo 1 cliente de pago los costes variables están cubiertos.
El break-even real (recuperar inversión inicial de 33$) se alcanza con 5 clientes pagando 1 mes.
Esto hace que el riesgo financiero sea prácticamente nulo.

## 8. Marketing y Ventas

### 8.1 Perfil del Cliente Ideal (ICP)

| Demografía | Hombre, 30-55 años, España |
| --- | --- |
| Oficio | Fontanero, electricista, pintor, carpintero, reformista |
| Situación laboral | Autónomo individual o con 1-2 empleados |
| Dolor principal | Pierde tiempo con presupuestos y los clientes le regatean |
| Relación con tecnología | Usa WhatsApp, YouTube y Facebook. No usa software de gestión. |
| Disposición a pagar | Paga si ve que le ahorra tiempo y le hace ganar más trabajos |
| Canal de descubrimiento | Grupos de Facebook, TikTok, recomendación de otros autónomos |

### 8.2 Canales de adquisición priorizados

| Canal | Inicio | Coste | ROI | Cómo |
| --- | --- | --- | --- | --- |
| Facebook Groups | Semana 1 | 0 € | Alto | Grupos de autónomos de oficios — demo del producto |
| TikTok / Reels | Semana 2 | 0 € | Alto | Vídeo de 60 seg grabando la pantalla — antes/después |
| SEO orgánico | Mes 2+ | 0 € | Muy alto | Keywords: 'presupuesto fontanero gratis PDF' |
| Boca a boca | Mes 2+ | 0 € | Alto | Usuarios satisfechos recomiendan a colegas |
| Email marketing | Mes 3+ | 0 € | Medio | Secuencia de 5 emails post-registro |
| Asociaciones | Mes 4+ | 0 € | Medio | UATAE, ATA, escuelas FP de oficios |
| Google Ads | Mes 6+ | ~50 €/mes | Alto | Solo cuando haya datos de conversión |

### 8.3 Plantilla de post para Facebook

> Texto listo para publicar en grupos
> Hola a todos 👋
> 
> ¿Cuánto tardáis en hacer un presupuesto? Yo tardaba 20 minutos en Word.
> 
> He creado una herramienta GRATUITA que lo hace en 30 segundos:
> ✅ PDF profesional con tus datos y los del cliente
> ✅ IVA calculado automáticamente (21%, 10% o sin IVA)
> ✅ Lo mandas directo al cliente por WhatsApp
> ✅ Asistente IA que te ayuda a redactar los conceptos
> 
> Sin registro, sin instalación: presupuestorapido.pro
> 
> ¿Qué os parece? ¿Le daríais uso?

### 8.4 Guión TikTok/Reel — 60 segundos

| Tiempo | Sección | Contenido |
| --- | --- | --- |
| 0-5s | GANCHO | "¿Sigues haciendo los presupuestos en Word? Para. 🛑" |
| 5-15s | PROBLEMA | Mostrar pantalla con Word feo, mal maquetado, tachado con X roja |
| 15-40s | DEMO | Grabar pantalla rellenando el formulario en tiempo real |
| 40-50s | RESULTADO | Mostrar el PDF profesional generado — zoom en el total |
| 50-60s | CTA | "Gratis en presupuestorapido.pro — link en bio 👆" |

### 8.5 Secuencia de emails automatizados — 30 días

| Día | Tipo | Asunto | Contenido |
| --- | --- | --- | --- |
| Día 0 | Bienvenida | ¡Bienvenido a PresupuestoRápido, {nombre}! | Tutorial en 3 pasos + enlace a la herramienta |
| Día 1 | Valor | 5 errores que pierden trabajos | Consejos de presentación de presupuestos |
| Día 3 | Social proof | Cómo Manuel cerró 3 trabajos más | Caso de éxito de un fontanero |
| Día 5 | Soft pitch | ¿Qué tal va todo, {nombre}? | Preguntar feedback + mencionar plan Pro |
| Día 7 | Urgencia | Quedan 7 días de prueba Pro | Recordatorio de fin de trial |
| Día 10 | Último aviso | Tu prueba termina mañana | CTA directo a Stripe |
| Día 14 | Post-trial | ¿Qué decidiste, {nombre}? | Ofrecer descuento 20% primer mes |
| Día 30 | Re-engagement | Te echamos de menos | Descuento del 30% para volver |

## 9. Onboarding de Nuevos Usuarios

### 9.1 Flujo de registro paso a paso

- Usuario llega a presupuestorapido.pro desde Facebook, TikTok o SEO
- Ve el hero con el mockup del presupuesto — scrollea hacia la herramienta
- Intenta generar su 4º presupuesto — aparece modal de límite
- Modal muestra: 'Has usado tus 3 presupuestos gratuitos este mes'
- Opción A: 'Crear cuenta gratis' — registro con email + contraseña
- Opción B: 'Empezar prueba Pro' — directo a Stripe con 14 días gratis
- Si elige crear cuenta: email de confirmación → click → acceso al plan gratuito
- Primera vez que entra logueado: tour guiado de 3 pasos (tooltip overlay)
- Tooltip 1: 'Aquí van tus datos de empresa — solo se rellenan una vez'
- Tooltip 2: 'El asistente IA te ayuda a redactar los conceptos'
- Tooltip 3: 'Pulsa Generar PDF cuando esté listo'

### 9.2 Email de bienvenida — listo para usar

> Asunto: ¡Bienvenido a PresupuestoRápido, {nombre}! 🎉
> Hola {nombre},
> 
> Acabas de unirte a la herramienta de presupuestos más sencilla de España.
> 
> En 30 segundos puedes generar tu primer presupuesto profesional:
> 👉 Ve a presupuestorapido.pro
> 👉 Rellena los datos de tu cliente y el trabajo
> 👉 Pulsa 'Generar PDF'
> 👉 Mándalo por WhatsApp
> 
> Si tienes cualquier duda, responde a este email y te ayudo personalmente.
> 
> Un saludo,
> Leandro — fundador de PresupuestoRápido
> 
> P.D. Tienes 14 días del plan Pro gratis activados. Aprovéchalo.

## 10. FAQ — Preguntas Frecuentes

| Pregunta | Respuesta |
| --- | --- |
| ¿Necesito instalar algo? | No. Funciona directamente en el navegador, desde el móvil o el ordenador. No hay app que descargar. |
| ¿Mis datos están seguros? | Los datos del presupuesto se generan en tu propio navegador y no se guardan en ningún servidor hasta que crees una cuenta. |
| ¿Puedo usarlo desde el móvil? | Sí, la web está optimizada para móvil. Ideal para hacer presupuestos desde la obra. |
| ¿Cómo funciona el plan gratuito? | Puedes generar hasta 3 presupuestos al mes sin pagar nada. A partir del 4º necesitas el plan Pro. |
| ¿Qué incluye el plan Pro? | Presupuestos ilimitados, PDF sin marca de agua, asistente IA ilimitado, historial de presupuestos y soporte por WhatsApp. |
| ¿Puedo cancelar cuando quiera? | Sí. Sin permanencia, sin penalizaciones. Cancelas en 1 click desde tu panel y no se renueva. |
| ¿Funciona para cualquier oficio? | Sí — fontaneros, electricistas, pintores, carpinteros, reformistas, jardineros, técnicos de aire acondicionado... |
| ¿Cómo calcula el IVA? | Puedes elegir entre 21% (general), 10% (reducido para obras de rehabilitación) y 0% (si eres autónomo en régimen simplificado). |
| ¿Puedo añadir mi logo? | En el plan Pro puedes subir tu logo y aparece en el PDF automáticamente. |
| ¿Se guarda el historial? | Con cuenta registrada, todos los presupuestos se guardan en tu historial y puedes buscarlos, duplicarlos y ver su estado. |
| ¿Qué pasa si el cliente no acepta? | Puedes marcar el presupuesto como 'rechazado' y el sistema te recordará hacer seguimiento. |
| ¿Puedo enviar por email además de WhatsApp? | Por ahora solo por WhatsApp. La opción de email está en el roadmap para la v1.5. |
| ¿El PDF tiene marca de agua en el plan gratuito? | Sí, aparece 'Generado con PresupuestoRápido' en el pie del documento. Con el plan Pro desaparece. |
| ¿Es legal el presupuesto generado? | El presupuesto es un documento válido que incluye todos los datos necesarios. No tiene validez de factura — para eso necesitas un software de facturación. |
| ¿Hay descuento para autónomos con pocos ingresos? | Escríbenos a presupuestorapido.pro — valoramos cada caso individualmente. |
| ¿Puedo tener varios usuarios con el mismo plan? | El plan Empresa (19€/mes) permite hasta 5 usuarios. El plan Pro es para 1 usuario. |
| ¿Cómo funciona la prueba gratuita de 14 días? | Al activar el plan Pro tienes 14 días gratis. No se cobra nada hasta que termine la prueba. Puedes cancelar antes sin coste. |
| ¿Dónde veo mis estadísticas? | En tu panel de cuenta puedes ver cuántos presupuestos has enviado, cuántos han sido aceptados y el importe total presupuestado. |
| ¿Puedo duplicar un presupuesto anterior? | Sí. Desde el historial puedes duplicar cualquier presupuesto y modificarlo para el nuevo cliente. |
| ¿El asistente IA entiende el argot de los oficios? | Sí. Está entrenado específicamente para fontanería, electricidad, pintura y construcción en España, con precios de mercado actualizados. |

## 11. Roadmap Día a Día — Primeros 90 Días

### Semana 1 — Infraestructura y primer usuario

| Día | Área | Tarea | Resultado esperado |
| --- | --- | --- | --- |
| Día 1 | Dev | Conectar Supabase MCP a Claude Code | Supabase operativo y accesible desde terminal |
| Día 2 | Dev | Crear schema SQL completo en Supabase | Todas las tablas, RLS y triggers activos |
| Día 3 | Dev | Implementar modal de registro/login en index.html | Usuarios pueden registrarse y loguearse |
| Día 4 | Dev | Crear función api/auth.js | Backend de autenticación funcionando |
| Día 5 | Dev | Limitar plan gratuito a 3 presupuestos/mes | Contador activo, modal de upgrade aparece |
| Día 6 | Dev | Añadir marca de agua en PDFs del plan gratuito | PDFs gratuitos con watermark |
| Día 7 | Ventas | Publicar en 5 grupos de Facebook de oficios | Primeros visitantes reales a la web |

### Semana 2 — Pagos reales y primeros clientes

| Día | Área | Tarea | Resultado esperado |
| --- | --- | --- | --- |
| Día 8 | Dev | Crear función api/stripe-hook.js | Webhook de Stripe recibiendo eventos |
| Día 9 | Dev | Conectar pago con plan Pro en Supabase | Al pagar se activa plan Pro automáticamente |
| Día 10 | Dev | Implementar portal de cliente Stripe | Usuarios pueden cancelar/gestionar su plan |
| Día 11 | Ventas | Grabar y publicar vídeo TikTok/Reel | Primer vídeo de demo online |
| Día 12 | Dev | Crear panel básico 'Mi cuenta' | Usuario ve su plan, uso y puede modificar datos |
| Día 13 | Dev | Historial de presupuestos guardados | Lista de presupuestos del usuario en BD |
| Día 14 | Ventas | Responder comentarios y mensajes de redes | Primeros leads cualificados contactados |

### Semana 3-4 — SEO y producto

| Día | Área | Tarea | Resultado esperado |
| --- | --- | --- | --- |
| Día 15 | Dev | Añadir meta tags SEO completos en index.html | Google puede indexar correctamente la web |
| Día 16 | Dev | Crear sitemap.xml y robots.txt | Archivos SEO técnico listos |
| Día 17 | Mktg | Registrar en Google Search Console | Web enviada a indexar en Google |
| Día 18 | Dev | Opción de duplicar presupuesto anterior | Usuarios ahorran más tiempo |
| Día 19 | Dev | Numeración automática de presupuestos | Secuencia 2024-001, 2024-002... |
| Día 20 | Mktg | Publicar artículo blog: 'Cómo hacer presupuesto fontanero' | Primer contenido SEO publicado |
| Día 21 | Ventas | Email a primeros registrados: ¿qué opinas? | Feedback cualitativo de primeros usuarios |

### Mes 2 — Crecimiento y optimización

| Semana | Área | Tarea | Resultado esperado |
| --- | --- | --- | --- |
| Semana 5 | Dev | Subida de logo en el PDF del plan Pro | Diferenciador del plan Pro completado |
| Semana 5 | Mktg | 2 vídeos más en TikTok/Reels | Crecimiento de seguidores y tráfico |
| Semana 6 | Dev | Configurar Brevo — secuencia de 5 emails | Automatización de email marketing activa |
| Semana 6 | Mktg | Contactar 3 asociaciones de autónomos | Pipeline B2B iniciado |
| Semana 7 | Dev | Página de Política de Privacidad y Términos | Cumplimiento legal completado |
| Semana 7 | Mktg | Publicar 2 artículos SEO más | 3 artículos de blog activos |
| Semana 8 | Dev | Estadísticas básicas en panel de usuario | Usuarios ven presupuestos enviados/aceptados |
| Semana 8 | Ventas | Llamar a los 10 primeros clientes de pago | NPS y feedback directo recogido |

### Mes 3 — Escala y nuevas funcionalidades

| Semana | Área | Tarea | Resultado esperado |
| --- | --- | --- | --- |
| Semana 9 | Dev | PWA — instalable desde móvil | App instalable sin app store |
| Semana 10 | Dev | Enlace de seguimiento de presupuesto | Saber si el cliente abrió el PDF |
| Semana 11 | Dev | Notificaciones email cuando cliente acepta | Automatización de cierre de ventas |
| Semana 12 | Ventas | Hito: 50 clientes Pro activos = 350€ MRR | Validación completa del modelo |

## 12. Análisis de Riesgos

| Riesgo | Prob. | Impacto | Mitigación |
| --- | --- | --- | --- |
| Nadie paga | Alta | Alto | Implementar límites reales del plan gratuito YA. Sin límites, nadie tiene razón para pagar. |
| Competidor grande entra al mercado | Baja | Alto | Holded o Billin podrían añadir esta función. Ventaja: somos más baratos y simples. Foco en nicho específico. |
| API de Anthropic sube precios | Media | Medio | Usar el modelo más barato (haiku). El coste por usuario es ~0,10€/mes — incluso triplicado sigue siendo rentable. |
| Vercel cambia condiciones gratuitas | Baja | Medio | Migración a Cloudflare Pages o Railway en <1 día. El código es portable. |
| Supabase tiene caída | Baja | Alto | Supabase tiene 99,9% uptime SLA. Backup semanal de la BD. Plan de migración a PlanetScale si falla. |
| Churn alto (>10%/mes) | Media | Alto | Mejorar onboarding, email de retención, descuento para usuarios que cancelan. Objetivo <5%. |
| No hay tráfico orgánico | Media | Medio | Compensar con Facebook y TikTok. El SEO tarda 3-6 meses. No depender solo de SEO al inicio. |
| Problemas legales RGPD | Baja | Alto | Publicar Política de Privacidad completa. No guardar datos sin consentimiento. Usar Supabase EU region. |

## 13. Versiones del Producto

| Versión | Fecha objetivo | Estado | Funcionalidades |
| --- | --- | --- | --- |
| v1.0 | Abril 2026 | ✅ ACTUAL | Web publicada + IA + PDF + WhatsApp + Stripe + Dominio propio |
| v1.1 | Mayo 2026 | ⏳ En desarrollo | Login/registro + BD Supabase + límites plan gratuito + webhooks Stripe |
| v1.2 | Junio 2026 | ⏳ Planificada | Panel de usuario + historial + logo en PDF + numeración automática |
| v1.5 | Julio 2026 | ⏳ Planificada | SEO completo + blog + PWA móvil + seguimiento de presupuesto |
| v2.0 | Sep 2026 | ⏳ Futura | Firma digital + notificaciones email + múltiples usuarios (plan Empresa) |
| v2.5 | Nov 2026 | ⏳ Futura | Integración con software contabilidad + exportación Excel + API pública |
| v3.0 | 2027 | ⏳ Visión | Expansión Portugal + Latinoamérica + marketplace de plantillas |

## 14. Glosario

| Término | Definición |
| --- | --- |
| MRR | Monthly Recurring Revenue — ingresos recurrentes mensuales. 100 clientes × 7€ = 700€ MRR |
| Churn | Tasa de cancelación mensual. Si 5 de 100 clientes cancelan = 5% churn |
| LTV | Lifetime Value — ingresos totales de un cliente. 7€/mes × 12 meses = 84€ LTV |
| CAC | Customer Acquisition Cost — coste de conseguir un cliente. Objetivo < 5€ |
| Freemium | Modelo con versión gratuita limitada y versión de pago con más funciones |
| MCP | Model Context Protocol — protocolo que permite a Claude Code conectarse a servicios externos |
| Webhook | Notificación automática que Stripe envía a tu servidor cuando ocurre un pago |
| JWT | JSON Web Token — credencial cifrada que identifica a un usuario autenticado |
| RLS | Row Level Security — seguridad en Supabase que impide que un usuario vea datos de otro |
| PWA | Progressive Web App — web que se puede instalar como app en el móvil |
| ICP | Ideal Customer Profile — descripción del cliente ideal del producto |
| Serverless | Funciones de backend que se ejecutan bajo demanda sin servidor dedicado |
| CI/CD | Continuous Integration/Continuous Deployment — deploy automático en cada commit |
| SEO | Search Engine Optimization — optimización para aparecer en Google |
| CTA | Call to Action — botón o enlace que invita al usuario a hacer algo |
| NPS | Net Promoter Score — métrica de satisfacción del cliente (0-10) |

> Resumen de próximos pasos inmediatos para Leandro
> 1. Instalar Supabase MCP en Claude Code y crear el schema SQL
> 2. Implementar login/registro con Supabase Auth
> 3. Activar límites del plan gratuito (contador de 3 presupuestos/mes)
> 4. Conectar Stripe webhooks para activar plan Pro automáticamente
> 5. Publicar en grupos de Facebook de autónomos — esta semana
> 6. Grabar TikTok de demo — esta semana
> Todo lo demás es secundario hasta tener los primeros 10 clientes de pago.
