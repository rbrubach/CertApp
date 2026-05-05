# DEPLOYMENT.md — CertTrack IT

**Última actualización:** 2026-05-04  
**Versión:** 1.0 (Single HTML)  
**Estado:** Ready for Production (Demo Mode)

---

## 📋 Tabla de Contenidos

1. [Overview](#overview)
2. [Prerequisitos](#prerequisitos)
3. [Opciones de Deployment](#opciones-de-deployment)
4. [Pasos de Instalación](#pasos-de-instalación)
5. [Configuración](#configuración)
6. [Testing Pre-Deployment](#testing-pre-deployment)
7. [Monitoreo & Mantenimiento](#monitoreo--mantenimiento)
8. [Roadmap de Evolución](#roadmap-de-evolución)
9. [Rollback & Contingencia](#rollback--contingencia)
10. [FAQ & Troubleshooting](#faq--troubleshooting)

---

## Overview

### ¿Qué se Despliega?

```
certtrack-it-full.html (≈120KB, autosuficiente)
├─ CSS (inline, ≈8KB)
├─ HTML (estructura, ≈4KB)
└─ JavaScript (lógica, ≈108KB)
   ├─ Datos demo (hardcoded)
   └─ Llamadas REST (para live mode)
```

### Características Importantes

- ✅ **Archivo único:** No requiere build process
- ✅ **Sin dependencias:** Excepto Google Fonts (CDN)
- ✅ **Modo Demo:** Funciona inmediatamente (datos hardcoded)
- ✅ **Modo Live:** Se conecta a SharePoint REST API
- ✅ **Navegador:** IE 11+ / Edge / Chrome / Firefox

### Ambiente Actual

- **Modo:** `CFG.mode = 'demo'` (hardcoded)
- **Datos:** Arrays JavaScript en memoria
- **Estado:** Listo para SharePoint Online

---

## Prerequisitos

### Técnicos

- [ ] **SharePoint Online** (SPO 2019 o newer)
- [ ] **Usuario con permisos:**
  - Owner en sitio SharePoint (para crear listas)
  - Global Admin (para Azure AD groups) — *opcional si ya existen*

### Listas SharePoint Requeridas

Deben crearse **antes** de pasar a modo `live`:

| Lista | Propósito | Permisos |
|-------|-----------|----------|
| `Certificaciones` | Registros de certs de empleados | Item-level (Admin: Full, Empleado: edit own) |
| `Colaboradores` | Datos maestros de empleados | Read all |
| `MaestroCertificaciones` | Catálogo de certificaciones | Read all |
| `MaestroEntidades` | Certificadores/entidades | Read all |
| `Roadmap` | Roadmap futuro | Role-based |

### Grupos Azure AD Requeridos

```
CertTrack_Admins       → Full Control en sitio
CertTrack_Empleados    → Contribute + Read
```

### Dependencias Externas

- **Google Fonts:** `fonts.googleapis.com` (Aptos)
  - Fallback: Arial si no está disponible
  - **⚠️ Consideración:** Si la red bloquea CDN externo, descarga la fuente localmente (ver sección Evolución)

---

## Opciones de Deployment

### Opción A: Upload a Biblioteca (RÁPIDO)

**Ideal para:** Testing inicial, demo, equipo pequeño

**Pasos:**
1. Subir `certtrack-it-full.html` a biblioteca de documentos
2. Acceder vía enlace directo (se abre en navegador)
3. **No se integra** con UI de SharePoint

**Ventajas:** Mínima configuración, fácil de actualizar  
**Desventajas:** Sin integración visual, sin contexto SP

---

### Opción B: Web Part HTML (RECOMENDADO AHORA)

**Ideal para:** Integración en página SP, acceso desde sitio

**Pasos:**
1. Crear página en SharePoint
2. Editar → Add Web Part
3. Buscar "HTML" o "Code Embed"
4. Pegar contenido del archivo (o usar `<iframe>`)
5. Publish página

**HTML de referencia:**
```html
<iframe 
  src="/sites/certtrack/DocumentLibrary/certtrack-it-full.html" 
  style="width:100%; height:800px; border:none;"
  title="CertTrack IT">
</iframe>
```

**Ventajas:** Integrado visualmente, acceso desde sitio  
**Desventajas:** Límite de tamaño de web part (si HTML > 150KB)

---

### Opción C: SPFx Component (FUTURO)

**Ideal para:** Distribución en múltiples tenants, versioning automático

**Roadmap:** Q3 2026 (si es necesario)

**Pasos (aproximados):**
1. Refactorizar a componente React
2. Generar `.sppkg` con yeoman/yo @microsoft/sharepoint
3. Instalar en App Catalog
4. Agregar a sitios vía "Add from your organization"

**Ventajas:** Distribución escalable, actualizaciones automáticas  
**Desventajas:** Requiere Node.js, build process, más complejidad

---

## Pasos de Instalación

### 🎯 Scenario A: Deployment Rápido (Demo Mode)

**Timeline:** 15 min  
**Usuario:** Cualquier owner de sitio SP  
**Destino:** Prueba de concepto, demo interna

#### Paso 1: Preparar Archivo

```bash
# En tu máquina local
1. Descargar certtrack-it-full.html
2. Verificar que abre en navegador (debe mostrar "Demo Mode")
3. Probar con diferentes roles (cambiar rol en UI)
```

#### Paso 2: Subir a SharePoint

```
SharePoint Site → Documents Library → Upload
Seleccionar: certtrack-it-full.html
```

#### Paso 3: Crear Acceso Compartido

```
Right-click archivo → Share
Agregar grupo: CertTrack_Empleados
Permiso: Can edit
```

#### Paso 4: Verificar

```
1. Abrir link compartido
2. Verificar que carga (debe decir "DEMO" en header)
3. Probar cambio de rol (Admin vs Empleado)
4. Probar crear/editar certificado (datos en memoria, no persisten)
```

✅ **Demo está activo.** Los datos se pierden al refresh (es normal).

---

### 🎯 Scenario B: Deployment en Producción (Live Mode)

**Timeline:** 4-6 horas + testing  
**Usuario:** Admin técnico + Power User de SP  
**Destino:** Uso real con datos persistentes en SharePoint

#### Fase 0: Preparación (1-2 horas)

**Checklist:**
- [ ] Crear listas SharePoint (ver template en `/docs/SHAREPOINT-SETUP.md`)
- [ ] Crear grupos Azure AD (`CertTrack_Admins`, `CertTrack_Empleados`)
- [ ] Llenar datos maestros (certificaciones, entidades)
- [ ] Asignar empleados a equipo
- [ ] Configurar Power Automate flows (opcional, ver `/docs/POWER-AUTOMATE.md`)

#### Fase 1: Modificar Archivo (15 min)

**En `certtrack-it-full.html`, buscar sección `CFG`:**

```javascript
// ≈ línea 810
const CFG = {
  mode: 'demo',  // ← CAMBIAR A 'live'
  user: null,
  role: null,
  spSite: null,
  spUser: null,
  spRole: null
};
```

**Cambiar a:**

```javascript
const CFG = {
  mode: 'live',  // ← PRODUCCIÓN
  user: null,    // Se obtiene de _spPageContextInfo
  role: null,    // Se detecta de grupos AD
  spSite: null,  // Se obtiene de _spPageContextInfo
  spUser: null,
  spRole: null
};

// Agregar esta función DESPUÉS de CFG (≈ línea 815)
function initSPContext() {
  if (window._spPageContextInfo) {
    CFG.spSite = _spPageContextInfo.webAbsoluteUrl;
    CFG.spUser = _spPageContextInfo.userDisplayName;
    CFG.user = _spPageContextInfo.userLoginName;
    
    // Detectar rol: buscar si usuario está en grupo admin
    // (Nota: en producción usar REST call a grupo de SP)
    CFG.role = 'empleado'; // Default
    
    console.log('[CertTrack] SP Context:', CFG);
  } else {
    console.warn('[CertTrack] No _spPageContextInfo - modo fallback');
    CFG.role = 'empleado';
  }
}

// Llamar al cargar página
document.addEventListener('DOMContentLoaded', initSPContext);
```

#### Fase 2: Crear Web Part en SP (30 min)

```
1. Ir a sitio SharePoint
2. Editar página destino (o crear nueva)
3. Add Web Part → "Code Embed" / "HTML"
4. Copiar contenido completo de certtrack-it-full.html (YA MODIFICADO)
5. Paste en web part
6. Set height: 800px (o más según pantalla)
7. Publish
```

**Alternativa (usando iframe):**
```html
<iframe 
  src="/sites/certtrack/DocumentLibrary/certtrack-it-full.html" 
  style="width:100%; height:1000px; border:none;"
  title="CertTrack IT - Gestión Certificaciones">
</iframe>
```

#### Fase 3: Configurar Permisos Item-Level

En lista `Certificaciones`:

```
1. Settings → List settings → Item-level permissions
2. Crear vista para admins: "Ver todos"
3. Crear vista para empleados: "Ver solo míos"
   → Filter: [Asignado A] = [Me]
```

#### Fase 4: Testing (1-2 horas)

Ver sección [Testing Pre-Deployment](#testing-pre-deployment)

#### Fase 5: Publicar & Comunicar (30 min)

```
1. Anunciar en equipo (Teams/Email)
2. Proporcionar enlace al sitio
3. Ofrecer sesión de capacitación
4. Recopilar feedback
```

---

## Configuración

### Archivo: `CONFIGURACION.md` (Referencia)

Crear `/docs/CONFIGURACION.md` con detalles de cada variable `CFG`:

```markdown
# CFG Object

## mode: 'demo' | 'live'
- **demo:** Usa datos hardcoded en JS, ideal para testing
- **live:** Llama REST API de SharePoint, datos persistentes

## user: string (email o login)
- Obtenido de _spPageContextInfo.userLoginName
- Usado para filtrar datos "ver solo míos"

## role: 'admin' | 'empleado'
- Detectado de grupo Azure AD
- Controla qué páginas/acciones ve el usuario

## spSite, spUser, spRole
- Valores SharePoint, llenados en live mode
```

### Variables de Entorno (Para Futuro)

Cuando evolucione a modularización:

```javascript
// .env (no commitear credenciales)
VITE_SP_SITE=https://tenant.sharepoint.com/sites/certtrack
VITE_MODE=live
VITE_TENANT_ID=xxx
```

---

## Testing Pre-Deployment

### ✅ Checklist Demo Mode

```
Test Case 1: Carga Inicial
[ ] Página carga sin errores
[ ] Header muestra "DEMO"
[ ] Sidebar muestra todos los items
[ ] Al cambiar rol (Admin ↔ Empleado) se actualizan vistas

Test Case 2: Navegación
[ ] Todos los items del sidebar funcionan
[ ] Cambio de página es suave (no parpadea)
[ ] Breadcrumb/header actualiza correctamente

Test Case 3: Datos Demo (Admin)
[ ] Home: muestra 4 stats (total, vigentes, por vencer, vencidas)
[ ] Mis Certs: muestra 3+ certificados
[ ] Roadmap: muestra 2+ roadmaps
[ ] Maestros: ver catálogos (Certs, Entidades)

Test Case 4: Datos Demo (Empleado)
[ ] Mis Certs: ve solo sus certs (no todas)
[ ] Alerts: ve solo sus alertas
[ ] NO ve: Admin pages (Empleados, Roadmap admin)

Test Case 5: Modales & Formularios
[ ] Click "New Cert" → abre modal
[ ] Llenar formulario → "Save" → alerta "ok"
[ ] Datos aparecen en tabla inmediatamente
[ ] Click "Delete" → pide confirmación
[ ] Delete ejecuta → alerta "ok"

Test Case 6: Responsive
[ ] Abrir en Mobile (375px) → sidebar colapsa
[ ] Tabla scrollea horizontalmente
[ ] Botones accesibles
```

**Test Data (Demo):**
- Admin user: cualquiera, cambiar rol a "admin"
- Empleado: cambiar rol a "empleado"
- Certificados: 5 en total (2 vigentes, 2 por vencer, 1 vencida)

### ✅ Checklist Live Mode

**Prerequisito:** Listas SP creadas + datos maestros ingresados

```
Test Case 1: Conexión SharePoint
[ ] Console (F12) NO muestra errores de CORS
[ ] API call a lista "Certificaciones" exitosa
[ ] Datos cargan de SP (no de hardcoded)

Test Case 2: Permisos
[ ] Admin: ve TODOS los certs de TODOS los empleados
[ ] Empleado: ve SOLO sus certs
[ ] Intento de accedera /admin por empleado → bloqueado

Test Case 3: CRUD
[ ] Admin crea certificado → aparece en SP
[ ] Empleado edita suyo → se guarda en SP
[ ] Delete → item se marca como deleted en SP
[ ] Refresh página → datos persisten

Test Case 4: Notificaciones
[ ] Power Automate envía alert 30 días antes
[ ] Email llega con datos correctos

Test Case 5: Reportes
[ ] Export CSV funciona (descarga archivo)
[ ] Datos en CSV coinciden con tabla
```

**Usuarios Teste (Live):**
- Admin: usuario@tenant.com (miembro de CertTrack_Admins)
- Empleado: empleado@tenant.com (miembro de CertTrack_Empleados)

### Herramientas

```
Browser DevTools (F12)
├─ Console: errores JS
├─ Network: llamadas REST
└─ Storage: localStorage (si se usa en futuro)

SharePoint
├─ Ir a lista → Filter/Sort para verificar datos
└─ Revisar Power Automate Runs
```

---

## Monitoreo & Mantenimiento

### Post-Deployment (Primeras 2 semanas)

```
1. Recopilar feedback de usuarios (Teams, email, survey)
2. Revisar errores console (F12)
3. Revisar network calls (performance)
4. Revisar logs Power Automate
5. Documento de issues encontrados
```

### Mantenimiento Mensual

```
1. Revisar reportes de uso (analytics si se agrega)
2. Backlog de mejoras (priorizar)
3. Actualizaciones de datos maestros (entidades, certificaciones)
4. Testing de alerts (manuel para que no se pierda)
```

### Mantenimiento Anual

```
1. Revisar estructura de datos SharePoint
2. Análisis de crecimiento (¿necesita optimización?)
3. Revisión de seguridad
4. Evaluación de evolución técnica (¿pasar a SPFx?)
```

---

## Roadmap de Evolución

### v1.0 (Actual)
- ✅ Single HTML file
- ✅ Demo mode + Live mode (estructura lista)
- ✅ Rol-based access (admin vs empleado)
- ✅ CRUD básico

### v1.1 (Q3 2026)
- 🔄 **Mejoras menores**
  - Descarga local de Google Fonts (si CDN bloqueado)
  - Más estados de certificado
  - Reportes mejorados
  - Dark/Light theme toggle

### v2.0 (Q4 2026 - Considerar si necesario)
- 🔄 **Modularización**
  - Separar JS en módulos
  - Build step simple (concatenación)
  - Versionado de assets

### v3.0 (2027 - Si escala significativamente)
- 🔄 **SPFx Component**
  - Refactorizar a React
  - Distribución en App Catalog
  - Actualizaciones automáticas
  - Mejor integración SP

**Criterios para evolucionar:**
- Más de 500 certificados activos → considerar optimización DB
- Más de 5 tenants → considerar SPFx
- Nuevos requisitos complejos → modularizar código

---

## Rollback & Contingencia

### Escenario: Error en Live Mode

**Paso 1: Rollback rápido (5 min)**

```
1. Ir a biblioteca donde se subió certtrack-it-full.html
2. Ver historial de versiones (click ...)
3. Restaurar versión anterior (la del último deploy exitoso)
4. Actualizar página web part (F5 en SharePoint)
```

**Paso 2: Diagnóstico**

```
1. Abrir Developer Tools (F12)
2. Ver Console para errores JS
3. Ver Network para fallos REST
4. Revisar lista SharePoint directamente (¿datos OK?)
5. Documentar error en issue tracker
```

**Paso 3: Fix & Re-Deploy**

```
1. Hacer cambio en certtrack-it-full.html
2. Pruebas locales en modo demo
3. Subir nueva versión a SP
4. Testing en SP stage (si existe)
5. Publicar en prod
```

### Escenario: Corrupción de Datos SP

```
1. STOP: No seguir haciendo cambios
2. Restaurar lista SP desde versión anterior
   SharePoint → List settings → Version history
3. Verificar integridad de datos
4. Comunicar a usuarios
5. Crear POST-MORTEM
```

### Escenario: CORS/Permisos REST API

**Síntoma:** Error en console `"Access denied" | "The remote server returned an error"`

**Solución:**
```
1. Verificar que usuario está en grupo CertTrack_*
2. Verificar que lista SP existe
3. Verificar que URL es correcta (CFG.spSite)
4. Revisar App Permissions (SharePoint → Manage Access)
```

### Escenario: Actualización de Usuarios

Si cambia plantilla de empleados:

```
1. Actualizar lista "Colaboradores" en SP
2. Actualizar grupos Azure AD (agregar/quitar usuarios)
3. Reiniciar navegador (limpiar cache)
4. Verificar que nuevos usuarios ven sus datos
```

---

## FAQ & Troubleshooting

### P: ¿Por qué mi empleado no ve sus certificados en live mode?

**R:** Posibles causas:

```
1. Usuario NO está en grupo CertTrack_Empleados
   → Solución: Agregar a grupo en Azure AD

2. Certificados no tienen "colabId" correcto
   → Solución: Verificar lista Certificaciones, editar items

3. Permiso item-level NO está configurado
   → Solución: Ir a list settings, habilitar item-level,
     crear vista con filter [AsignadoA] = [Me]

4. Usuario logueado con cuenta diferente
   → Solución: Salir de SP, ingresar con cuenta correcta
```

### P: ¿Funciona sin acceso a internet (Google Fonts)?

**R:** Parcialmente.

```
Sin CDN: Se carga con Arial (fallback)
Con CDN: Se carga con Aptos (mejor diseño)

Para asegurar que funciona sin CDN:
1. Descargar Aptos desde Google Fonts
2. Hostear en carpeta /fonts de SP
3. Cambiar @import en <style>
   @import url('/sites/certtrack/fonts/aptos.css');
```

### P: ¿Necesito instalar certificados SSL?

**R:** No. SharePoint Online ya usa HTTPS. La app funciona como parte de la página SP.

### P: ¿Puedo tener múltiples versiones en paralelo?

**R:** Sí, para testing.

```
1. Subir a biblioteca: certtrack-it-full.html (PROD)
2. Subir a biblioteca: certtrack-it-full-BETA.html (TESTING)
3. Crear dos web parts, uno por versión
4. Dejar BETA con acceso limitado (grupo de test)
```

### P: ¿Cada cuánto debo hacer backup?

**R:** Automático en SharePoint.

```
- Todos los datos en listas SP se respaldan automáticamente
- El archivo HTML se verciona automáticamente
- Sugerir: Copiar versión estable a carpeta /archive cada mes
```

### P: ¿Cómo sé si alguien accedió la app?

**R:** Vía SharePoint auditing (requiere license Premium).

```
SharePoint Admin Center → Audit Logs → Search
Buscar: "certtrack-it-full.html" o "Certificaciones list"
```

### P: ¿Puedo personalizar colores/logo?

**R:** Sí, variables CSS.

```
En <style>, sección :root
--ink, --bg, --accent, etc.

Cambiar y subir nueva versión
```

---

## Información de Contacto & Escalation

| Rol | Contacto | Responsabilidad |
|-----|----------|-----------------|
| **Tech Lead** | @your-name | Modificaciones código |
| **SP Admin** | @sp-admin | Listas, permisos, auditing |
| **Power Automate Admin** | @poa-admin | Flows, alertas |
| **Project Owner** | @owner | Backlog, roadmap |

---

## Versionado & Histórico

### v1.0 (2026-05-04)
- ✅ Initial deployment guide
- ✅ Demo + Live mode
- ✅ Testing checklist
- ✅ Rollback procedures

**Próxima actualización:** 2026-08-04 (Post-deployment review)

---

## Apéndices

### A. Script de Validación (PowerShell)

```powershell
# Validar listas SP existen
$siteUrl = "https://tenant.sharepoint.com/sites/certtrack"
$lists = @("Certificaciones", "Colaboradores", "MaestroCertificaciones", 
           "MaestroEntidades", "Roadmap")

Connect-PnPOnline -Url $siteUrl -Interactive
foreach ($list in $lists) {
    $l = Get-PnPList -Identity $list -ErrorAction SilentlyContinue
    if ($l) { Write-Host "✓ $list existe" }
    else { Write-Host "✗ $list NO EXISTE - crear antes de live mode" }
}
```

### B. Checklist Pre-Deployment

```
[ ] certtrack-it-full.html probado localmente (demo mode)
[ ] CFG.mode cambió a 'live' (si prod)
[ ] Listas SharePoint creadas
[ ] Grupos Azure AD creados
[ ] Datos maestros cargados
[ ] Permisos item-level configurados
[ ] Power Automate flows configurados (opcional)
[ ] Testing completado (ver checklist)
[ ] Usuarios informados
[ ] Backup de versión anterior guardado
[ ] Contacto de soporte documentado
[ ] Go/No-Go aprobado por PM
```

---

**Generated:** 2026-05-04  
**Author:** CertTrack Team  
**Next Review:** 2026-08-04
