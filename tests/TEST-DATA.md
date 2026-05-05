# TEST-DATA.md — Datos para Testing

**Propósito:** Especificar datos consistentes para testing reproducible  
**Audience:** QA, Testers, Developers  
**Última actualización:** 2026-05-04

---

## 📋 Demo Data (Hardcoded en certtrack-it-full.html)

### Colaboradores (Empleados)

```javascript
const COLABORADORES = [
  { id: 'C001', name: 'Demo Admin', email: 'admin@demo.local', area: 'Cloud', rol: 'Lead' },
  { id: 'C002', name: 'Demo Empleado', email: 'empleado@demo.local', area: 'Cloud', rol: 'Senior' },
  { id: 'C003', name: 'Juan García', email: 'juan@demo.local', area: 'Infraestructura', rol: 'Junior' },
  { id: 'C004', name: 'María López', email: 'maria@demo.local', area: 'Bases de Datos', rol: 'Senior' },
];
```

| ID | Nombre | Email | Área | Rol |
|----|--------|-------|------|-----|
| C001 | Demo Admin | admin@demo.local | Cloud | Lead |
| C002 | Demo Empleado | empleado@demo.local | Cloud | Senior |
| C003 | Juan García | juan@demo.local | Infraestructura | Junior |
| C004 | María López | maria@demo.local | Bases de Datos | Senior |

**Uso:** 
- C001/C002: Para testing UI (cambiar rol en header)
- C003/C004: Para testing de permisos (items específicos)

---

### Certificaciones Master (Catálogo)

```javascript
const MASTER_CERTS = [
  { id: 'MC1', name: 'Google Associate Cloud Engineer', vendor: 'GCP', nivel: 'Associate', validezMeses: 36 },
  { id: 'MC2', name: 'Azure Solutions Architect Expert', vendor: 'Azure', nivel: 'Expert', validezMeses: 36 },
  { id: 'MC3', name: 'AWS Solutions Architect Professional', vendor: 'AWS', nivel: 'Professional', validezMeses: 36 },
  { id: 'MC4', name: 'Oracle Database Administrator', vendor: 'DB', nivel: 'Professional', validezMeses: 60 },
  { id: 'MC5', name: 'Scrum Master Certified', vendor: 'Otro', nivel: 'Professional', validezMeses: 36 },
];
```

| ID | Nombre | Vendor | Nivel | Validez |
|----|--------|--------|-------|---------|
| MC1 | Google Associate Cloud Engineer | GCP | Associate | 36 meses |
| MC2 | Azure Solutions Architect Expert | Azure | Expert | 36 meses |
| MC3 | AWS Solutions Architect Professional | AWS | Professional | 36 meses |
| MC4 | Oracle Database Administrator | DB | Professional | 60 meses |
| MC5 | Scrum Master Certified | Otro | Professional | 36 meses |

---

### Entidades (Certificadores)

```javascript
const MASTER_ENTITIES = [
  { id: 'E1', name: 'Google Cloud', color: '#ea4335' },
  { id: 'E2', name: 'Microsoft Azure', color: '#0078d4' },
  { id: 'E3', name: 'Amazon AWS', color: '#ff9900' },
  { id: 'E4', name: 'Oracle Database', color: '#ff3621' },
  { id: 'E5', name: 'Scrum Alliance', color: '#7b42bc' },
];
```

---

### Certificaciones (Registros por Empleado)

Distribuidas así para testing de estados:

```javascript
const CERTS = [
  // C001 - Demo Admin
  { id: '1', colabId: 'C001', name: 'Google Associate Cloud Engineer', 
    vendor: 'GCP', entidad: 'Google Cloud', 
    fechaObtención: '2023-05-15', fechaVencimiento: '2026-05-15', // VIGENTE (90+ días)
    estado: 'Vigente', docUrl: null },
    
  { id: '2', colabId: 'C001', name: 'Azure Solutions Architect Expert',
    vendor: 'Azure', entidad: 'Microsoft Azure',
    fechaObtención: '2022-11-20', fechaVencimiento: '2025-11-20', // VIGENTE (180+ días)
    estado: 'Vigente', docUrl: null },

  // C002 - Demo Empleado
  { id: '3', colabId: 'C002', name: 'AWS Solutions Architect Professional',
    vendor: 'AWS', entidad: 'Amazon AWS',
    fechaObtención: '2023-03-10', fechaVencimiento: '2026-05-30', // POR VENCER (25 días)
    estado: 'Por Vencer', docUrl: null },

  // C003 - Juan García
  { id: '4', colabId: 'C003', name: 'Oracle Database Administrator',
    vendor: 'DB', entidad: 'Oracle Database',
    fechaObtención: '2023-08-01', fechaVencimiento: '2026-05-20', // POR VENCER (15 días)
    estado: 'Por Vencer', docUrl: null },

  // C004 - María López
  { id: '5', colabId: 'C004', name: 'Scrum Master Certified',
    vendor: 'Otro', entidad: 'Scrum Alliance',
    fechaObtención: '2023-01-15', fechaVencimiento: '2025-04-24', // VENCIDA (-10 días)
    estado: 'Vencida', docUrl: null },
];
```

**Estados para Testing:**

| ID | Empleado | Certificación | Días Restantes | Estado | Uso |
|----|----------|---------------|----------------|--------|-----|
| 1 | C001 | GCP | +90 | Vigente | Test de cert sana |
| 2 | C001 | Azure | +180 | Vigente | Test de cert sana |
| 3 | C002 | AWS | +25 | Por Vencer | Test de alerta próxima |
| 4 | C003 | Oracle | +15 | Por Vencer | Test urgente |
| 5 | C004 | Scrum | -10 | Vencida | Test de cert vencida |

---

### Roadmap

```javascript
const ROADMAP = [
  { id: 'R1', colabId: 'C001', name: 'Google Professional Cloud Architect', 
    fechaDestino: '2026-08-31', estado: 'En Curso' },
  
  { id: 'R2', colabId: 'C002', name: 'Kubernetes Administration',
    fechaDestino: '2026-07-15', estado: 'Planeado' },

  { id: 'R3', colabId: 'C003', name: 'AWS Certified DevOps Engineer',
    fechaDestino: '2026-12-31', estado: 'Planeado' },
];
```

---

## 🧪 Test Cases por Escenario

### Escenario 1: Primer Login (Verificar Carga)

**Datos esperados:**

```
Home page muestra:
- Total: 5 certs
- Vigentes: 2
- Por Vencer: 2  
- Vencidas: 1

Lista "Mis Certs" muestra según rol:
- Admin: ve los 5
- Empleado (C002): ve solo sus 1 cert
```

### Escenario 2: Testing de Estados

**Test cada estado:**

```
1. Vigente (cert 1, 2):
   - Badge verde
   - Texto: "Vigente"
   - Días: +90, +180
   
2. Por Vencer (cert 3, 4):
   - Badge naranja
   - Texto: "Por Vencer"
   - Días: +25, +15
   - Debe generar alerta
   
3. Vencida (cert 5):
   - Badge rojo
   - Texto: "Vencida"
   - Días: -10
```

### Escenario 3: Testing de CRUD

**Al crear nuevo:**

```
Usar datos:
- Nombre: "Test Cert 001" (o incrementar número)
- Vendor: GCP / Azure / AWS (rotar)
- Empleado: C001, C002, C003, C004 (distribuir)
- Fecha Vencimiento: Hoy + 30 días (estándar)

Esperado:
- Aparece en tabla
- Stats se actualizan (+1 total)
- Si fue vencido, stats de "vencidas" suben
```

**Al editar:**

```
Cambiar un campo y guardar
- Verificar que tabla se actualiza
- Stats recalculan si cambió fecha
```

**Al eliminar:**

```
Total baja -1
Stats se actualizan si era de categoría especial
```

### Escenario 4: Testing de Permisos (Live Mode)

**Admin:**
```
Debe ver:
- Cert ID 1 (C001)
- Cert ID 2 (C001)
- Cert ID 3 (C002)
- Cert ID 4 (C003)
- Cert ID 5 (C004)
Total: 5
```

**Empleado (C002):**
```
Debe ver:
- Cert ID 3 (solo la suya)
Total: 1
```

---

## 📊 Datos Live Mode (SharePoint)

### Schema Listas SP

Idéntico a demo, pero persistente. Ver [SHAREPOINT-SETUP.md](../docs/SHAREPOINT-SETUP.md) para schema completo.

### Datos Iniciales Sugeridos

**Para testing en live:**

```
1. Cargar los mismos datos demo a SP
   → Garantiza consistencia entre demo y live

2. Agregar 1-2 items adicionales en SP
   → Para test de "datos que no existían en demo"

3. Crear certificaciones "en curso"
   → Para test de workflow completo (crear → aprobar → renovar)
```

---

## 🔄 Gestión de Datos Durante Testing

### Antes de Cada Sesión

```
[ ] Restaurar datos demo a estado original
    (Si modificaste datos, hacer reset)

[ ] Verificar que todas las fecha estén correctas
    (Si pasaron días, actualizar "hoy + X días")

[ ] Limpiar registros de prueba
    (Eliminar test-cert-001, test-cert-002, etc.)

[ ] Verificar que no hay datos huérfanos
    (Certificados sin empleado asociado)
```

### Cleanup Post-Testing

```
[ ] Eliminar todas las certificaciones creadas
[ ] Eliminar roadmaps de prueba
[ ] Resetear datos a valores conocidos
[ ] Documentar qué se cambió (si algo)
```

---

## 📝 Checklist de Datos

```
DEMO MODE:
[ ] 4 colaboradores con roles distintos
[ ] 5 certificaciones con estados variados
[ ] 3 roadmaps planeados
[ ] 5 entidades diferentes
[ ] Datos suficientes para mostrar listas no-vacías

LIVE MODE:
[ ] SharePoint lists creadas
[ ] Datos maestros cargados
[ ] Permiso item-level configurado
[ ] Test data cargada en SP
[ ] Conexión REST API funciona
```

---

## 🧮 Cálculos Verificación (Fórmulas)

### Estadísticas Home

```
Total = Count(CERTS)
      = 5

Vigentes = Count(CERTS where días_restantes > 30)
         = 2 (ID 1, 2)

Por Vencer = Count(CERTS where 0 < días_restantes <= 30)
           = 2 (ID 3, 4)

Vencidas = Count(CERTS where días_restantes <= 0)
         = 1 (ID 5)

Verificación: 2 + 2 + 1 = 5 ✓
```

### Por Empleado (C002)

```
C002 tiene:
- ID 3 (AWS, por vencer)

Count = 1
Vigentes = 0
Por Vencer = 1
Vencidas = 0
```

---

## 🚀 Evolución de Test Data

### v1.0 (Actual)
- 4 empleados
- 5 certificaciones
- 3 roadmaps
- 5 entidades

### v1.1 (Futuro)
- Agregar 10+ empleados
- Agregar datos por departamento
- Agregar histórico de cambios

### v2.0
- Datos realistas de producción
- Testing con datasets grandes (100+ certs)
- Perfil de performance

---

**Generado:** 2026-05-04  
**Próxima actualización:** 2026-06-04
