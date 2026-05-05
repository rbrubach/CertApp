# SHAREPOINT-SETUP.md — Crear Listas y Configurar Permisos

**Prerequisito:** Acceso de Owner a sitio SharePoint Online  
**Timeline:** 30-45 min  
**Objetivo:** Crear estructura de datos para modo `live`

---

## 📋 Listas Necesarias

### 1. Certificaciones

**Propósito:** Registro de certificaciones por empleado

**Columnas:**

| Nombre | Tipo | Obligatorio | Notas |
|--------|------|-------------|-------|
| Title | Text | ✓ | Nombre certificación |
| colabId | Text | ✓ | ID del colaborador |
| Certificacion | Choice | ✓ | Valores: [GCP, Azure, AWS, DB, Otros] |
| Entidad | Text | ✓ | Ej: "Google", "Microsoft" |
| FechaVencimiento | Date | ✓ | Formato: yyyy-MM-dd |
| FechaObtención | Date | ✓ | Cuando se obtuvo |
| docUrl | URL | | Link a badge/PDF |
| Estado | Calculated | | Fórmula: `IF(TODAY()<[FechaVencimiento],"Vigente","Vencida")` |
| DíasParaVencer | Calculated | | Fórmula: `DATEDIF(TODAY(),[FechaVencimiento],"D")` |

**Crear:**
```
SharePoint → Create new List → Blank list
Name: Certificaciones
Add columns según tabla ↑
```

---

### 2. Colaboradores

**Propósito:** Master de empleados

**Columnas:**

| Nombre | Tipo | Obligatorio | Notas |
|--------|------|-------------|-------|
| Title | Text | ✓ | Nombre completo |
| email | Text | ✓ | Email corporativo |
| id | Text | ✓ | ID único (ej: "C001") |
| area | Choice | ✓ | [Cloud, Infraestructura, Bases de Datos, Otro] |
| rol | Choice | | [Senior, Junior, Lead] |
| manager | Person | | Jefe directo |

**Crear:**
```
SharePoint → Create new List → Blank list
Name: Colaboradores
Add columns según tabla ↑
```

---

### 3. MaestroCertificaciones

**Propósito:** Catálogo de certificaciones disponibles

**Columnas:**

| Nombre | Tipo | Obligatorio | Notas |
|--------|------|-------------|-------|
| Title | Text | ✓ | Ej: "Google Associate Cloud Engineer" |
| vendor | Choice | ✓ | [GCP, Azure, AWS, DB, GGL, HC, Otro] |
| nivel | Choice | | [Associate, Professional, Expert] |
| validezMeses | Number | | Meses que dura el certificado (ej: 36) |

**Crear:**
```
SharePoint → Create new List → Blank list
Name: MaestroCertificaciones
Add columns según tabla ↑
```

**Datos iniciales:**

```
Title                          | vendor | nivel       | validezMeses
Google Associate Cloud Eng     | GCP    | Associate   | 36
Azure Solutions Architect      | Azure  | Expert      | 36
AWS Solutions Architect        | AWS    | Professional| 36
Oracle Database Administrator  | DB     | Professional| 60
Scrum Master                   | Otro   | Professional| 36
```

---

### 4. MaestroEntidades

**Propósito:** Certificadores/entidades emisoras

**Columnas:**

| Nombre | Tipo | Obligatorio | Notas |
|--------|------|-------------|-------|
| Title | Text | ✓ | Ej: "Google Cloud" |
| color | Text | | Hex color (ej: #ea4335) |

**Crear:**
```
SharePoint → Create new List → Blank list
Name: MaestroEntidades
Add columns según tabla ↑
```

**Datos iniciales:**

```
Title                | color
Google Cloud         | #ea4335
Microsoft Azure      | #0078d4
Amazon AWS           | #ff9900
Oracle Database      | #ff3621
Google (General)     | #4285f4
HashiCorp            | #7b42bc
Otra Entidad         | #666666
```

---

### 5. Roadmap

**Propósito:** Plan de certificaciones futuras

**Columnas:**

| Nombre | Tipo | Obligatorio | Notas |
|--------|------|-------------|-------|
| Title | Text | ✓ | Nombre certificación planeada |
| colabId | Text | ✓ | ID colaborador |
| FechaDestino | Date | ✓ | Fecha objetivo |
| Certificacion | Text | | Nombre tecnico |
| estado | Choice | | [Planeado, En Curso, Completado, Cancelado] |

**Crear:**
```
SharePoint → Create new List → Blank list
Name: Roadmap
Add columns según tabla ↑
```

---

## 🔐 Configurar Permisos

### Paso 1: Crear Grupos Azure AD

**Abre:** Azure AD → Groups → Create group

#### Grupo 1: CertTrack_Admins

```
Name: CertTrack_Admins
Description: Administradores de CertTrack
Members: Tu usuario + otros admins
```

#### Grupo 2: CertTrack_Empleados

```
Name: CertTrack_Empleados
Description: Empleados que usan CertTrack
Members: Todos los empleados
```

---

### Paso 2: Asignar Permisos en Sitio SharePoint

**En el sitio de CertTrack:**

```
Settings → Site permissions → Share site

Add group: CertTrack_Admins  → Permission: Owner
Add group: CertTrack_Empleados → Permission: Edit (o Custom - Read + Edit own)
```

---

### Paso 3: Configurar Item-Level Permissions en Listas

#### Lista: Certificaciones

```
Settings → List settings → Permissions for this list

☑ Limit user permissions to their own items
  → Los empleados solo ven SUS certificados
  → Los admins ven todos
```

#### Cómo verificar que funciona:

```
1. Login como Admin → debe ver TODOS los certificados
2. Login como Empleado → debe ver SOLO los asignados a él
3. Si algo no funciona:
   - Revisar que usuario está en grupo correcto
   - Revisar que certificado tiene "colabId" del empleado
```

---

### Paso 4: Vistas de Lista (Opcional, mejora UX)

En cada lista, crear vistas para filtros comunes:

#### Certificaciones - Vista: "Por Vencer"

```
List → Create view → Filter
[FechaVencimiento] is within next 30 days
Sort by: FechaVencimiento (ascending)
```

#### Certificaciones - Vista: "Vencidas"

```
List → Create view → Filter
[FechaVencimiento] is before [Today]
Sort by: FechaVencimiento (descending)
```

#### Roadmap - Vista: "En Curso"

```
List → Create view → Filter
[estado] = "En Curso"
Sort by: FechaDestino (ascending)
```

---

## ✅ Checklist Post-Setup

```
[ ] Certificaciones lista creada
[ ] Colaboradores lista creada
[ ] MaestroCertificaciones lista creada
[ ] MaestroEntidades lista creada
[ ] Roadmap lista creada

[ ] Grupos Azure AD creados (CertTrack_Admins, CertTrack_Empleados)
[ ] Grupos asignados al sitio con permisos correctos
[ ] Item-level permissions habilitadas en Certificaciones
[ ] Datos maestros ingresados (certificaciones, entidades)

[ ] Test: Admin accede, ve todos los certificados
[ ] Test: Empleado accede, ve solo sus certificados
[ ] Test: REST API es accesible (abrir DevTools, ver network)
```

---

## 🔧 Troubleshooting

### P: Item-level permissions no funciona

```
1. Verificar que está HABILITADA en List settings
2. Verificar que usuario está en grupo CORRECTO
3. Crear vista con filter [colabId] = [Me]
4. Esperar 5 min (caché de SP)
```

### P: Usuarios no pueden ver listas

```
1. Verificar que grupo está en sitio permissions
2. Verificar que usuario está EN el grupo (Azure AD)
3. Usuario debe hacer Sign out / Sign in
4. Limpiar caché navegador (Ctrl+Shift+Del)
```

### P: No puedo agregar columnas calculadas

```
1. La columna debe ser Text o Number (no Choice)
2. Si es Calculated, verificar sintaxis fórmula
3. SharePoint no permite algunas funciones (ej: CUSTOM functions)
```

---

## 📊 Cargar Datos Iniciales

### Opción 1: Manual (UI SharePoint)

```
1. Ir a lista Colaboradores
2. Click "New"
3. Llenar manualmente
4. Repeat para todas las listas
```

### Opción 2: Bulk Upload (CSV)

```
1. Preparar CSV con columnas correctas
2. List → Import → Choose file → Upload
3. SharePoint mapea columnas automáticamente
```

### Opción 3: Power Automate (Avanzado)

```
1. Crear flow: Cloud flows → Automated
2. Trigger: When a file is created (en carpeta)
3. Action: Parse JSON
4. Action: Create item (en lista correcta)
5. Repetir para cada lista
```

---

## 🚀 Siguiente Paso

Una vez completado este setup, volver a [DEPLOYMENT.md → Scenario B: Deployment en Producción](./DEPLOYMENT.md#-scenario-b-deployment-en-producción-live-mode) para:

1. Modificar CFG.mode a `'live'`
2. Crear web part
3. Hacer testing

---

**Última actualización:** 2026-05-04
