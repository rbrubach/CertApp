# PRE-DEPLOYMENT-CHECKLIST.md — Go / No-Go Decision

**Propósito:** Garantizar que la app está lista para producción  
**Quién:** Tech Lead, QA Manager, Project Owner  
**Timeline:** 2-3 horas antes de publicar  
**Decisión:** APROBADO para Go Live / RECHAZADO - Pausar

---

## 📋 Fases del Checklist

Este checklist está organizado en 4 fases:
1. **Code & Build** — Validación del código
2. **Testing** — Ejecución de test cases
3. **Deployment** — Preparación de infraestructura
4. **Go/No-Go** — Decisión final

---

# FASE 1: CODE & BUILD

## 1.1 Código & Configuración

- [ ] **CFG.mode está correcto**
  - [ ] Demo mode: `CFG.mode = 'demo'` (para testing)
  - [ ] Live mode: `CFG.mode = 'live'` (para producción)
  - [ ] Ambiente correcto según target

- [ ] **No hay console errors**
  - [ ] Abrir DevTools (F12)
  - [ ] Navegar todas las páginas
  - [ ] Console → No debe haber mensajes en rojo
  - [ ] Anotar cualquier warning (si no son críticos)

- [ ] **Variables CFG tienen valores sensatos**
  ```javascript
  CFG.spSite    // URL SharePoint (si live)
  CFG.spUser    // Email user (si live)
  CFG.spRole    // 'admin' o 'empleado'
  ```

- [ ] **URLs internas son correctas**
  - [ ] Google Fonts CDN accesible
  - [ ] Si offline, fallback a Arial funciona
  - [ ] Links internos (mailto, docUrl) válidos

- [ ] **Datos demo cargan correctamente**
  - [ ] Arrays COLABORADORES, MASTER_CERTS, etc. válidos
  - [ ] No hay datos corrupted o undefined
  - [ ] Fechas en formato correcto (yyyy-MM-dd)

## 1.2 Performance Baseline

- [ ] **Tiempo de carga < 2 segundos**
  - [ ] Medir con DevTools Performance tab
  - [ ] Documento baseline para comparaciones futuras

- [ ] **Navegación entre páginas < 500ms**
  - [ ] Cambio suave sin lag
  - [ ] No hay freeze en UI

- [ ] **Archivo HTML tamaño razonable**
  - [ ] Máximo esperado: 150KB (actual: ~120KB)
  - [ ] Si crece mucho, considerar modularización

---

# FASE 2: TESTING

## 2.1 Smoke Test (5 min)

- [ ] **Página carga sin errores**
  - [ ] Abrir en navegador
  - [ ] Header visible
  - [ ] Sidebar visible
  - [ ] Main content visible

- [ ] **Cambio de rol funciona**
  - [ ] Click user chip
  - [ ] Cambiar Admin ↔ Empleado
  - [ ] UI se actualiza

- [ ] **Navegación funciona**
  - [ ] Click cada item sidebar
  - [ ] Página cambia sin refresh

- [ ] **Tabla de datos carga**
  - [ ] Navegar a "Mis Certificaciones"
  - [ ] Datos visibles
  - [ ] Scroll funciona

## 2.2 Functional Testing (1-2 horas)

**Ejecutar casos de prueba de [TEST-CASES.md](./TEST-CASES.md)**

### Demo Mode

- [ ] **TC-001:** Carga inicial ✅
- [ ] **TC-002:** Navegación ✅
- [ ] **TC-003:** Home page stats ✅
- [ ] **TC-004:** CRUD completo (Create, Read, Update, Delete) ✅
- [ ] **TC-005:** Alertas ✅
- [ ] **TC-006:** Validación de formularios ✅
- [ ] **TC-007:** Modales ✅
- [ ] **TC-008:** Exports CSV ✅

**Resultado:**
- [ ] ≥ 95% test cases passed
- [ ] 0 blockers críticos
- [ ] Máximo 2-3 warnings menores

### Live Mode (Si aplica)

- [ ] **TC-101:** Conexión SP ✅
- [ ] **TC-102:** CRUD en SP ✅
- [ ] **TC-103:** Permisos item-level ✅
- [ ] **TC-104:** Alertas automáticas ✅

**Resultado:**
- [ ] ≥ 95% test cases passed
- [ ] REST API funciona
- [ ] Datos persisten en SP

## 2.3 Security Testing

- [ ] **No hay inyección de código posible**
  - [ ] Revisar inputs de formularios
  - [ ] XSS prevention (escaping correcto)

- [ ] **Permisos están gateados**
  - [ ] Empleado NO puede acceder admin pages
  - [ ] Si intenta URL directa, error
  - [ ] Data filtering funciona correctamente

- [ ] **Credenciales NO se exponen**
  - [ ] Console log NO muestra passwords
  - [ ] Network tab → URLs no contienen credenciales
  - [ ] localStorage no almacena secrets

- [ ] **CORS y cross-origin checks**
  - [ ] Si live, SP REST calls no tienen errors CORS
  - [ ] Origen correcto

## 2.4 Responsive Testing

- [ ] **Desktop (1920x1080)** ✅
  - [ ] Layout correcto
  - [ ] Todos elementos visibles
  - [ ] Legibilidad OK

- [ ] **Tablet (768x1024)** ✅
  - [ ] Sidebar colapsa si necesario
  - [ ] Tablas scrollean
  - [ ] Touch targets accesibles

- [ ] **Móvil (375x812)** ✅
  - [ ] Hamburger menu funciona
  - [ ] Sin overflow horizontal
  - [ ] Botones tocables

## 2.5 Browser Compatibility

- [ ] **Chrome/Edge latest** ✅
- [ ] **Firefox latest** ✅
- [ ] **Safari** ✅ (si aplica)
- [ ] **IE 11** ⚠️ (soporte mínimo, posibles issues)

---

# FASE 3: DEPLOYMENT

## 3.1 Preparación SharePoint (Live mode)

- [ ] **Listas creadas**
  - [ ] Certificaciones
  - [ ] Colaboradores
  - [ ] MaestroCertificaciones
  - [ ] MaestroEntidades
  - [ ] Roadmap

- [ ] **Grupos Azure AD creados**
  - [ ] CertTrack_Admins (members: _____)
  - [ ] CertTrack_Empleados (members: _____)

- [ ] **Permisos asignados en sitio**
  - [ ] CertTrack_Admins → Owner
  - [ ] CertTrack_Empleados → Edit

- [ ] **Item-level security habilitada**
  - [ ] Lista Certificaciones: limit user permissions ✓
  - [ ] Empleados ven solo propios

- [ ] **Datos maestros cargados**
  - [ ] Certificaciones master completadas
  - [ ] Entidades completadas
  - [ ] Colaboradores cargados

- [ ] **Power Automate flows configurados** (opcional)
  - [ ] Flow 1: 30 días antes ✓
  - [ ] Flow 2: Fecha vencimiento ✓
  - [ ] Flow 3: Reporte semanal ✓

## 3.2 Preparación Archivo

- [ ] **Archivo final nombre correcto**
  - [ ] `certtrack-it-full.html` (no con versión o test)

- [ ] **Archivo subido a ubicación correcta**
  - [ ] SharePoint Documents library
  - [ ] O web part (si usando web part option)

- [ ] **Versión anterior respaldada**
  - [ ] Copiar a `/archive`
  - [ ] Ej: `archive/certtrack-it-full-v0.9.html`

- [ ] **Links accesibles**
  - [ ] URL para web part accesible
  - [ ] Link compartido válido
  - [ ] QR code (si necesario) funciona

## 3.3 Comunicación & Documentación

- [ ] **Equipo informado**
  - [ ] Email enviado a usuarios
  - [ ] Fecha de lanzamiento clara
  - [ ] Link a guía de usuario

- [ ] **Guías de usuario creadas**
  - [ ] GUIA-USUARIO-ADMIN.md (pendiente)
  - [ ] GUIA-USUARIO-EMPLEADO.md (pendiente)
  - [ ] O al menos un README simple

- [ ] **Soporte documentado**
  - [ ] Contacto técnico designado
  - [ ] Teléfono / Email de escalation
  - [ ] Horario de soporte

- [ ] **FAQ preparada**
  - [ ] Preguntas comunes respondidas
  - [ ] Troubleshooting básico listo

---

# FASE 4: GO / NO-GO DECISION

## 4.1 Criterios de Aprobación (MUST HAVE)

```
✅ TODAS estas condiciones deben cumplirse para Go:

☑ Fase 1: Code & Build
  [ ] CFG mode correcto
  [ ] Sin console errors críticos
  [ ] Performance OK
  [ ] Datos validos

☑ Fase 2: Testing
  [ ] ≥ 95% test cases PASSED
  [ ] 0 blockers críticos
  [ ] ≤ 3 warnings menores
  [ ] Permisos funcionan
  [ ] Responsive OK

☑ Fase 3: Deployment
  [ ] SharePoint setup completo (live mode)
  [ ] Archivo en ubicación correcta
  [ ] Versión anterior respaldada
  [ ] Comunicación enviada

☑ Fase 4: Aprobación
  [ ] Tech Lead: revisó y aprobó
  [ ] QA: revisó y aprobó
  [ ] Project Owner: revisó y aprobó
```

## 4.2 Métricas de Decisión

### Scoring

Asignar puntos:
- Cero blockers: **+100** (requirement)
- Test pass rate ≥ 95%: **+50**
- Performance OK: **+25**
- Security OK: **+25**

**Threshold:**
```
Score ≥ 150 → GO LIVE ✅
Score < 150 → NO-GO (pausar, fix issues)
```

## 4.3 Escalation Path

Si hay duda:

```
1. Tech Lead → revisa y decide
2. Si aún hay duda → Project Owner
3. Si blockers → PAUSAR y fix antes de retry
4. Timeline: Máximo 1 semana para resolver
```

---

# ✅ SIGN-OFF

## Aprobaciones Requeridas

```
Nombre: ________________________  Rol: ______________
Firma: _________________________  Fecha: _____________

Nombre: ________________________  Rol: ______________
Firma: _________________________  Fecha: _____________

Nombre: ________________________  Rol: ______________
Firma: _________________________  Fecha: _____________
```

## Notas Finales

```
Observaciones / Risk assessment:
_______________________________________________________
_______________________________________________________
_______________________________________________________

Plan de rollback (en caso de problema):
_______________________________________________________
_______________________________________________________

Versión aprobada: __________ (ej: certtrack-it-full.html v1.0)
Fecha deployment: __________ (ej: 2026-05-15 10:00 AM)
```

---

# 🚨 POST-DEPLOYMENT (First 24 hrs)

Una vez en vivo, monitorear:

- [ ] **Usuarios pueden acceder**
  - [ ] Sin errores 404 / 403
  - [ ] Cargan datos correctamente

- [ ] **No hay crash report**
  - [ ] Console monitor por errores
  - [ ] SharePoint logs clean

- [ ] **Alertas funcionan** (si live)
  - [ ] Power Automate ejecuta sin error
  - [ ] Emails se envían

- [ ] **Feedback inicial**
  - [ ] Usuarios reportan issues
  - [ ] Documentar en issue tracker

**If critical issue:**
```
1. PAUSAR
2. Notificar usuarios
3. Activar rollback (ver DEPLOYMENT.md)
4. Investigar root cause
5. Fix + test nuevamente
6. Retry deployment
```

---

## Resumen Rápido (1 página)

Para imprimir / distribuir:

```
╔══════════════════════════════════════════════════╗
║ CertTrack IT — PRE-DEPLOYMENT GO/NO-GO           ║
╠══════════════════════════════════════════════════╣
║ Fecha: _______________                           ║
║ Ambiente: [ ] Demo  [ ] Live                     ║
╠══════════════════════════════════════════════════╣
║ FASES:                                           ║
║ [ ] Fase 1: Code & Build        Status: ____     ║
║ [ ] Fase 2: Testing             Status: ____     ║
║ [ ] Fase 3: Deployment          Status: ____     ║
╠══════════════════════════════════════════════════╣
║ DECISIÓN FINAL:                                  ║
║                                                  ║
║ [ ] ✅ GO LIVE - Aprobado                        ║
║ [ ] ❌ NO-GO - Pausar hasta resolver             ║
║                                                  ║
║ Autorizador: ___________                         ║
║ Fecha: ___________                               ║
╚══════════════════════════════════════════════════╝
```

---

**Última actualización:** 2026-05-04  
**Válido para:** v1.0+  
**Próxima revisión:** 2026-06-04
