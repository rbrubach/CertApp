# ARQUITECTURA.md — Visión Técnica de CertTrack IT

**Propósito:** Documentar arquitectura técnica, componentes, y flujos de datos  
**Audience:** Developers, Tech Leads, Architects  
**Última actualización:** 2026-05-04

---

## 📋 Tabla de Contenidos

1. [Overview Arquitectura](#overview-arquitectura)
2. [Componentes Principales](#componentes-principales)
3. [Flujo de Datos](#flujo-de-datos)
4. [Seguridad & Permisos](#seguridad--permisos)
5. [Rendimiento & Escalabilidad](#rendimiento--escalabilidad)
6. [Decisiones Arquitectónicas](#decisiones-arquitectónicas)
7. [Roadmap Técnico](#roadmap-técnico)

---

## Overview Arquitectura

### Diagrama High-Level

```
┌─────────────────────────────────────────────────┐
│                                                 │
│            NAVEGADOR (Cliente)                  │
│                                                 │
│   ┌──────────────────────────────────────────┐ │
│   │  certtrack-it-full.html (120 KB)         │ │
│   │  ├─ CSS (estilos, theming)               │ │
│   │  ├─ HTML (estructura)                    │ │
│   │  └─ JavaScript (lógica, CRUD)            │ │
│   └──────────────────────────────────────────┘ │
│          │                                     │
│          ├─ Demo Mode: Datos en memoria       │
│          └─ Live Mode: REST calls a SP        │
│                                                 │
└─────────────────┬───────────────────────────────┘
                  │
                  │ REST API Calls (si live mode)
                  ▼
┌─────────────────────────────────────────────────┐
│                                                 │
│      SharePoint Online (Almacenamiento)        │
│                                                 │
│  ┌─────────────────────────────────────────┐  │
│  │ Listas:                                 │  │
│  │  - Certificaciones                      │  │
│  │  - Colaboradores                        │  │
│  │  - MaestroCertificaciones               │  │
│  │  - MaestroEntidades                     │  │
│  │  - Roadmap                              │  │
│  └─────────────────────────────────────────┘  │
│                                                 │
│  ┌─────────────────────────────────────────┐  │
│  │ Autenticación:                          │  │
│  │  - Azure AD (usuarios)                  │  │
│  │  - Grupos: CertTrack_Admins,           │  │
│  │           CertTrack_Empleados           │  │
│  └─────────────────────────────────────────┘  │
│                                                 │
└─────────────────────────────────────────────────┘
```

### Características

```
✅ Single-page application (SPA) — No refresh
✅ Client-side rendering — Todo en el navegador
✅ Responsive design — Desktop, tablet, móvil
✅ Offline-capable (demo) — Funciona sin internet
✅ Real-time sync (live) — REST API calls a SP
✅ Role-based access — Admin vs Empleado
```

---

## Componentes Principales

### 1. Frontend (HTML/CSS/JS)

**Ubicación:** `certtrack-it-full.html`

**Estructura:**

```
<head>
  <style>
    /* CSS Variables para theming */
    :root {
      --bg, --ink, --accent, etc.
    }
    
    /* Estilos por componente */
    header { ... }
    .sidebar { ... }
    .card { ... }
    /* etc. */
  </style>
</head>

<body>
  <!-- Estructura estática -->
  <header>Logo, user chip, role pill</header>
  <div class="shell">
    <aside class="sidebar">Navegación</aside>
    <main class="main">Páginas dinámicas</main>
  </div>
  
  <!-- Modales, tooltips -->
  <div id="modalNewCert">...</div>
  
  <script>
    /* JavaScript application logic */
  </script>
</body>
```

**Líneas de código:**
- CSS: ~400 líneas
- HTML: ~100 líneas
- JavaScript: ~1400 líneas

**Total: ~1900 líneas, 120 KB minificado**

---

### 2. JavaScript Architecture

#### Global State (CFG)

```javascript
const CFG = {
  mode: 'demo' | 'live',
  user: { email, name, id },
  role: 'admin' | 'empleado',
  
  // SharePoint context (live mode)
  spSite: 'https://tenant.sharepoint.com/sites/certtrack',
  spUser: 'email@corp',
  spRole: 'admin' | 'empleado'
};
```

#### Master Data (Demo Mode)

```javascript
const COLABORADORES = [...]    // Empleados
const MASTER_CERTS = [...]     // Catálogo certificaciones
const MASTER_ENTITIES = [...]   // Entidades certificadores
const CERTS = [...]             // Instancias de certs
const ROADMAP = [...]           // Certificaciones futuras
const BUDGET_CFG = [...]        // Config presupuesto
```

#### Rendering Functions

```javascript
function renderHome() { ... }
function renderMisCerts() { ... }
function renderAlerts() { ... }
function renderRoadmap() { ... }
function renderAllTable() { ... }
function renderEmpleados() { ... }
function renderSkills() { ... }
function renderBudget() { ... }
function renderReporte() { ... }
function renderMaestros() { ... }
```

**Patrón:**
1. Obtener datos (array o REST call)
2. Filtrar según rol
3. Transformar a HTML string
4. Inyectar via `innerHTML`

#### Data Mutation Functions

```javascript
function saveCert() { ... }      // Create/Update
function delCert() { ... }       // Delete
function saveColab() { ... }     // Save empleado
function saveRoadmap() { ... }   // Save roadmap
// etc.
```

**En demo:** Actualiza arrays en memoria  
**En live:** Llamadas REST a SharePoint

#### Utility Functions

```javascript
days(dateStr)        // Días hasta vencimiento
calcSt(dateStr)      // Estado (Vigente/Por Vencer/Vencida)
fd(dateStr)          // Formato fecha (yyyy-MM-dd → dd/MM/yyyy)
toast(msg, type)     // Notificación
vis(arr)             // Filtro por rol (admin ve todos, empleado solo suyos)
goPage(id)           // Navegación + role gating
```

---

### 3. SharePoint Integration (Live Mode)

**REST Endpoints Utilizados:**

```javascript
GET /api/web/lists/getByTitle('Certificaciones')/items
POST /api/web/lists/getByTitle('Certificaciones')/items
PATCH /api/web/lists/getByTitle('Certificaciones')/items(ID)
DELETE /api/web/lists/getByTitle('Certificaciones')/items(ID)

// Similar para otras listas:
// - Colaboradores
// - MaestroCertificaciones
// - MaestroEntidades
// - Roadmap
```

**Headers Requeridos:**

```javascript
{
  'Accept': 'application/json',
  'Content-Type': 'application/json',
  'X-RequestDigest': 'token_from_SP' // CSRF protection
}
```

**Autenticación:**
- Automática vía `_spPageContextInfo`
- Usuario logueado en SharePoint = autenticado en app

---

## Flujo de Datos

### Demo Mode

```
Usuario hace acción (ej: crear cert)
   ↓
JavaScript event listener captura
   ↓
Validar entrada (no vacíos, fechas válidas)
   ↓
Crear objeto nuevo (id, timestamp, etc.)
   ↓
Agregar a array en memoria (CERTS.push(...))
   ↓
Recalcular estadísticas globales
   ↓
Llamar renderFunction correspondiente
   ↓
innerHTML actualiza DOM
   ↓
Toast notificación "ok"
   ↓
FIN — Datos en memoria (se pierden en refresh)
```

### Live Mode

```
Usuario hace acción (ej: crear cert)
   ↓
JavaScript event listener captura
   ↓
Validar entrada
   ↓
Crear objeto con payload REST
   ↓
XMLHttpRequest POST a SharePoint
   ↓
¿Éxito (HTTP 201)?
   ├─ Sí → Item creado en SP
   │       Retornar ID desde SP
   │       Actualizar array local con ID
   │       Renderizar
   │       Toast "ok"
   │
   └─ No → Error HTTP
           Toast "error"
           Log error en console
```

### Filtro por Rol (Visibility)

```javascript
function vis(arr) {
  if (CFG.role === 'admin') return arr;        // Admin ve todo
  if (CFG.role === 'empleado') {
    return arr.filter(item => item.colabId === CFG.user.id);
  }
}
```

**Aplicado en:**
- Certificaciones (employee solo ve propios)
- Roadmaps (solo propios)
- Algunas páginas no aparecen en sidebar

---

## Seguridad & Permisos

### Autenticación

**Demo Mode:**
```
No hay autenticación real
Simula cambio de rol en UI
CFG.role se cambia manualmente
```

**Live Mode:**
```
SharePoint online authentication
_spPageContextInfo provee:
  - User email
  - User name
  - Site URL
  
Se detecta rol de grupo Azure AD:
  - CertTrack_Admins → CFG.role = 'admin'
  - CertTrack_Empleados → CFG.role = 'empleado'
```

### Autorización

**Frontend Gating:**

```javascript
function goPage(pageId) {
  // Bloquear si no eres admin y es admin page
  const adminPages = ['empleados', 'roadmap-admin', 'reportes', ...];
  
  if (adminPages.includes(pageId) && CFG.role !== 'admin') {
    toast('No tienes acceso a esta página', 'err');
    return;
  }
  
  // Mostrar página
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.getElementById(pageId).classList.add('active');
  render[pageId](); // Render específico
}
```

**Backend Gating (Live Mode):**

```javascript
// REST calls incluyen automáticamente usuario context
// SharePoint verifica:
  1. ¿Está en grupo de acceso al sitio?
  2. ¿Tiene permisos sobre el item? (item-level security)
  3. Si no → HTTP 403 Forbidden
```

### Data Filtering

```javascript
// Empleados solo ven propios items
vis(CERTS).filter(c => c.colabId === CFG.user.id)

// Admins ven todos
vis(CERTS)  // Sin filtro, retorna todos
```

---

## Rendimiento & Escalabilidad

### Baseline Performance (Demo Mode)

```
Métrica              Target      Actual
─────────────────────────────────────────
Initial Load         < 2s        ~800ms
Navigate Page        < 500ms     ~200ms
Tabla 5 items        < 100ms     ~50ms
Tabla 100 items      < 500ms     ~300ms
Tabla 1000 items     < 2s        ~800ms
```

### Bottlenecks Conocidos

```
1. Tabla muy grande (500+ items)
   - Problema: Render todo el HTML
   - Síntoma: Lag al scroll
   - Solución futura: Virtualización / paginación

2. Muchos eventos listeners
   - Problema: Cada botón es listener individual
   - Síntoma: Memory leak si no limpiar
   - Solución: Event delegation

3. Google Fonts CDN
   - Problema: Si CDN lento, bloquea render
   - Síntoma: FOUC (Flash of Unstyled Content)
   - Solución: Descargar fonts localmente
```

### Escalabilidad

**Capacidad Actual:**

```
Demo Mode:
  - Soporta hasta 1000+ items sin problema
  - Limitado solo por memoria navegador
  - En máquinas modernas: 0 problema

Live Mode:
  - Limitado por:
    1. SharePoint list performance (típicamente < 5000 items ok)
    2. REST API latency (típicamente < 500ms por call)
    3. User quota (batch requests)
```

**Si escala mucho (>10000 items):**

```
v2.0 opciones:
  ☐ Paginación (mostrar 50 items/página)
  ☐ Virtualización (solo render visible)
  ☐ Search indexing
  ☐ Server-side filtering
```

---

## Decisiones Arquitectónicas

### ✅ Decisión 1: Single HTML File

**Alternativas consideradas:**
- Multi-file HTML + JS (modularizado)
- React SPA (con build step)
- SPFx component (.sppkg)

**Elegida:** Single HTML file

**Razones:**
```
✓ Cero build process — deploy inmediato
✓ Zero dependencies — no npm install
✓ Fácil versioning — 1 archivo = 1 versión
✓ Offline capable — incluye todo
✓ Fácil para SharePoint — 1 upload = done

✗ Tradeoff: Difícil modularizar si crece
```

**Evolución futura:**
```
Si código crece > 2000 líneas:
  → Considerar split a módulos JS
  → Build step simple (concatenación)
  
Si necesita distribución en múltiples tenants:
  → Considerar migración a SPFx (v3.0)
```

---

### ✅ Decisión 2: Client-Side Rendering

**Por qué no Server-Side?**

```
Server-side (MVC, Node.js):
  ✓ SEO friendly
  ✓ Lighter client code
  ✗ Requiere servidor
  ✗ Deployment más complejo
  ✗ Higher latency (roundtrip)

Client-side (SPA):
  ✓ Funciona offline
  ✓ Deployment simple
  ✓ Muy rápido (no roundtrips)
  ✗ JS más pesado
  ✗ SEO no aplica (interno corporate)
```

**Resultado:** Client-side es mejor para este case.

---

### ✅ Decisión 3: Arrays en Memoria vs DB

**Demo Mode = Arrays:**

```
const CERTS = [
  { id: 1, ... },
  { id: 2, ... },
  ...
]
```

**Live Mode = SharePoint:**

```
Data persiste en SharePoint
No necesita IndexedDB o localStorage
```

**Tradeoff:**

```
Demo mode:
  ✓ Simple, rápido, no setup
  ✗ Se pierden datos en refresh
  
Live mode:
  ✓ Persistencia
  ✗ REST call latency
  ✗ Requiere setup SharePoint
```

---

## Roadmap Técnico

### v1.0 (Actual)

**Características:**
- ✅ Single HTML
- ✅ Demo + Live mode
- ✅ CRUD básico
- ✅ Role-based access
- ✅ Responsive design

**Limitaciones conocidas:**
- ❌ No hay búsqueda avanzada
- ❌ No hay offline sync
- ❌ Datos no se cachean localmente

---

### v1.1 (Q3 2026)

**Mejoras planeadas:**

```javascript
[ ] Google Fonts descarga local
    // Si red corporativa bloquea CDN
    
[ ] Búsqueda en tabla
    // Encontrar certs por nombre
    
[ ] Dark mode toggle
    // Usuario puede cambiar tema
    
[ ] Exportar PDF
    // Además de CSV
    
[ ] Webhooks para notificaciones
    // En tiempo real vs. batch diario
```

---

### v2.0 (Q4 2026 - Si es necesario)

**Modularización:**

```
Si código crece > 2000 líneas:

Opción A: Modularización simple
  src/
  ├── app.js (inicialización)
  ├── state.js (CFG, datos globales)
  ├── render.js (todas las render functions)
  ├── crud.js (save, delete)
  └── utils.js (helpers)
  
  build: Concatenar en 1 archivo

Opción B: Module bundler (webpack, vite)
  - Más control
  - Mejor tree-shaking
  - Posibilidad de lazy-loading
```

---

### v3.0 (2027 - Si escala a múltiples tenants)

**SPFx Component:**

```
Cuando:
  - Necesita distribución en múltiples tenants
  - Usuarios quieren app store experience
  - Quiere actualizaciones automáticas

Cambios:
  - Refactorizar a React
  - Usar SPFx framework
  - Generar .sppkg
  - Distribuir vía App Catalog

Ventajas:
  ✓ Distribución escalable
  ✓ Updates automáticas
  ✓ Mejor integración con Modern SP
  ✓ Soporte oficial de MS

Desventajas:
  ✗ Más complejidad
  ✗ Requiere build process
  ✗ Node.js + npm
```

---

## Patrones de Código

### Patrón 1: Rendering

```javascript
function renderMisCerts() {
  const src = vis(CERTS)
    .map(c => ({
      ...c,
      _s: calcSt(c.fechaVencimiento),      // Estado calculado
      _d: days(c.fechaVencimiento)          // Días calculados
    }));
  
  const html = `
    <table>
      ${src.map(c => `
        <tr>
          <td>${c.name}</td>
          <td class="badge-${c._s}">${c._s}</td>
          ...
        </tr>
      `).join('')}
    </table>
  `;
  
  document.getElementById('page-miscerts').innerHTML = html;
  attachEventListeners();
}
```

**Características:**
- Datos transformados (.map)
- HTML strings (template literals)
- innerHTML (replaces content)
- Event delegation (attachEventListeners)

---

### Patrón 2: CRUD

```javascript
function saveCert() {
  const name = document.getElementById('certName').value;
  const vencimiento = document.getElementById('certVencimiento').value;
  
  if (!name || !vencimiento) {
    toast('Campos requeridos', 'err');
    return;
  }
  
  const newCert = {
    id: Date.now(),
    name,
    vencimiento,
    colabId: CFG.user.id
  };
  
  if (CFG.mode === 'demo') {
    CERTS.push(newCert);
    renderMisCerts();
    toast('Certificación creada', 'ok');
  } else {
    // REST POST a SharePoint
    fetch(`${CFG.spSite}/_api/web/lists/...`, {
      method: 'POST',
      headers: { ... },
      body: JSON.stringify(newCert)
    }).then(r => r.json()).then(data => {
      CERTS.push({ ...newCert, id: data.Id });
      renderMisCerts();
      toast('Certificación creada', 'ok');
    }).catch(err => {
      toast('Error: ' + err.message, 'err');
    });
  }
}
```

---

## Testing Implicaciones

**Ver [TEST-CASES.md](../tests/TEST-CASES.md) para casos detallados.**

---

## Conclusión

CertTrack IT es una **SPA simple, autosuficiente, escalable** que:
1. Funciona standalone (demo) o integrada con SP (live)
2. Requiere cero infraestructura adicional
3. Tiene roadmap claro para evolución

**Filosofía:** Simple > Complex  
**Mantra:** Ship fast, iterate often

---

**Última actualización:** 2026-05-04  
**Próxima revisión:** 2026-09-04
