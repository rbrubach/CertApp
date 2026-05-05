# E2E-TESTING.md — Testing End-to-End de Flujos Completos

**Propósito:** Validar flujos de negocio completos (no solo componentes)  
**Scope:** Demo + Live mode  
**Timeline:** 1-2 horas por flow  
**Audience:** QA, Testers, Business Analysts

---

## 📋 Flujos End-to-End

Los siguientes flujos representan procesos de negocio completos que un usuario (admin o empleado) haría en la app.

---

# FLOW 1: Admin crea catálogo inicial & empleado ve datos

**Actor Principal:** Admin  
**Actor Secundario:** Empleado  
**Duración:** 30 minutos  
**Ambiente:** Demo mode (simplificado) o Live (con SP)

## Pasos

### Fase 1: Admin Setup Inicial

```
1. Abrir app como ADMIN
   → Verificar: Header muestra "ADMIN"
   → Verificar: Sidebar muestra todas las opciones

2. Navegar a "Maestros" / Catálogos
   → Verificar: Ve opciones para crear Certificaciones, Entidades

3. (DEMO) Revisar que catálogo ya está cargado
   (LIVE) Crear 2-3 certificaciones masters:
     a. "AWS Certified Solutions Architect"
     b. "Azure Administrator Associate"
     c. "GCP Professional Cloud Architect"
   → Cada una requiere: Name, Vendor, Nivel, ValidezMeses
   → Verificar: Aparecen en lista

4. Crear 1-2 entidades certificadoras:
     a. "Amazon AWS"
     b. "Microsoft Azure"
   → Verificar: Aparecen con color correcto
```

### Fase 2: Admin Carga Datos Empleados

```
5. Navegar a "Empleados"
   (LIVE) Crear 2 empleados:
     - Nombre: "Ana Gómez", Email: ana@corp, Área: "Cloud"
     - Nombre: "Carlos Ruiz", Email: carlos@corp, Área: "Cloud"
   
   (DEMO) Revisar que empleados ya existen

6. Crear 1-2 certificaciones de Ana:
   → Click "Nueva Certificación"
   → Llenar:
      Certificación: "AWS Certified Solutions Architect"
      Asignado a: Ana Gómez
      Fecha Obtención: 2024-01-15
      Fecha Vencimiento: 2027-01-15
   → Guardar
   → Verificar: Alerta "ok"
   → Verificar: Aparece en tabla

7. Crear 1 certificación de Carlos:
   → Certificación: "Azure Administrator"
   → Asignado a: Carlos Ruiz
   → Fecha Vencimiento: 2026-06-30 (próximo mes - por vencer)
   → Guardar
```

### Fase 3: Empleado Accede

```
8. Logout como Admin
   (DEMO) Cambiar rol a "Empleado"
   (LIVE) Acceder como Ana Gómez (ana@corp)

9. Navegar a "Home"
   → Verificar: Stats solo muestran SUS certs
   → Si Ana tiene 1 cert: Total = 1

10. Navegar a "Mis Certificaciones"
    → Verificar: Ve solo su certificación (AWS)
    → Verificar: NO ve certificación de Carlos
    → Verificar: Estado "Vigente"

11. Navegar a "Skills"
    → Verificar: Aparece la certificación que tiene
    → Nivel: muestra correctamente
```

### Fase 4: Admin Verifica Visibilidad

```
12. Logout como Empleado, login como Admin

13. Navegar a "Todos los Certs"
    → Verificar: Ve ambas certificaciones (Ana + Carlos)
    → Filtrar por "Cloud"
    → Verificar: Siguen ambas (ambas area cloud)

14. Ir a "Reportes"
    → Exportar CSV
    → Verificar: Contiene ambos empleados
```

## Criterios de Éxito

```
✅ PASS si:
  - Admin ve todos los datos
  - Empleado ve solo SUS datos
  - Filtros funcionan
  - Export contiene datos correctos
  - Cambio de rol actualiza vistas
  - No hay data leakage (empleado no ve certs de otros)
```

---

# FLOW 2: Alerta de Vencimiento - Admin notificado

**Actor Principal:** Admin  
**Disparador:** Certificación próxima a vencer  
**Duración:** 15 minutos (demo), + 5 min espera (live con Power Automate)  
**Ambiente:** Demo (simulación) o Live (con email real)

## Pasos

### Fase 1: Setup (Admin)

```
1. Login como Admin

2. (LIVE ONLY) Verificar que Power Automate Flow está activo
   → Power Automate → Mis flujos → "CertTrack - 30 días antes"
   → Status: Habilitado

3. Crear certificación para empleado con vencimiento PRÓXIMO:
   → Nombre: "Test Alert Cert"
   → Asignado a: C002 (Demo Empleado)
   → Fecha Vencimiento: Hoy + 25 días (dentro de 30 días threshold)
   → Guardar

4. Navegar a "Alertas"
   → Verificar: Nueva alerta aparece para este certificado
   → Verificar: Status "Por Vencer" en badge
   → Días mostrados: ~25 días
```

### Fase 2: Admin Revisa Alerta (Demo)

```
5. (DEMO) Ver lista de alertas
   → Verificar: Muestra Empleado, Cert, Días, Botones
   → Click "Ver alerta"
   → Verificar: Modal muestra detalles

6. Cambiar estado:
   → Click "Resolver"
   → Confirmación: "¿Marcar como resuelta?"
   → Aceptar
   → Verificar: Alerta desaparece
   → Alerta "ok": "Alerta resuelta"
```

### Fase 3: Email Automático (Live)

```
7. (LIVE ONLY) Esperar a que Power Automate ejecute
   → Típicamente cada noche (11 PM) o en demo test manual
   → Ir a Power Automate → Flujos → flow name → Test
   → Ejecutar manualmente

8. Revisar inbox del Empleado (C002)
   → Debe llegar email con:
      Subject: "Certificación por Vencer"
      Body: Detalles de cert + días
      Link: Enlace a CertTrack (si existe)
   → Verificar: Formato correcto, sin encoding issues

9. Revisar inbox del Admin
   → Copia de email + admin instructions
   → Verificar: Admin notificado
```

### Fase 4: Empleado Toma Acción

```
10. (LIVE) Empleado recibe email
    → Click link o accede a app directamente
    → Navega a "Mis Certificaciones"
    → Ve la cert por vencer con badge naranja

11. Empleado puede:
    [ ] Option A: Hacer clic "Renovar" (si existe botón)
    [ ] Option B: Editar fecha (si se renovó ya)
    [ ] Option C: Solo revisar status
    
12. Cambiar fecha vencimiento a futuro:
    → Click "Editar"
    → Cambiar fecha a: Hoy + 180 días
    → Guardar
    → Verificar: Badge cambia a verde (vigente)
```

## Criterios de Éxito

```
✅ PASS si:
  - Alerta se crea cuando está en rango (< 30 días)
  - Alerta visible en página "Alertas"
  - Email se envía (si live)
  - Email contiene info correcta
  - Empleado puede actualizar fecha
  - Alerta se resuelve / desaparece
  - Stats se actualizan (vigentes sube)
```

---

# FLOW 3: Roadmap Futuro - Planificación de Certs

**Actor:** Admin  
**Duración:** 20 minutos  
**Ambiente:** Demo + Live

## Pasos

```
1. Login como Admin

2. Navegar a "Roadmap"
   → Verificar: Muestra certificaciones futuras

3. Crear nuevo roadmap:
   → Click "+ Nueva Cert Planeada"
   → Llenar:
      Certificación: "Google Professional Cloud Architect"
      Asignado a: C001 (Demo Admin / Juan García)
      Fecha Destino: 2026-09-30
      Estado: "En Curso"
   → Guardar

4. Verificar en tabla:
   → Aparece con estado "En Curso"
   → Ordenado por fecha

5. Cambiar estado a "Completado":
   → Click "Editar"
   → Cambiar Estado: "Completado"
   → Guardar

6. Resultado esperado:
   → Cambio persiste
   → Si hay stats, "Completadas" sube +1

7. (FUTURO) Cuando cert realmente se obtiene:
   → Ir a "Mis Certificaciones"
   → Crear entrada con misma cert
   → Linked to roadmap entry
```

---

# FLOW 4: Reportes & Análisis

**Actor:** Admin  
**Duración:** 15 minutos

## Pasos

```
1. Login como Admin

2. Navegar a "Reportes"
   → Verificar: Muestra dashboard con datos
   → Elementos esperados:
      - Distribución de certs por vendor (GCP, Azure, AWS)
      - Distribución por empleado
      - Timeline (próximas a vencer)

3. Filtros:
   → Click "Área": Cloud
   → Reportes se actualizan mostrando solo área Cloud

4. Exportar:
   → Click "Exportar CSV" o "Exportar PDF"
   → Archivo descarga
   → Verificar: Datos correctos

5. Compartir:
   → (FUTURO) Opción de compartir resultado
   → Enviar a stakeholders

6. Verificación:
   → Número de certs en reporte = número en BD
   → Usuarios mostrados correcto
   → Fechas formateadas correctamente
```

---

# FLOW 5: Gestión de Colaboradores (Admin)

**Actor:** Admin  
**Duración:** 20 minutos

## Pasos

```
1. Navegar a "Empleados"

2. Ver lista:
   → Tabla muestra: Nombre, Email, Área, Rol
   → Botones: Ver, Editar, Eliminar

3. Crear empleado:
   → Click "+ Nuevo Empleado"
   → Llenar:
      Nombre: "Sandra López"
      Email: sandra@corp
      Área: "Infraestructura"
      Rol: "Senior"
   → Guardar

4. Editar empleado:
   → Click "Editar" en Sandra
   → Cambiar Área: "Cloud"
   → Guardar
   → Verificar: Cambio refleja en tabla

5. Ver empleado:
   → Click "Ver" en Sandra
   → Modal muestra detalles
   → (FUTURO) Histórico de certificaciones
   → (FUTURO) Relaciones (jefes, reportes)

6. Búsqueda / Filtro:
   → Si existe search, buscar "Sandra"
   → Debe encontrar y filtrar
```

---

# FLOW 6: Multi-Role Scenario (Cambios de Permisos)

**Actor:** Admin, Empleado (cambio dinámico)  
**Duración:** 25 minutos

## Pasos

```
1. Login como Admin
   → Navegar a "Empleados"
   → Ver lista completa (5+ empleados)

2. Cambiar a Empleado (demo: cambiar rol en header)
   → (LIVE: logout, login como empleado)

3. Como Empleado:
   → Home muestra: Solo SUS certs
   → Sidebar: NO ve "Empleados", "Roadmap admin", "Reportes"
   → Click página admin → Error / No se carga

4. Cambiar de nuevo a Admin

5. Modificar certificación de empleado
   → Navegar a "Todos"
   → Buscar cert del empleado C002
   → Editar: Cambiar fecha vencimiento
   → Guardar

6. Cambiar a Empleado C002
   → Navegar a "Mis Certs"
   → Verificar: Ve el cambio que admin hizo
   → Confirma: Cambio visible en su vista

7. Ambos roles tienen acceso correcto
   → Admin: Total control
   → Empleado: Solo sus datos
```

---

# FLOW 7: Data Integrity - CRUD Consistency

**Actor:** Admin  
**Duración:** 30 minutos

## Pasos

```
1. Crear certificación:
   → ID: Test-001
   → Datos: [A, B, C]

2. Leer inmediatamente:
   → Datos deben ser [A, B, C]
   
3. Editar: Cambiar A→A'
   → Guardar
   → Leer de nuevo: debe ser [A', B, C]

4. Eliminar:
   → Confirmar eliminación
   → Buscar ID: Test-001
   → NO debe encontrar

5. (LIVE) Refresh página:
   → Asegurar: Datos persisten (en SP)
   → Deletado sigue deletado

6. Transacciones complejas:
   → Crear 3 certs para 1 empleado
   → Eliminar 1 cert
   → Editar 1 cert
   → Verificar que las 2 restantes están correctas
   → Stats se actualizan correctamente

7. Validación:
   → Total inicial: X
   → Después de operaciones: X + 3 - 1 = X + 2
   → Verificar estadísticas coinciden
```

---

# FLOW 8: Performance & Load

**Actor:** Tester  
**Duración:** 20 minutos

## Pasos

```
1. Crear muchos datos (si es posible):
   → 50+ certificaciones
   → 10+ empleados
   → 10+ roadmaps

2. Navegar entre páginas:
   → Verificar que no hay lag
   → Transiciones suaves
   → Tablas cargadas sin delay

3. Medir con DevTools:
   → Performance → Measure
   → Tiempo de carga de tabla: < 1 segundo
   → Cambio de rol: < 500ms

4. Scroll en tabla grande:
   → Debe ser smooth
   → Sin freezes

5. Export con datos grandes:
   → Click export
   → Debe completar en < 2 segundos

6. Resultado:
   → Si hay lag, documentar
   → Si datos muy grandes, considerar paginación en v2.0
```

---

# FLOW 9: Error Handling & Edge Cases

**Actor:** Tester  
**Duración:** 20 minutos

## Casos

```
1. Dates edge cases:
   [ ] Fecha vencimiento = Hoy
       → Debe mostrar "Vencida" o "Por Vencer"?
   [ ] Fecha vencimiento muy futura (2100)
       → Debe funcionar sin overflow
   [ ] Fecha obtención > Fecha vencimiento
       → Sistema debe rechazar o alertar

2. Empty states:
   [ ] Tabla sin datos → "Sin certificaciones"
   [ ] Empleado sin certs → stats muestra 0
   [ ] Filtro sin resultados → mensaje claro

3. Special characters:
   [ ] Nombre con ñ, acentos, caracteres especiales
   [ ] Email internacional
   [ ] Caracteres unicode
   → Verificar display correcto

4. Browser edge cases:
   [ ] Muy ancho (4K monitor)
   [ ] Muy angosto (muy móvil)
   [ ] Low internet (simular con DevTools throttle)
   → Verificar comportamiento

5. Concurrent edits (LIVE):
   [ ] Admin A edita cert
   [ ] Admin B edita MISMA cert
   [ ] Quién "gana"?
   [ ] (Nota: Típicamente último en guardar sobrescribe)
```

---

# CRITERIA GENERAL DE E2E

```
✅ PASS si:
  - Flow completo de principio a fin
  - Datos persisten (en memoria demo, en SP live)
  - No hay data loss
  - Permisos respetados
  - UI updates correctamente
  - Performance acceptable
  - Errores handled con mensajes claros
  
❌ FAIL si:
  - Algún paso rompe el flujo
  - Data loss ocurre
  - Empleado ve data de otros
  - Performance inaceptable (> 5 segundos)
  - Error crash sin recovery
```

---

## Reporte E2E Template

```
FLOW: [número y nombre]
Ambiente: [ ] Demo [ ] Live
Navegador: ________________
Resultado: [ ] PASS [ ] FAIL

Detalles:
  Paso 1: ✅
  Paso 2: ✅
  ...
  Paso N: ❌ [Error: _______________]

Root cause:
  ______________________________

Fix requerido:
  ______________________________

Blocker: [ ] Sí [ ] No
```

---

**Última actualización:** 2026-05-04  
**Duración total estimada:** 3-4 horas para todos los flows
