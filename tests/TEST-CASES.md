# TEST-CASES.md — Casos de Prueba Detallados

**Propósito:** Garantizar que la app funciona correctamente en demo y live mode  
**Tiempo estimado:** 2-3 horas por ambiente (demo + live)  
**Quién:** Tester, QA, Admin técnico

---

## 📋 Tabla de Contenidos

1. [Cómo Usar Este Documento](#cómo-usar-este-documento)
2. [Test Cases - Demo Mode](#test-cases---demo-mode)
3. [Test Cases - Live Mode](#test-cases---live-mode)
4. [Test Cases - Responsive & Navegadores](#test-cases---responsive--navegadores)
5. [Reporte de Resultados](#reporte-de-resultados)

---

## Cómo Usar Este Documento

### Preparación

```
1. Leer esta sección completa antes de empezar
2. Preparar ambiente de test (ver TEST-DATA.md)
3. Abrir DevTools (F12) para revisar errores
4. Usar template de reporte al final
5. Documentar cualquier fallo encontrado
```

### Convenciones

```
✅ PASS    = Funciona como se espera
❌ FAIL    = No funciona, hay error
⚠️  WARNING = Funciona pero con comportamiento inesperado
🔄 SKIP    = No se puede testear (prerequisitos no cumplidos)

Ejemplo:
[ ] TC-001-01 ✅ Página home carga sin errores
[ ] TC-001-02 ⚠️  Logo se corta en móvil (design issue, no blocker)
```

---

# TEST-CASES — DEMO MODE

## TC-001: Carga Inicial & Autenticación

### TC-001-01: Página carga correctamente

**Precondición:** Navegador moderno (Chrome, Edge, Firefox)  
**Pasos:**

```
1. Abrir certtrack-it-full.html en navegador
2. Esperar 2-3 segundos a que cargue
3. Revisar console (F12) → No debe haber errores en rojo
```

**Resultado esperado:**
- ✅ Página carga sin lag
- ✅ No hay errores en console
- ✅ Header visible con logo + user chip
- ✅ Sidebar visible con navegación
- ✅ Main content muestra página "Home"
- ✅ Demo badge visible en header

**Verificación:**
```
Console (F12) debe mostrar:
  [CertTrack] Initialized in demo mode
  [CertTrack] User: Demo Admin (role: admin)

O si empleado:
  [CertTrack] User: Demo Empleado (role: empleado)
```

---

### TC-001-02: Cambiar rol (Admin ↔ Empleado)

**Pasos:**

```
1. En header, click en "user chip" (avatar + nombre)
2. Seleccionar "Switch Role" o dropdown
3. Cambiar de Admin a Empleado
4. Esperar 1 segundo
5. Revisar que UI se actualiza
```

**Resultado esperado:**
- ✅ User chip muestra nuevo rol
- ✅ Badge de rol cambia de color (admin=morado, empleado=azul)
- ✅ Sidebar items se actualizan (empleado NO ve admin pages)
- ✅ Contenido de home se actualiza (stats cambian)

**Verificación:**
```
Como Admin:
- Sidebar tiene items: Home, Mis Certs, Alertas, Roadmap, 
                        Todos, Empleados, Skills, Budget, Reportes, Maestros

Como Empleado:
- Sidebar tiene items: Home, Mis Certs, Alertas, Skills
- NO tiene: Roadmap admin, Empleados, Budget, Reportes, Maestros
```

---

## TC-002: Navegación & Páginas

### TC-002-01: Navegar por todas las páginas (Admin)

**Pasos:**

```
1. Click "Home" → Página cambia con animación
2. Click "Mis Certificaciones" → Muestra tabla
3. Click "Alertas" → Muestra alertas
4. Click "Roadmap" → Muestra roadmap
5. Click "Todos los Certs" → Tabla con filtro por area
6. Click "Empleados" → Tabla empleados
7. Click "Skills Matrix" → Grid habilidades
8. Click "Presupuesto" → Gráficos presupuesto
9. Click "Reportes" → Dashboard reportes
10. Click "Maestros" → Catálogos
```

**Resultado esperado:**
- ✅ Cada click navega a la página correcta
- ✅ Animación suave (no parpadea)
- ✅ Item activo en sidebar se resalta
- ✅ No hay errores en console
- ✅ Cada página carga contenido relevante

---

### TC-002-02: Navegar por todas las páginas (Empleado)

**Pasos:**

```
1. Cambiar rol a Empleado
2. Click "Home" → OK
3. Click "Mis Certificaciones" → OK
4. Click "Alertas" → OK
5. Click "Skills Matrix" → OK
6. Intentar click "Roadmap" (admin page) 
   → Debe estar disabled o no aparece
7. Intentar acceder vía URL direct (si conoce URL)
   → Debe redirigir o mostrar error
```

**Resultado esperado:**
- ✅ Las 4 páginas públicas funcionan
- ✅ Admin pages NO son accesibles (no hay botón)
- ✅ Si intenta URL directa, muestra error o redirige
- ✅ No hay forma de "hackear" permisos en demo

---

### TC-002-03: Navegación en móvil (375px)

**Pasos:**

```
1. Abrir DevTools → Responsive Mode (375x812 - iPhone)
2. Navegar entre páginas
3. Sidebar debe colapsar (hamburger menu)
4. Click hamburger → sidebar expande
5. Click item → sidebar colapsa, página cambia
```

**Resultado esperado:**
- ✅ Sidebar oculto en móvil, hamburger visible
- ✅ Click hamburger abre sidebar full-screen
- ✅ Navegación funciona igual que desktop
- ✅ No overflow horizontal
- ✅ Texto legible en pantalla pequeña

---

## TC-003: Home Page & Estadísticas

### TC-003-01: Home Admin - Estadísticas correctas

**Pasos:**

```
1. Cambiar rol a Admin
2. Navegar a Home
3. Revisar 4 cards de estadística:
   - Total Certificaciones
   - Certificaciones Vigentes
   - Por Vencer (próximos 30 días)
   - Vencidas
```

**Resultado esperado:**
```
Total Certificaciones: 5
Vigentes: 2
Por Vencer: 2
Vencidas: 1

Cards tienen colores diferenciados:
- Vigentes: verde
- Por Vencer: naranja
- Vencidas: rojo
```

**Verificación:**
```
Datos demo:
- Cert 1: Google GCP (vigente, 90+ días)
- Cert 2: Azure Solutions Arch (vigente, 60+ días)
- Cert 3: AWS Solutions Arch (por vencer, 25 días)
- Cert 4: Oracle DBA (por vencer, 15 días)
- Cert 5: Scrum Master (vencido, -10 días)
```

---

### TC-003-02: Home Empleado - Ve solo sus stats

**Pasos:**

```
1. Cambiar rol a Empleado
2. Navegar a Home
3. Revisar stats
```

**Resultado esperado:**
```
Como empleado, stats deben mostrar:
- Solo sus certificados (no los de otros empleados)
- Típicamente: 1-2 vigentes, 1 por vencer, 0 vencidos
```

---

## TC-004: Mis Certificaciones - CRUD

### TC-004-01: Leer certificaciones (Admin)

**Pasos:**

```
1. Navegar a "Mis Certificaciones"
2. Revisar que tabla muestra certificados
3. Columnas: Nombre, Entidad, Estado, Vencimiento, Acciones
```

**Resultado esperado:**
- ✅ Tabla carga con 5+ certificados
- ✅ Datos son legibles
- ✅ Estado mostrado con badge (verde/naranja/rojo)
- ✅ Fechas formateadas: dd/MM/yyyy
- ✅ Botones: Ver, Editar, Eliminar

---

### TC-004-02: Crear certificación

**Pasos:**

```
1. Click botón "+ Nueva Certificación"
2. Modal se abre
3. Llenar formulario:
   - Nombre: "Test Cert 001"
   - Certificación: GCP
   - Entidad: Google
   - Asignado a: Empleado demo
   - Fecha Obtención: Hoy
   - Fecha Vencimiento: Hoy + 30 días
4. Click "Guardar"
5. Modal cierra
```

**Resultado esperado:**
- ✅ Modal abre y cierra suavemente
- ✅ Alerta "ok" muestra: "Certificación creada"
- ✅ Certificado aparece en tabla inmediatamente
- ✅ Datos se muestran correctamente

---

### TC-004-03: Editar certificación

**Pasos:**

```
1. Click en un certificado en tabla (o botón "Editar")
2. Modal se abre con datos precargados
3. Cambiar un campo (ej: fecha vencimiento)
4. Click "Guardar"
```

**Resultado esperado:**
- ✅ Modal muestra datos actuales
- ✅ Campo editable, cambio visible
- ✅ Al guardar, alerta "ok"
- ✅ Tabla se actualiza sin refresh
- ✅ El cambio persiste (si refresh, sigue ahí)

---

### TC-004-04: Eliminar certificación

**Pasos:**

```
1. Click botón "Eliminar" en un certificado
2. Confirmación modal: "¿Estás seguro?"
3. Click "Sí, eliminar"
```

**Resultado esperado:**
- ✅ Pide confirmación antes de eliminar
- ✅ Alerta "ok": "Certificación eliminada"
- ✅ Certificado desaparece de tabla
- ✅ Estadísticas se actualizan (total baja)
- ✅ Si refresh, sigue sin estar (en memoria, así es ok)

---

### TC-004-05: Filtros en tabla (Admin)

**Pasos:**

```
1. Navegar a "Todos los Certs"
2. Seleccionar filtro por "Area": Cloud
3. Tabla debe mostrar solo certs de área Cloud
4. Cambiar a "Infraestructura"
5. Tabla se actualiza
```

**Resultado esperado:**
- ✅ Filtro funciona inmediatamente
- ✅ Tabla se actualiza sin delay
- ✅ Cantidad de items baja al filtrar

---

## TC-005: Alertas

### TC-005-01: Ver alertas (Admin)

**Pasos:**

```
1. Navegar a "Alertas"
2. Revisar que muestra alertas generadas
3. Cada alerta debe mostrar:
   - Empleado
   - Certificación
   - Días para vencer
   - Botones: Ver, Resolver
```

**Resultado esperado:**
- ✅ Muestra 2+ alertas de demo
- ✅ Formato legible
- ✅ Alertas ordenadas por urgencia (rojo primero)

---

### TC-005-02: Resolver alerta

**Pasos:**

```
1. Click botón "Resolver" en una alerta
2. Modal pide confirmación
3. Click "Confirmar"
```

**Resultado esperado:**
- ✅ Alerta desaparece de lista
- ✅ Alerta "ok": "Alerta resuelta"

---

## TC-006: Formularios & Validación

### TC-006-01: Validación campos obligatorios

**Pasos:**

```
1. Click "+ Nueva Certificación"
2. Dejar campo vacío (ej: Nombre)
3. Click "Guardar"
```

**Resultado esperado:**
- ✅ Campo se resalta en rojo
- ✅ No permite guardar sin llenar
- ✅ Mensaje: "Campo requerido"

---

### TC-006-02: Validación fechas

**Pasos:**

```
1. Crear certificación
2. Poner Fecha Vencimiento = Hoy - 1 día (pasada)
3. Click "Guardar"
```

**Resultado esperado:**
```
Opción A: Acepta y calcula estado como "Vencida" ✅
Opción B: Rechaza con error "Fecha no puede ser pasada" ⚠️
```

---

## TC-007: Modales & UI Elements

### TC-007-01: Modal abre y cierra

**Pasos:**

```
1. Click botón que abre modal
2. Modal aparece con overlay (fondo oscuro)
3. Click botón "Cancelar" o X
4. Modal cierra, overlay desaparece
```

**Resultado esperado:**
- ✅ Modal abre con animación
- ✅ Se puede cerrar con X o Cancel
- ✅ Overlay evita clicks atrás
- ✅ Transiciones suaves

---

### TC-007-02: Toast notifications

**Pasos:**

```
1. Crear un certificado
2. Esperar notificación en esquina inferior
3. Debe mostrarse 3-4 segundos
4. Luego desaparece automáticamente
```

**Resultado esperado:**
- ✅ Toast muestra con animación
- ✅ Texto legible (color contraste ok)
- ✅ Desaparece automáticamente
- ✅ Ícono correcto (✓ para ok, ⚠️ para warning)

---

## TC-008: Reportes & Exports

### TC-008-01: Exportar a CSV

**Pasos:**

```
1. En tabla "Todos los Certs", click botón "Exportar CSV"
2. Esperar descarga
3. Abrir archivo descargado en Excel
```

**Resultado esperado:**
- ✅ Archivo descarga automáticamente
- ✅ Nombre: "reporte_certificaciones.csv"
- ✅ Abre en Excel correctamente
- ✅ Datos coinciden con tabla (mismo orden, columnas)
- ✅ Caracteres especiales se ven bien (ñ, acentos)

---

## TC-009: Dark Mode & Theming (Si aplica)

### TC-009-01: Cambiar tema

**Pasos:**

```
1. Buscar botón "Theme" o "Settings"
2. Cambiar a "Light Mode" (si está en dark)
3. Página se actualiza
```

**Resultado esperado:**
- ✅ Colores cambian (fondo claro, texto oscuro)
- ✅ Legibilidad mantiene
- ✅ Cambio persiste si refresh

---

## TC-010: Performance & Carga

### TC-010-01: Tiempo de carga inicial

**Pasos:**

```
1. Limpiar caché (Ctrl+Shift+Del)
2. Abrir certtrack-it-full.html
3. Usar DevTools → Performance tab → medir
```

**Resultado esperado:**
```
Primera carga: < 2 segundos
Navegación entre páginas: < 500ms
Esperado: Rápido (es un archivo único, no hay server calls)
```

---

### TC-010-02: Carga con muchos datos

**Pasos:**

```
1. En demo data, aumentar cantidad de certificados a 100+
2. Navegar a tabla
3. Revisar performance
```

**Resultado esperado:**
```
- Tabla carga y scrollea smoothly
- No hay lag visible
- Si futura hay millones de items, considerar paginación
```

---

# TEST-CASES — LIVE MODE

**Prerequisito:** SharePoint setup completado (ver SHAREPOINT-SETUP.md)

## TC-101: Conexión SharePoint

### TC-101-01: Conectar a SharePoint REST API

**Pasos:**

```
1. Cambiar CFG.mode = 'live'
2. Abrir app en navegador dentro de SharePoint
3. DevTools → Network tab
4. Debe haber llamadas REST: 
   /api/web/lists/getByTitle('Certificaciones')/items
```

**Resultado esperado:**
- ✅ No hay errores CORS
- ✅ HTTP 200 en llamadas REST
- ✅ Datos cargan de SharePoint (no de hardcoded)

---

### TC-101-02: Detectar usuario y rol

**Pasos:**

```
1. Abrir en SharePoint como Admin
2. Revisar console: CFG.role debe ser 'admin'
3. Logout, ingresar como Empleado
4. Revisar console: CFG.role debe ser 'empleado'
```

**Resultado esperado:**
- ✅ CFG.role se detecta correctamente
- ✅ Basado en grupo Azure AD

---

## TC-102: CRUD SharePoint

### TC-102-01: Crear certificación en SP

**Pasos:**

```
1. Como admin, crear certificación vía app
2. Click "Guardar"
3. Ir a SharePoint → lista Certificaciones
4. Item debe estar allí
```

**Resultado esperado:**
- ✅ Item aparece en lista SP inmediatamente
- ✅ Todos los campos están llenos
- ✅ ID auto-generado

---

### TC-102-02: Editar certificación en SP

**Pasos:**

```
1. Editar certificación en app
2. Cambiar fecha vencimiento
3. Guardar
4. Ir a SP → lista Certificaciones
5. Revisar que cambio está
```

**Resultado esperado:**
- ✅ Cambio se refleja en SP
- ✅ Timestamp "Modified" actualiza

---

### TC-102-03: Eliminar certificación en SP

**Pasos:**

```
1. Eliminar certificación en app
2. Ir a SP → Recycle bin o Versionado
3. Item debe estar marcado como deleted o estar en papelera
```

**Resultado esperado:**
- ✅ Item se elimina o marca
- ✅ Puede recuperarse desde Recycle Bin (si aplica)

---

## TC-103: Permisos Item-Level

### TC-103-01: Empleado ve solo sus certs

**Pasos:**

```
1. Como Empleado, navegar a "Mis Certs"
2. Debe ver SOLO sus certificados
3. Si hay certs de otro empleado, NO debe aparecer
4. Navegar a "Todos los Certs" (admin page)
   → No debe tener acceso
```

**Resultado esperado:**
- ✅ Filtrado automático por empleado logueado
- ✅ No puede "hackear" para ver certs de otros
- ✅ Si intenta URL directa a admin page, error

---

### TC-103-02: Admin ve todos los certs

**Pasos:**

```
1. Como Admin, navegar a "Todos los Certs"
2. Revisar que ve certificados de TODOS los empleados
```

**Resultado esperado:**
- ✅ Sin filtro, ve todos

---

## TC-104: Alertas Automáticas (Power Automate)

### TC-104-01: Email 30 días antes de vencer

**Pasos:**

```
1. Crear certificación con vencimiento = Hoy + 25 días
2. Esperar a que se ejecute Flow (programado para cierta hora)
3. Revisar inbox del empleado
```

**Resultado esperado:**
- ✅ Email llega dentro de 5-10 minutos
- ✅ Asunto: "Certificación por Vencer"
- ✅ Email tiene formato correcto
- ✅ Links funcionan

---

### TC-104-02: Email en fecha vencimiento

**Pasos:**

```
1. Crear certificación con vencimiento = Hoy
2. Esperar a que se ejecute Flow
3. Revisar inbox
```

**Resultado esperado:**
- ✅ Email alerta de vencimiento
- ✅ Formato correcto

---

## TC-105: Reportes

### TC-105-01: Export CSV en live mode

**Pasos:**

```
1. Click "Exportar CSV"
2. Archivo descarga
3. Datos coinciden con lo mostrado en app
```

**Resultado esperado:**
- ✅ CSV descarga correctamente
- ✅ Datos coinciden

---

# TEST-CASES — RESPONSIVE & NAVEGADORES

## TC-201: Desktop (1920x1080)

```
[ ] Todos los elementos visibles
[ ] Sidebar y main content lado a lado
[ ] Tablas completas sin scroll horizontal
[ ] Botones accesibles
```

## TC-202: Tablet (768x1024)

```
[ ] Layout se adapta correctamente
[ ] Sidebar puede colapsar
[ ] Tablas scrollean horizontalmente si es necesario
[ ] Touch targets > 44px
```

## TC-203: Móvil (375x812)

```
[ ] Sidebar colapsa
[ ] Hamburger menu funciona
[ ] Tablas scrollean
[ ] Botones grandes y tocables
[ ] Sin content overflow
```

## TC-204: Navegadores

```
[ ] Chrome/Edge (último)
[ ] Firefox (último)
[ ] Safari (si aplica)
[ ] IE 11 (soporte mínimo - si aplica en SP)
```

---

# REPORTE DE RESULTADOS

## Template de Reporte

Copiar y llenar después de ejecutar tests:

```
╔════════════════════════════════════════════════════════════╗
║ REPORTE DE TESTING - CertTrack IT                          ║
╠════════════════════════════════════════════════════════════╣
║ Fecha: [___________]                                       ║
║ Tester: [___________]                                      ║
║ Ambiente: [ ] Demo  [ ] Live                               ║
║ Navegador: [___________]                                   ║
║ Resolución: [___________]                                  ║
╠════════════════════════════════════════════════════════════╣

RESUMEN RESULTADOS:
  Total TC ejecutados: ___
  Passed (✅): ___
  Failed (❌): ___
  Warnings (⚠️): ___
  Skipped (🔄): ___
  
  % Pass Rate: ___% (ideal: > 95%)

BLOCKERS (Issue críticos que impiden uso):
  [ ] Ninguno - Go Live approved ✅
  [ ] Si hay, listar:
    1. [Descripción issue]
       Severidad: [ ] Critical [ ] High [ ] Medium
       
WARNINGS (Comportamiento esperado pero subóptimo):
  1. [Descripción]

NOTAS & OBSERVACIONES:
  [Espacio libre para feedback]

FIRMA:
  Tester: ____________  Fecha: ____________
  Lead: ____________    Fecha: ____________
```

## Criterios de Aprobación (Go Live)

```
✅ APROBADO para Go Live si:
  - % Pass Rate ≥ 95%
  - Cero blockers críticos
  - Todos los casos CRUD funcionan
  - Permisos funcionan correctamente
  - Alertas funcionan (en live mode)

⚠️  CONDICIONAL si:
  - % Pass Rate 90-94%
  - Warnings menores, sin impacto usuarios
  - Plan para fix en próxima versión

❌ RECHAZADO si:
  - % Pass Rate < 90%
  - Algún blocker crítico
  - Permisos rotos
  - Data loss posible
```

---

**Última actualización:** 2026-05-04  
**Próxima revisión:** 2026-06-04
