# GUIA-USUARIO-ADMIN.md — Manual para Administradores

**Para:** Administradores de CertTrack IT  
**Nivel:** Intermedio-Avanzado  
**Tiempo:** 20-30 minutos lectura  
**Última actualización:** 2026-05-04

---

## 📋 Tabla de Contenidos

1. [Acceso & Dashboard](#acceso--dashboard)
2. [Gestión de Certificaciones](#gestión-de-certificaciones)
3. [Gestión de Empleados](#gestión-de-empleados)
4. [Maestros & Catálogos](#maestros--catálogos)
5. [Roadmap Futuro](#roadmap-futuro)
6. [Alertas & Resolución](#alertas--resolución)
7. [Reportes & Análisis](#reportes--análisis)
8. [Tips & Best Practices](#tips--best-practices)
9. [FAQ Admin](#faq-admin)

---

## Acceso & Dashboard

### Cómo Acceder

```
1. Abrir SharePoint sitio de CertTrack
2. Navegar a página con la app
3. Buscar web part "CertTrack IT"
4. Click para abrir
5. Sistema detecta tu cuenta automáticamente
6. Si rol = admin, verás página Home admin
```

**¿Qué sucede si no eres admin?**
- Solo verás opciones de empleado
- Contacta con tu tech lead para agregar permisos

### Home Page - Dashboard Admin

**Elementos principales:**

```
┌─────────────────────────────────────────┐
│ CertTrack IT            [User Chip]     │
├─────────────────────────────────────────┤
│ Sidebar          │ Main Content         │
│ - Home (active)  │                      │
│ - Mis Certs      │  📊 ESTADÍSTICAS     │
│ - Alertas        │  ┌──────┬──────┐    │
│ - Roadmap        │  │Total │Vigent│    │
│ - Todos          │  │ 124  │  98  │    │
│ - Empleados      │  └──────┴──────┘    │
│ - Skills         │  ┌──────┬──────┐    │
│ - Budget         │  │P.Venc│Vencid│    │
│ - Reportes       │  │ 18   │  8   │    │
│ - Maestros       │  └──────┴──────┘    │
│                  │                      │
│                  │ 📅 PRÓXIMOS 30 DÍAS  │
│                  │ (gráfico)            │
│                  │                      │
│                  │ 👥 EMPLEADOS ACTIVOS │
│                  │ (lista corta)        │
└─────────────────────────────────────────┘
```

**Qué significan los números:**

| Métrica | Definición |
|---------|-----------|
| **Total** | Certificaciones registradas en el sistema |
| **Vigentes** | Certificaciones con > 30 días para vencer |
| **Por Vencer** | Certificaciones con 0-30 días |
| **Vencidas** | Certificaciones con < 0 días (expiradas) |

---

## Gestión de Certificaciones

### Ver Todas las Certificaciones

```
Pasos:
1. Sidebar → Click "Todos los Certs"
2. Tabla muestra: Empleado, Cert, Entidad, Vencimiento, Estado
3. Puedes:
   - Ordenar por columna (click encabezado)
   - Filtrar por Área (dropdown)
   - Buscar (barra search si existe)
4. Cada fila tiene botones: Ver, Editar, Eliminar
```

### Crear Nueva Certificación

**Caso de uso:** Empleado obtuvo nueva certificación, registrarla.

```
Pasos:
1. Click "+ Nueva Certificación"
   → Modal se abre

2. Llenar formulario:
   ┌─────────────────────────────────┐
   │ Nombre: "AWS Solutions Arch"    │
   │ Certificación: [dropdown] AWS   │
   │ Entidad: [dropdown] Amazon      │
   │ Asignado a: [dropdown] Juan     │
   │ Fecha Obtención: 2026-05-01     │
   │ Fecha Vencimiento: 2029-05-01   │
   │ [Guardar] [Cancelar]            │
   └─────────────────────────────────┘

3. Click "Guardar"
   → Alerta verde: "Certificación creada"
   → Modal cierra
   → Aparece en tabla automáticamente
```

**Tips:**
- Fecha vencimiento = fecha obtención + validez del cert
  - Ej: AWS 3 años → 2026-05-01 + 36 meses = 2029-05-01
- Si no sabes fecha exacta, usar día 1 del mes
- Mejor ser exacto que aproximado

### Editar Certificación

**Caso de uso:** Empleado renovó cert, actualizar fecha.

```
Pasos:
1. Tabla → Click fila o botón "Editar"
2. Modal abre con datos actual
3. Cambiar campo que necesita:
   - Fecha Vencimiento: 2026-05-01 → 2029-05-01
   - Entidad: Cambiar si fue error
   - Etc.
4. Click "Guardar"
5. Alerta: "Certificación actualizada"
```

### Eliminar Certificación

**Caso de uso:** Certificación registrada por error, eliminarla.

```
Pasos:
1. Tabla → Click botón "Eliminar"
2. Confirmación: "¿Eliminar esta certificación?"
3. Click "Sí, eliminar"
4. Alerta: "Certificación eliminada"
5. Desaparece de tabla inmediatamente
```

**Cuidado:** Acción irreversible (en demo, datos se pierden al refresh).

---

## Gestión de Empleados

### Ver Empleados

```
Pasos:
1. Sidebar → "Empleados"
2. Tabla muestra: Nombre, Email, Área, Rol
3. Botones: Ver, Editar
```

### Crear Empleado

**Caso de uso:** Nuevo empleado en equipo, registrarlo.

```
Pasos:
1. Click "+ Nuevo Empleado"
2. Llenar:
   - Nombre: "Pablo Mendez"
   - Email: pablo@corp
   - Área: [dropdown] Cloud
   - Rol: [dropdown] Senior
3. Click "Guardar"
```

### Editar Datos Empleado

```
Pasos:
1. Click "Editar" en fila
2. Cambiar lo que sea necesario
3. Guardar
```

---

## Maestros & Catálogos

### Qué son Maestros

Datos de referencia que usa la app:
- **Certificaciones:** Catálogo de todas las posibles certificaciones (GCP, Azure, AWS, etc.)
- **Entidades:** Organizaciones que emiten certificaciones (Google, Microsoft, Amazon, etc.)

**Estos datos son compartidos:** Todos los empleados ven el mismo catálogo.

### Gestionar Certificaciones Master

```
Pasos:
1. Sidebar → "Maestros"
2. Click tab "Certificaciones"
3. Tabla: Nombre, Vendor, Nivel, Validez (meses)
4. Acciones: Ver, Editar, Eliminar
```

**Crear Certificación Master:**

```
Click "+ Nueva Cert Master"
Nombre: "Kubernetes Administration"
Vendor: "Otro"
Nivel: "Professional"
Validez: 36 meses
Guardar
```

**Ejemplos de master certs:**

| Nombre | Vendor | Nivel | Meses |
|--------|--------|-------|-------|
| GCP Associate Cloud Engineer | GCP | Associate | 36 |
| Azure Solutions Architect | Azure | Expert | 36 |
| AWS Solutions Architect | AWS | Professional | 36 |
| Scrum Master | Otro | Professional | 36 |
| Kubernetes Admin | Otro | Professional | 36 |

### Gestionar Entidades

```
Pasos:
1. Sidebar → "Maestros"
2. Click tab "Entidades"
3. Tabla: Nombre, Color
4. Acciones: Crear, Editar, Eliminar
```

**Por qué colores?**
Para visualizar rápidamente en gráficos:
- Google = Rojo
- Microsoft = Azul
- Amazon = Naranja
- Etc.

---

## Roadmap Futuro

### Qué es Roadmap

Plan de certificaciones que empleados van a obtener en futuro.

**Ejemplo:**
```
Juan: "Quiero obtener AWS Solutions Architect"
      Fecha destino: 2026-12-31
      Estado: En Curso

María: "Planificada GCP Professional"
       Fecha destino: 2027-06-30
       Estado: Planeado
```

### Ver Roadmap

```
Pasos:
1. Sidebar → "Roadmap"
2. Tabla: Empleado, Cert, Fecha Destino, Estado
3. Filtrar por estado (En Curso, Planeado, Completado)
```

### Crear Roadmap

```
Click "+ Nuevo Plan"
Certificación: "Azure Solutions Architect"
Asignado a: "María López"
Fecha Destino: "2026-09-30"
Estado: "Planeado"
Guardar
```

### Actualizar Roadmap (Cuando se completa)

```
1. Click "Editar" en fila
2. Cambiar Estado: "Completado"
3. Guardar
4. Cuando se complete realmente:
   → Crear entrada en "Mis Certificaciones"
   → Con misma certificación
```

---

## Alertas & Resolución

### Qué son Alertas

Notificaciones automáticas de certificaciones próximas a vencer.

**Configuración:**
- Alertan 30 días ANTES del vencimiento
- Se envían emails automáticos (Power Automate)
- Admin puede resolver alertas manualmente

### Ver Alertas

```
Pasos:
1. Sidebar → "Alertas"
2. Tabla: Empleado, Cert, Días, Estado, Acciones
3. Orden: Por urgencia (rojo primero = más urgentes)

Estados:
  🟢 Verde  = Vigente (no alerta)
  🟠 Naranja = Por vencer (alerta activa)
  🔴 Rojo   = Vencida (alerta crítica)
```

### Resolver Alerta

**Caso de uso:** Juan renovó su AWS, marcar alerta como resuelta.

```
Pasos:
1. Tabla → Click botón "Resolver" en fila
2. Confirmación: "¿Marcar como resuelta?"
3. Click "Sí"
4. Alerta desaparece
```

### Seguimiento Manual

```
Si alerta sigue sin resolverse:
1. Contactar empleado
2. Verificar si renovó o va a renovar
3. Si renovó → Actualizar fecha en "Mis Certs"
4. Resolver alerta después
```

---

## Reportes & Análisis

### Dashboard Reportes

Acceder: Sidebar → "Reportes"

**Elementos:**

```
1. Distribución por Vendor
   - Gráfico pie: % de certs por (GCP, Azure, AWS, etc.)

2. Distribución por Empleado
   - Gráfico barras: Cuántas certs tiene cada empleado

3. Timeline Próximos 30 días
   - Línea tiempo: Qué vence cuándo

4. Resumen por Área
   - Tabla: Cloud (98), Infra (45), DB (32)
```

### Filtrar Reportes

```
Dropdown "Filtrar por Área":
- Cloud
- Infraestructura
- Bases de Datos
- Otro

Al seleccionar → Todos los gráficos se actualizan
```

### Exportar Reportes

```
Pasos:
1. Preparar reporte (aplicar filtros si necesario)
2. Click botón "Exportar CSV"
3. Descarga automática: "reporte_certificaciones.csv"
4. Abrir en Excel / Sheets
5. Usar para:
   - Reportes ejecutivos
   - Análisis interno
   - Shares con stakeholders
```

**Columnas CSV:**

```
Empleado | Email | Área | Certificación | Vendor | Vencimiento | Estado | Días
```

---

## Tips & Best Practices

### ✅ Hacer

```
1. Actualizar certificaciones LO ANTES POSIBLE
   - Cuando empleado obtiene cert, registrar mismo mes
   - No esperar a vencer para descubrir que falta data

2. Usar nombres consistentes
   - "AWS Solutions Architect" (no "AWS Solutions Arch.")
   - "Azure Administrator Associate" (no "Azure Admin")

3. Revisar alertas semanalmente
   - Proactivo: detectar issues antes de vencer
   - Contactar empleados si alerta y no han renovado

4. Mantener maestros de datos limpios
   - Si agregaste cert master por error, eliminar
   - Si hay duplicados, consolidar

5. Hacer backup de reportes mensualmente
   - Descargar CSV último día de mes
   - Guardar en carpeta corporativa
   - Para auditoría posterior
```

### ❌ NO hacer

```
1. NO usar fechas aproximadas
   - "Vencimiento: Junio 2026" (inexacto)
   - ✓ "Vencimiento: 2026-06-30" (exacto)

2. NO olvidar actualizar cuando empleado renueva
   - Sin actualizar → Alertas false positives
   - → Pérdida de confianza en sistema

3. NO eliminar certificaciones vigentes
   - A menos que sea error comprobado
   - Mejor: marcar como "Cancelada" (si futura versión lo permite)

4. NO crear duplicados de maestros
   - Si "Azure Administrator" existe → NO crear "Azure Admin"
   - Causa confusión en filtros y reportes

5. NO compartir datos sin autorización
   - Si necesitas compartir reporte → verificar permisos primero
```

---

## FAQ Admin

### P: ¿Cuánta tiempo toma agregar un certificado?

**R:** 1-2 minutos por certificado.
- Abierto modal
- Llenar 5 campos
- Click guardar
- Listo

### P: ¿Qué pasa si me equivoco en fecha?

**R:** Puedes editar después.
```
1. Click fila
2. Editar fecha
3. Guardar
4. Si ya pasó la alerta, puedes resolver manualmente
```

### P: ¿Cómo contacto a empleado para que renueve cert?

**R:** 
- Opción 1: Revisar email en tabla "Empleados"
- Opción 2: Usar directorio interno (Teams, email)
- Opción 3: Contacta su manager

### P: ¿Puedo crear categorías/áreas propias?

**R:** En versión actual, están predefinidas:
- Cloud
- Infraestructura
- Bases de Datos
- Otro

Si necesitas agregar, contacta tech lead.

### P: ¿Hay límite de certificaciones por empleado?

**R:** No. Sistema soporta 100+ sin problemas.

### P: ¿Qué pasa si dato maestro fue usado por error?

**R:**
```
Si "Azure Admin" (error) → crea reportes confusos

Solución:
1. Cambiar todas las certs de "Azure Admin" → "Azure Administrator"
2. Eliminar maestro "Azure Admin"

O contactar tech lead para batch update.
```

### P: ¿Puedo generar reporte para auditoría?

**R:** Sí, exportar CSV.
```
Reportes → Exportar CSV
Contiene: Todas las certs, empleados, fechas, estados
```

### P: ¿Es seguro compartir reporte con CEO?

**R:** Sí, si contiene:
- Resumen por departamento (no nombres individuales)
- Estadísticas agregadas
- Tendencias

Para datos individuales → Verificar permisos primero.

---

## Contacto & Soporte

Si tienes problemas:

```
1. Revisar FAQ-TROUBLESHOOTING.md
2. Si problema persiste:
   - Contacta: tech-lead@nttdata.com
   - Describe: Qué hiciste + Error exacto
   - Adjunta: Screenshot (F12 → Console)
```

---

**Última actualización:** 2026-05-04  
**Para preguntas:** Ver FAQ-TROUBLESHOOTING.md
