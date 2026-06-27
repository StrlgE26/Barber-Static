# Barber Cerdas — Demo estática

Versión **100% estática** del sistema de gestión de citas de **Academia De Barberia The Hipster** (Lindavista, CDMX). Sin backend, sin base de datos, sin red: todo corre en el navegador con datos simulados.

Pensada como **showcase visual y prueba de UX** del producto real. La versión productiva (con Supabase, Auth, emails reales, RLS, etc.) vive en otro repo — esta es para mostrar la interfaz sin necesidad de provisionar infra.

**Demo en vivo:** `https://StrlgE26.github.io/Barber-Static/`

---

## 1. Stack tecnológico

| Capa | Tecnología | Notas |
|---|---|---|
| Frontend | HTML5 + CSS3 + JavaScript Vanilla | Cero frameworks, cero bundlers, cero dependencias |
| "Backend" | `mock-data.js` en cliente | Catálogos hardcodeados + generador de slots |
| Persistencia | `localStorage` | Sesiones, citas creadas durante la demo, estado de UI |
| Deploy | GitHub Pages | Branch `main` / raíz |
| Hosting | Estático puro | No requiere ningún runtime |

**Cero dependencias npm.** No hay `package.json`, no hay `node_modules`, no hay build step. Abres el HTML y funciona.

---

## 2. Qué hace y qué NO hace

### Sí hace
- Landing pública con catálogo de servicios, equipo y ubicación.
- Wizard de reserva en 3 pasos (sucursal/servicios → barbero/horario → datos).
- Login simulado para cliente y barbero (credenciales abajo).
- Dashboard del barbero con citas del día.
- Panel kiosko para walk-ins.
- Pantalla `/mi-cuenta` con próximas citas e historial del cliente.
- Confirmación de cita vía URL con token simulado.

### NO hace
- No envía emails reales — solo simula el flujo.
- No persiste entre dispositivos (cada navegador tiene su propio `localStorage`).
- No valida disponibilidad real — los slots se generan localmente.
- No tiene RLS, ni auth real, ni triggers, ni cron jobs.
- No multi-sucursal — está limitado a una sucursal demo (`The Hipster Lindavista`).

> Si necesitas la versión con backend real, consulta el repo de producción.

---

## 3. Estructura del repositorio

```
Barber-Static/
├── README.md                  # Este documento
├── index.html                 # Landing pública (wizard de reserva, auth, navegación)
├── 404.html                   # Página de error con redirect al inicio
│
├── dashboard/
│   └── index.html             # Panel del barbero (login simulado + agenda del día)
├── mi-cuenta/
│   └── index.html             # Dashboard del cliente (próximas, historial, perfil)
├── kiosko/
│   └── index.html             # Tablet en mostrador para walk-ins
├── confirmar/
│   └── index.html             # Confirma la cita desde el "link del email" simulado
│
├── css/
│   ├── styles.css             # Estilos globales (paleta dorado/negro, Playfair + Barlow)
│   ├── dashboard.css          # Específicos del dashboard
│   ├── kiosko.css             # Específicos del kiosko
│   └── mi-cuenta.css          # Específicos de mi-cuenta
│
└── js/
    ├── mock-data.js           # Catálogos, credenciales demo, generador de slots, helpers
    ├── script.js              # Lógica de la landing (wizard, auth modal, agenda)
    ├── dashboard.js           # Dashboard del barbero
    ├── kiosko.js              # Auto-registro walk-in
    └── mi-cuenta.js           # Dashboard del cliente
```

URLs limpias gracias a que cada sección vive en su propia carpeta con `index.html` — GitHub Pages las sirve como `/dashboard/`, `/mi-cuenta/`, etc.

---

## 4. Credenciales demo

| Rol | Email | Contraseña |
|---|---|---|
| Cliente | `cliente@barberia.com` | `cliente123` |
| Barbero | `barbero@barberia.com` | `barbero123` |
| Admin de sucursal | `sucursal@barberia.com` | `admin123` |
| Admin general | `admin@barberia.com` | `admin123` |

Las credenciales viven en [js/mock-data.js](js/mock-data.js) y se validan contra el array `CREDENTIALS`. No hay hashing, no hay JWT — es una demo.

---

## 5. Cómo correr local

No necesitas Node, ni Vercel CLI, ni nada. Solo un servidor estático.

```bash
# Opción 1: Python
python -m http.server 8000

# Opción 2: VS Code → extensión "Live Server"
# Opción 3: cualquier extensión de IDE que sirva archivos estáticos
```

Luego abre `http://localhost:8000/`.

> No abras los HTML con doble click (`file://`) — algunos navegadores bloquean `fetch`/módulos en ese esquema. Usa siempre un servidor.

---

## 6. Deploy en GitHub Pages

1. Push del repo a GitHub.
2. **Settings → Pages → Source: Deploy from a branch → Branch: `main` / `(root)`**.
3. Espera ~1 min y el sitio queda en `https://<tu-usuario>.github.io/Barber-Static/`.

### URLs resultantes

| Sección | URL |
|---|---|
| Landing | `/Barber-Static/` |
| Panel barbero | `/Barber-Static/dashboard/` |
| Cuenta cliente | `/Barber-Static/mi-cuenta/` |
| Kiosko walk-in | `/Barber-Static/kiosko/` |
| Confirmación cita | `/Barber-Static/confirmar/` |

> Si renombras el repo, actualiza el path hardcodeado en [404.html:7](404.html:7) (`var repo = '/Barber-Static/'`).

---

## 7. Datos simulados

Todo lo "dinámico" vive en [js/mock-data.js](js/mock-data.js) bajo el namespace `DEMO`:

| Constante | Qué contiene |
|---|---|
| `SUCURSAL` | Datos de la sucursal única (nombre, dirección, teléfono, horario) |
| `SERVICIOS` | Catálogo de 6 servicios (corte, degradado, barba, combo, tratamiento, diseño) |
| `EMPLEADOS_BASE` | 4 barberos + admin sucursal + admin general |
| `CREDENTIALS` | Credenciales de login para los 4 roles demo |
| `CLIENTE_DEMO` | Cliente preregistrado con historial simulado |

### Helpers expuestos

| Función | Qué hace |
|---|---|
| `DEMO.authenticate(email, pwd)` | Valida credenciales contra `CREDENTIALS` |
| `DEMO.generarSlots(barbero, fecha, duracion)` | Genera horarios disponibles del día (10:00–20:00, intervalos de 30 min) |
| `DEMO.agendarCita({...})` | Guarda una cita nueva en `localStorage` y devuelve un token simulado |
| `DEMO.getHoy()` | Devuelve la fecha de hoy en formato `YYYY-MM-DD` |
| `DEMO.fmtHora(iso)` | Formatea un ISO datetime a hora local |
| `DEMO.getSrv(id)` / `DEMO.getEmp(id)` | Lookups por id |

Las citas que crees durante la demo se guardan en `localStorage` bajo `sb_citas_demo`. Borrar el storage del navegador resetea todo.

---

## 8. Diferencias con la versión productiva

| Aspecto | Productiva | Esta demo |
|---|---|---|
| Backend | Supabase (Postgres + Auth + REST) | Ninguno |
| Autenticación | Supabase Auth con JWT | Validación de array en cliente |
| Persistencia | Postgres con RLS | `localStorage` por navegador |
| Slots | RPC `obtener_slots_disponibles` con anti-traslape | `DEMO.generarSlots` (todo libre por defecto) |
| Confirmación email | Resend + serverless Vercel | Toast simulado |
| Anti-spam teléfono | Trigger + tabla `bloqueo_telefono` | No implementado |
| Realtime dashboard | Supabase Realtime (WebSocket) | Lectura puntual de `localStorage` |
| Roles y permisos | RLS + policies por rol | Switch en cliente |
| Multi-sucursal | Sí | No (una sola sucursal demo) |
| Costo de hosting | Vercel + Supabase | Gratis en GH Pages |

---

## 9. Para qué sirve esta demo

- Mostrar la UI a stakeholders sin tener que provisionar Supabase.
- Pruebas de UX y flujo sin contaminar datos reales.
- Demo offline en presentaciones (un servidor local basta).
- Base para forks o variantes que necesiten una landing parecida.

Si vas a llevarla a producción, **migra a la versión con Supabase** — esta no tiene seguridad real, ni persistencia, ni capacidad de manejar usuarios concurrentes.
