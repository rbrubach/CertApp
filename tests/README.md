# 🧪 TESTING — CertTrack IT

**Bienvenido a la carpeta de testing.**

Esta carpeta contiene toda la documentación y procedimientos para garantizar que CertTrack IT funciona correctamente antes de ir a producción.

---

## 📋 Archivos de Testing

### 📄 [TEST-CASES.md](./TEST-CASES.md) — Casos Detallados por Feature

**Para:** QA, Testers  
**Contiene:** 200+ test cases organizados en:
- **TC-001 a TC-010:** Demo Mode (carga, navegación, CRUD, alertas, etc.)
- **TC-101 a TC-105:** Live Mode (SharePoint, permisos, alertas automáticas)
- **TC-201 a TC-204:** Responsive + Navegadores
- Template de reporte + criterios aprobación

**Cómo usar:**
```
1. Abre TEST-CASES.md
2. Ve a sección según ambiente (demo vs live)
3. Ejecuta cada test case
4. Marca ✅ (pass) o ❌ (fail)
5. Llena reporte al final
```

---

### 📄 [TEST-DATA.md](./TEST-DATA.md) — Datos Consistentes para Testing

**Para:** Todos (testers, devs)  
**Contiene:**
- Schema de datos demo (5 colaboradores, 5 certs, 3 roadmaps)
- Schema de datos SharePoint
- Distribución de estados (vigente, por vencer, vencida)
- Cálculos de estadísticas para validación
- Instrucciones de cleanup

**Cómo usar:**
```
1. Antes de testear, revisar TEST-DATA.md
2. Conocer qué datos hay en demo
3. Saber qué esperar en estadísticas
4. Al acabar testing, cleanup datos según guía
```

**Datos Demo (Fijos):**
```
5 Certificaciones:
  ID 1: C001, GCP, 90+ días (vigente)
  ID 2: C001, Azure, 180+ días (vigente)
  ID 3: C002, AWS, +25 días (por vencer)
  ID 4: C003, Oracle, +15 días (por vencer)
  ID 5: C004, Scrum, -10 días (vencida)

Stats: Total=5, Vigentes=2, Por vencer=2, Vencidas=1
```

---

### 📄 [PRE-DEPLOYMENT-CHECKLIST.md](./PRE-DEPLOYMENT-CHECKLIST.md) — Go/No-Go Decision

**Para:** Tech Lead, QA Manager, Project Owner  
**Contiene:** 4 fases (Code, Testing, Deployment, Decision)
- Checklist detallado de 40+ items
- Criterios de aprobación (MUST HAVE)
- Scoring system para decisión
- Sign-off signatures
- Post-deployment monitoring

**Cómo usar:**
```
1. 2-3 horas ANTES de publicar
2. Ejecutar cada fase completamente
3. Si algún item falla → parar y fix
4. Si ≥ 95% items OK → autorizar Go Live
5. Si < 95% → NO-GO, pausar
```

**Checklist:** 4 fases
- Fase 1: Code & Build (5 checks)
- Fase 2: Testing (20+ checks)
- Fase 3: Deployment (15+ checks)
- Fase 4: Go/No-Go (decisión final)

---

### 📄 [E2E-TESTING.md](./E2E-TESTING.md) — Flujos End-to-End Completos

**Para:** QA, Business Analysts, UAT testers  
**Contiene:** 9 flujos de negocio completos
- Flow 1: Admin setup inicial + Empleado ve datos
- Flow 2: Alerta de vencimiento
- Flow 3: Roadmap futuro
- Flow 4: Reportes & análisis
- Flow 5: Gestión de empleados
- Flow 6: Multi-role (cambio de permisos)
- Flow 7: Data integrity (CRUD consistency)
- Flow 8: Performance & load
- Flow 9: Error handling

**Cómo usar:**
```
1. Elegir un flow
2. Seguir pasos de A a Z
3. Verificar cada resultado esperado
4. Si algún paso falla, documentar
5. Llenar reporte E2E template
```

**Tiempo estimado:** 3-4 horas para todos los flows

---

## 🎯 Flujo de Testing Recomendado

```
┌─────────────────────────────────────┐
│ 1. Revisar TEST-DATA.md             │
│    (entender qué datos hay)         │
└────────────┬────────────────────────┘
             │
┌────────────▼────────────────────────┐
│ 2. Ejecutar TEST-CASES.md           │
│    - Smoke test (5 min)             │
│    - Functional test (1-2 hrs)      │
│    - Security test (30 min)         │
│    - Responsive test (30 min)       │
└────────────┬────────────────────────┘
             │
┌────────────▼────────────────────────┐
│ 3. Ejecutar E2E-TESTING.md          │
│    (9 flujos de negocio)            │
│    Duración: 2-3 horas              │
└────────────┬────────────────────────┘
             │
┌────────────▼────────────────────────┐
│ 4. Llenar PRE-DEPLOYMENT-CHECKLIST  │
│    (Go / No-Go decision)            │
│    Duración: 1 hora                 │
└────────────┬────────────────────────┘
             │
┌────────────▼────────────────────────┐
│ 5. DECISION                         │
│    [ ] ✅ GO LIVE                   │
│    [ ] ❌ NO-GO (fix & retry)       │
└─────────────────────────────────────┘

Total estimado: 5-7 horas
```

---

## 📊 Matriz por Rol

**¿Quién debería ejecutar qué?**

| Rol | TEST-CASES | TEST-DATA | E2E | PRE-CHECKLIST |
|-----|----------|-----------|-----|---------------|
| **QA Lead** | ✅ (dirigir) | ✅ | ✅ | ✅ (autorizar) |
| **Tester** | ✅ (ejecutar) | ✅ (referencia) | ✅ | Reportar |
| **Dev** | ⚠️ (smoke) | ✅ | ⚠️ | - |
| **Tech Lead** | ⚠️ (revisar) | ✅ | ⚠️ | ✅ (decidir) |
| **Project Owner** | - | - | - | ✅ (sign-off) |

---

## ⏱️ Timeline Sugerido

### **Antes del Deployment (1 semana)**

```
Día 1: Preparación
  [ ] Revisar documentación (1 hr)
  [ ] Preparar ambiente (30 min)

Día 2-3: Testing Funcional
  [ ] Ejecutar TEST-CASES.md (3 hrs)
  [ ] Documentar resultados

Día 4: Testing E2E
  [ ] Ejecutar E2E-TESTING.md (3-4 hrs)
  [ ] Documentar flows

Día 5: Pre-Deployment
  [ ] Ejecutar PRE-DEPLOYMENT-CHECKLIST (2 hrs)
  [ ] Decisión Go/No-Go
```

---

## ✅ Criterios de Aprobación

### Para Go Live

```
DEBE CUMPLIR:
  ✅ ≥ 95% test cases passed (TEST-CASES.md)
  ✅ Todos los E2E flows completados (E2E-TESTING.md)
  ✅ 0 blockers críticos encontrados
  ✅ PRE-DEPLOYMENT-CHECKLIST 100% completo
  ✅ Firmas de aprobación obtenidas

PUEDE TENER:
  ⚠️ Máximo 3 warnings menores (no blockers)
  ⚠️ Máximo 1-2 issues para próxima versión
```

---

## 🔧 Herramientas Necesarias

```
Para ejecutar tests:
  ✓ Navegador (Chrome, Firefox, Edge)
  ✓ DevTools (F12)
  ✓ Este directorio (archivos MD)
  ✓ Pen & paper (o doc para anotar)
  ✓ SharePoint (si testing live mode)

Opcional:
  ✓ Excel (para registrar resultados)
  ✓ Test management tool (si empresa tiene)
  ✓ Performance profiler
```

---

## 📈 Escalas de Severidad

Usar al documentar issues:

```
🔴 CRITICAL (Blocker)
   - App no carga
   - Data loss
   - Security vulnerability
   - User cannot do job
   → PAUSAR deployment

🟠 HIGH (Major)
   - Feature no funciona
   - Performance muy malo
   - Error en casos comunes
   → Fix antes de go live

🟡 MEDIUM (Moderate)
   - Minor UI issue
   - Feature funciona pero con glitch
   - Error en edge case
   → Fix en próxima versión

🟢 LOW (Minor)
   - Typo, cosmetic
   - Comportamiento raro pero no afecta
   → Fix cuando haya tiempo
```

---

## 🚀 Post-Deployment Testing

Después de publicar:

```
24-48 horas después:
  [ ] Usuarios pueden acceder sin errores 404/403
  [ ] No hay crashes en console
  [ ] Data se muestra correctamente
  [ ] Alertas funcionan (si live)
  [ ] Feedback inicial recolectado

Si critical issue:
  [ ] ROLLBACK inmediatamente (ver DEPLOYMENT.md)
  [ ] Investigar root cause
  [ ] Fix + test nuevamente
  [ ] Retry deployment
```

---

## 📝 Documentación Relacionada

```
TESTING (en esta carpeta):
  ├── TEST-CASES.md ...................... Cases por feature
  ├── TEST-DATA.md ....................... Datos consistentes
  ├── PRE-DEPLOYMENT-CHECKLIST.md ........ Go/No-Go decision
  └── E2E-TESTING.md ..................... Flujos end-to-end

DEPLOYMENT (en /docs):
  ├── DEPLOYMENT.md ...................... Cómo publicar
  ├── SHAREPOINT-SETUP.md ............... Setup SP lists
  └── POWER-AUTOMATE.md ................. Alertas automáticas
```

---

## 🤔 FAQ Testing

### P: ¿Cuánto tiempo toma testing completo?

**R:** 5-7 horas si lo haces bien
- Smoke: 5 min
- Functional: 2 hrs
- E2E: 3 hrs
- Pre-deployment: 1 hr

### P: ¿Qué pasa si encuentro un bug?

**R:** 
1. Documentar en reporte
2. Clasificar severidad
3. Si es blocker → NO-GO, fix y retry
4. Si es menor → documentar para v1.1

### P: ¿Necesito testing en demo Y live?

**R:** Idealmente sí (cubrirá diferencias SP)
- Demo: 2-3 hrs
- Live: 2-3 hrs
- Si tiempo limitado, priorizar live (producción)

### P: ¿Quién aprueba ir a producción?

**R:** 
- Tech Lead (+ QA Lead) → Aprueba testing
- Project Owner → Aprueba negocio
- Ambos firman PRE-DEPLOYMENT-CHECKLIST

---

## 📞 Contacto & Escalation

Si encuentras un issue crítico:

```
1. Documentar exactamente qué pasó
2. Tomar screenshot / video
3. Contactar Tech Lead
4. Si no se puede resolver → NO-GO
5. Fix → retry testing
```

---

## 🎯 Resumen Rápido

Para imprimir:

```
╔═══════════════════════════════════════════════╗
║ CertTrack IT — TESTING CHECKLIST              ║
╠═══════════════════════════════════════════════╣
║ 1. ✓ Revisar TEST-DATA.md                      ║
║ 2. ✓ Ejecutar TEST-CASES.md (demo)             ║
║ 3. ✓ Ejecutar TEST-CASES.md (live)             ║
║ 4. ✓ Ejecutar E2E-TESTING.md (9 flows)         ║
║ 5. ✓ Llenar PRE-DEPLOYMENT-CHECKLIST           ║
║                                               ║
║ Pass Rate: _____% (Goal: ≥95%)                ║
║ Decision: [ ] GO [ ] NO-GO                    ║
║ Aprobador: ________________ Fecha: ________    ║
╚═══════════════════════════════════════════════╝
```

---

**Última actualización:** 2026-05-04  
**Válido para:** v1.0+  
**Próxima revisión:** 2026-06-04
