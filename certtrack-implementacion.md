# CertTrack IT — Guía de Implementación

Documento técnico completo para configurar CertTrack IT en SharePoint Online + Power Automate. Cubre listas, maestros, permisos, campos de colaboradores y los 3 flujos de automatización de emails.

---

## Tabla de contenidos

1. [Overview](#overview)
2. [Datos de Colaboradores](#datos-de-colaboradores)
3. [Listas de SharePoint](#listas-de-sharepoint)
4. [Maestros en SP](#maestros-en-sp)
5. [Grupos y Permisos](#grupos-y-permisos)
6. [Power Automate — Introducción](#power-automate--introducción)
7. [Flow 1 — Alerta 30 Días](#flow-1--alerta-30-días)
8. [Flow 2 — Certificación Vencida](#flow-2--certificación-vencida)
9. [Flow 3 — Resumen Semanal](#flow-3--resumen-semanal)
10. [Templates de Email](#templates-de-email)
11. [Roadmap Técnico](#roadmap-técnico)
12. [Campos a Definir](#campos-a-definir)

---

## Overview

### Arquitectura General

La solución usa **6 listas de SharePoint** como backend, **1 biblioteca de documentos** para evidencias, **3 flujos de Power Automate** para notificaciones automáticas, y la app HTML/JS como frontend embebido.

| Componente | Tecnología | Rol |
|---|---|---|
| **Frontend** | HTML + JS (SharePoint Web Part) | UI, lógica de roles, filtros, roadmap |
| **Backend** | SharePoint Lists + REST API | Persistencia, seguridad, item-level permissions |
| **Maestros** | Listas SP + ABM en app | Certificaciones y entidades editables |
| **Notificaciones** | Power Automate (3 flows) | Alertas de vencimiento automáticas |
| **Autenticación** | Azure AD / Windows Auth | SSO, detección de rol por grupo SP |
| **Documentos** | SP Document Library | Badges, PDFs de certificación |

### Listas SharePoint (resumen)

| Lista | Descripción | Quién escribe |
|---|---|---|
| **Colaboradores** | Datos del personal (legajo, área, seniority…) | Admin |
| **MaestroCertificaciones** | Catálogo de certs por proveedor cloud | Admin (app + SP) |
| **MaestroEntidades** | Entidades certificadoras por proveedor | Admin (app + SP) |
| **Certificaciones** | Registros individuales de cada cert | Empleado + Admin |
| **Roadmap** | Planificación de certificaciones futuras | Empleado + Admin |
| **DocumentosCerts** | Biblioteca: badges, PDFs, evidencias | Empleado + Admin |

### Flujos Power Automate (resumen)

| Flow | Trigger | Destinatarios | Frecuencia |
|---|---|---|---|
| **Alerta 30 días** | Scheduled (diario) | Empleado + Admin RRHH | Diario 08:00 |
| **Alerta vencida** | Scheduled (diario) | Empleado + Admin + Manager | Diario 08:00 |
| **Resumen semanal** | Scheduled (lunes) | Admin RRHH solamente | Lunes 09:00 |

---

## Datos de Colaboradores

### Campos actuales implementados

| Campo | Tipo SP | Notas |
|---|---|---|
| **Legajo** *(requerido)* | Single line (Text) | Identificador único. Ej: EJ-0042. Usado para filtros y reportes. |
| **Nombre** *(requerido)* | Single line (Text) | Nombre completo del colaborador |
| **Cargo** | Single line (Text) | Ej: Cloud Engineer, Data Analyst |
| **Area** *(requerido)* | Choice | Data & Analytics · Cloud Infrastructure · DevOps · Seguridad · Desarrollo · Consultoría |
| **Seniority** | Choice | Junior · Semi Senior · Senior · Lead |
| **FechaIngreso** | Date and Time | Fecha de ingreso a la consultora |
| **Email** | Single line (Text) | Email corporativo — usado por Power Automate para enviar alertas |
| **ClienteAsignado** | Single line (Text) | Cliente o proyecto actual |
| **Stack** | Multiple lines (Text) | JSON: `["gcp","aws","databricks"]` — proveedores cloud del colaborador |
| **AzureAdId** | Single line (Text) | ID de usuario en Azure AD (para lookup automático con Graph API) |
| **SPUserId** | Number | ID interno de SharePoint — se usa para item-level permissions |

### Campos a definir en el futuro

La lista `Colaboradores` acepta columnas adicionales en cualquier momento. Estas son las más comunes en consultoras IT:

| Campo sugerido | Tipo SP | Ejemplo | Cuándo agregar |
|---|---|---|---|
| **BandaSalarial** | Choice | B1 · B2 · B3 | Si RRHH gestiona compensaciones |
| **CertificacionObligatoria** | Multiple Choice | Terraform Associate, AZ-900 | Si hay certs contractuales por rol |
| **PresupuestoAnual** | Currency | $1500 | Si se gestiona por persona |
| **Manager** | Person or Group | juan@empresa.com | Para notificar al jefe en alertas |
| **ModalidadTrabajo** | Choice | Remoto · Híbrido · Presencial | Datos de RRHH generales |
| **Pais** | Choice | Argentina · Chile · Colombia | Si la consultora es multipaís |
| **FechaBaja** | Date | — | Para marcar colaboradores inactivos |
| **Activo** | Yes/No | Sí/No | Flag de baja/alta |

> 💡 **Recomendación:** Agregar la columna `Manager` (tipo Person or Group) es prioritario — permite que Power Automate notifique automáticamente al responsable cuando un cert de su equipo vence.

### Integración con Azure AD (opcional)

Si la empresa usa **Azure Active Directory**, se puede sincronizar automáticamente el campo `Email`, `Cargo` y `Manager` usando Microsoft Graph API desde Power Automate, evitando duplicación de datos:

```
// Acción en Power Automate: HTTP → Microsoft Graph
GET https://graph.microsoft.com/v1.0/users/@{items('Apply_to_each')?['AzureAdId']}
    ?$select=displayName,mail,jobTitle,manager

// Response → actualiza columnas en lista Colaboradores
{
  "Email": "@{body('Get_user')?['mail']}",
  "Cargo": "@{body('Get_user')?['jobTitle']}",
  "Manager": "@{body('Get_user')?['manager']?['mail']}"
}
```

---

## Listas de SharePoint

Crearlas en **Site Contents → New → List**.

### 1. Lista: Colaboradores

| Campo | Tipo | Notas |
|---|---|---|
| **Legajo** *(requerido)* | Single line | Requerido · Único · Índice |
| **Nombre** *(requerido)* | Single line | Requerido |
| **Cargo** | Single line | |
| **Area** | Choice | Data & Analytics;Cloud Infrastructure;DevOps;Seguridad;Desarrollo;Consultoría |
| **Seniority** | Choice | Junior;Semi Senior;Senior;Lead |
| **FechaIngreso** | Date (Date Only) | |
| **Email** | Single line | ⚠ Crítico para Power Automate |
| **ClienteAsignado** | Single line | |
| **Stack** | Multiple lines | JSON array de vendor IDs |
| **AzureAdId** | Single line | GUID de Azure AD |
| **SPUserId** | Number | ID de currentuser en SP |

### 2. Lista: MaestroCertificaciones

| Campo | Tipo | Notas |
|---|---|---|
| **Title** *(requerido)* | Single line | Nombre de la certificación (renombrar columna Title) |
| **Proveedor** *(requerido)* | Choice | gcp;azure;aws;databricks;google;hashicorp;otro |
| **Nivel** | Choice | Fundamentals;Associate;Professional;Expert;Specialty |
| **VigenciaMeses** | Number | 0 = sin vencimiento (ej: AZ-900, DB Fundamentals) |
| **Codigo** | Single line | Código oficial. Ej: AZ-104, DP-203 |
| **UrlInfo** | Single line | URL de la página oficial de la cert |
| **Activo** | Yes/No | Para dar de baja certs sin eliminarlas |

### 3. Lista: MaestroEntidades

| Campo | Tipo | Notas |
|---|---|---|
| **Title** *(requerido)* | Single line | Nombre de la entidad certificadora |
| **Proveedor** | Choice | Mismo dominio que MaestroCertificaciones |
| **UrlEntidad** | Single line | Web de la entidad |
| **PaisOrigen** | Single line | Para entidades locales |

### 4. Lista: Certificaciones (Principal)

| Campo | Tipo | Notas |
|---|---|---|
| **Title** *(requerido)* | Single line | Nombre de la cert (auto desde maestro) |
| **ColaboradorId** *(requerido)* | Single line | ID interno del colaborador (para item-level security) |
| **Legajo** *(requerido)* | Single line | Legajo denormalizado (para filtros rápidos) |
| **Colaborador** *(requerido)* | Single line | Nombre denormalizado |
| **EmailColaborador** | Single line | ⚠ Denormalizado — Power Automate lo lee directamente |
| **CertMasterId** *(requerido)* | Single line | ID del ítem en MaestroCertificaciones |
| **EntidadId** | Single line | ID del ítem en MaestroEntidades |
| **Entidad** | Single line | Nombre denormalizado |
| **Proveedor** | Choice | gcp;azure;aws;databricks;google;hashicorp;otro |
| **Nivel** | Choice | Fundamentals;Associate;Professional;Expert;Specialty |
| **FechaVencimiento** *(requerido)* | Date (Date Only) | ⚠ Crítico — Power Automate filtra por esta columna |
| **FechaEmision** | Date (Date Only) | |
| **NumeroCertificado** | Single line | Código de verificación del proveedor |
| **Costo** | Currency | USD · Para dashboard de presupuesto |
| **Notas** | Multiple lines | |
| **DocumentoUrl** | Single line | URL relativa del archivo en biblioteca |
| **AlertaEnviada30d** | Yes/No | Flag para que PA no envíe la alerta dos veces |
| **AlertaEnviadaVencida** | Yes/No | Flag para cert vencida |

### 5. Lista: Roadmap

| Campo | Tipo | Notas |
|---|---|---|
| **Title** *(requerido)* | Single line | Nombre de la cert planificada |
| **ColaboradorId** *(requerido)* | Single line | Item-level permissions |
| **Colaborador** | Single line | Nombre denormalizado |
| **Legajo** | Single line | |
| **CertMasterId** | Single line | |
| **Proveedor** | Choice | |
| **Nivel** | Choice | |
| **Estado** | Choice | Planificado;En Progreso;Completado;Cancelado |
| **Prioridad** | Choice | Alta;Media;Baja |
| **FechaInicioEstimada** | Date | |
| **FechaObjetivo** | Date | |
| **Progreso** | Number | 0 a 100 (porcentaje) |
| **CostoEstimado** | Currency | USD |
| **Notas** | Multiple lines | |

---

## Maestros en SP

Los maestros de certificaciones y entidades se pueden editar desde **dos lugares**: directamente en las listas de SharePoint (para administradores SP) o desde el ABM dentro de la app (para admins de la herramienta).

### Flujo de carga de un maestro

1. **Inicio** — Admin agrega nueva cert al maestro (desde la app ABM Maestros o directo en la lista SP)
2. **Acción** — POST a `MaestroCertificaciones` vía REST API (la app escribe directamente en la lista SP)
3. **Sincronización** — App recarga el select de certificaciones (al abrir el modal de nueva cert, consulta la lista maestro en tiempo real)
4. **Resultado** — Nueva cert disponible para todos los colaboradores (aparece en el dropdown del modal y en el roadmap)

### Carga inicial de maestros (PowerShell)

Para cargar las 20+ certificaciones iniciales de una sola vez:

```powershell
# Conectar
Connect-PnPOnline -Url "https://empresa.sharepoint.com/sites/certtrack" -Interactive

# Agregar certificaciones en lote
$certs = @(
  @{Title="Associate Cloud Engineer"; Proveedor="gcp"; Nivel="Associate"; VigenciaMeses=24; Codigo="ACE"},
  @{Title="Professional Data Engineer"; Proveedor="gcp"; Nivel="Professional"; VigenciaMeses=24; Codigo="PDE"},
  @{Title="AZ-104 Azure Administrator"; Proveedor="azure"; Nivel="Associate"; VigenciaMeses=24; Codigo="AZ-104"},
  @{Title="AZ-900 Azure Fundamentals"; Proveedor="azure"; Nivel="Fundamentals"; VigenciaMeses=0; Codigo="AZ-900"},
  @{Title="AWS Solutions Architect Associate"; Proveedor="aws"; Nivel="Associate"; VigenciaMeses=36; Codigo="SAA-C03"},
  @{Title="Databricks Data Engineer Associate"; Proveedor="databricks"; Nivel="Associate"; VigenciaMeses=24; Codigo="DEA"},
  @{Title="Terraform Associate"; Proveedor="hashicorp"; Nivel="Associate"; VigenciaMeses=24; Codigo="003"}
)

foreach ($cert in $certs) {
  Add-PnPListItem -List "MaestroCertificaciones" -Values $cert
  Write-Host "✓ $($cert.Title)"
}
```

---

## Grupos y Permisos

Configuración de seguridad en SharePoint Online. Se crean 2 grupos y se asignan permisos diferenciados por lista.

### Grupos a crear

**1. Ir a People and Groups**
⚙ Settings → Site Settings → Users and Permissions → **People and Groups** → New → New Group

**2. Grupo: CertTrack_Admins**
```
Name:        CertTrack_Admins
Description: Administradores de CertTrack IT (RRHH)
Owner:       (cuenta de servicio o admin principal)
Permisos SP: Full Control en todas las listas
```

**3. Grupo: CertTrack_Empleados**
```
Name:        CertTrack_Empleados
Description: Todos los colaboradores de la consultora
Owner:       CertTrack_Admins
Permisos SP: Contribute en Certificaciones y Roadmap
             Read en MaestroCertificaciones y MaestroEntidades
```

**4. Item-Level Permissions en lista Certificaciones**

List Settings → Advanced Settings:
```
Read access:   Read items that were created by the user
Create/Edit:   Create and edit items that were created by the user
Delete:        Delete items that were created by the user
```

> ⚠️ **Importante:** Esta configuración hace que la API REST retorne SOLO los ítems del usuario autenticado para colaboradores. Los admins (Full Control) ven todo sin importar este setting.

### Permisos por lista

| Lista | CertTrack_Admins | CertTrack_Empleados | Item-Level |
|---|---|---|---|
| **Colaboradores** | Full Control | Read (solo su registro) | Sí |
| **MaestroCertificaciones** | Full Control | Read | No |
| **MaestroEntidades** | Full Control | Read | No |
| **Certificaciones** | Full Control | Contribute | ✅ Sí |
| **Roadmap** | Full Control | Contribute | ✅ Sí |
| **DocumentosCerts (lib.)** | Full Control | Contribute | No (limitación de SP) |

---

## Power Automate — Introducción

Se implementan 3 flujos automatizados. Todos son **Scheduled Cloud Flows** (sin costo adicional, incluidos en Microsoft 365).

### Prerequisitos

| Requisito | Detalle |
|---|---|
| **Licencia** | Microsoft 365 Business Basic o superior (incluye PA) |
| **Permisos PA** | El creador del flow necesita acceso de edición a la lista Certificaciones |
| **Columna Email** | La lista Certificaciones debe tener `EmailColaborador` poblada |
| **Columna flags** | `AlertaEnviada30d` y `AlertaEnviadaVencida` (Yes/No) para no duplicar envíos |
| **Email admin** | Una cuenta de grupo RRHH (ej: `rrhh@empresa.com`) para recibir copias |

### Cómo acceder a Power Automate

1. **Ir a make.powerautomate.com** — Inicia sesión con la cuenta corporativa Microsoft 365. En el panel, clic en **Create → Scheduled cloud flow**.
2. **Seleccionar frecuencia** — Para los flujos de alerta: **Repeat every 1 Day**, hora: **08:00**. Para el resumen semanal: **Repeat every 1 Week**, día: **Monday**.
3. **Conectar SharePoint** — Primera vez que agregues una acción de SharePoint, Power Automate pedirá autenticación. Usa la cuenta que tiene acceso a la lista. Las credenciales quedan guardadas como *Connection* reutilizable.

---

## Flow 1 — Alerta 30 Días

Se ejecuta diariamente y notifica a cada colaborador cuyas certificaciones vencen en los próximos 30 días. Solo envía una vez por certificación gracias al flag `AlertaEnviada30d`.

### Diagrama del flujo

| Paso | Tipo | Detalle |
|---|---|---|
| **Trigger** | Scheduled | Recurrence: Diario a las 08:00. Interval: 1 · Frequency: Day · Start time: 08:00 UTC-3 |
| **Acción 1** | SharePoint — Get items | Lista Certificaciones. Filter Query (OData) — ver abajo |
| **Condition** | Control | `length(body('Get_items')?['value']) > 0` — si no hay ítems, termina el flujo |
| **Acción 2** | Control — Apply to each | Itera sobre los ítems devueltos |
| **Acción 3** | Email — Send an email (V2) | To: `@{items('Apply_to_each')?['EmailColaborador']}` · CC: rrhh@empresa.com |
| **Acción 4** | SharePoint — Update item | Marcar `AlertaEnviada30d = Yes` para no reenviar |

### OData Filter

```
// Filtrar certs que vencen entre hoy y 30 días, que no fueron notificadas
FechaVencimiento ge '@{formatDateTime(utcNow(), 'yyyy-MM-dd')}'
and FechaVencimiento le '@{formatDateTime(addDays(utcNow(), 30), 'yyyy-MM-dd')}'
and AlertaEnviada30d eq 0

// En Power Automate, el campo Yes/No se filtra con 0 (No) y 1 (Yes)
// Reemplazar AlertaEnviada30d por el nombre interno de la columna SP
```

> 💡 **Tip:** Si querés enviar alertas a los 60, 30 y 7 días, creá una variable `diasRestantes` calculada dentro del loop con `div(sub(ticks(items('Apply_to_each')?['FechaVencimiento']), ticks(utcNow())), 864000000000)` y usá condiciones anidadas para personalizar el mensaje según la urgencia.

---

## Flow 2 — Certificación Vencida

Detecta certificaciones que vencieron ayer o antes y no fueron notificadas. Envía alerta con tono urgente al colaborador, copia al admin y (si se configura) al manager.

### Diagrama del flujo

| Paso | Tipo | Detalle |
|---|---|---|
| **Trigger** | Scheduled | Recurrence: Diario a las 08:05 (5 minutos después del Flow 1 para evitar solapamientos) |
| **Acción 1** | SharePoint — Get items | Filter: `FechaVencimiento lt '@{formatDateTime(utcNow(),'yyyy-MM-dd')}' and AlertaEnviadaVencida eq 0` |
| **Acción 2** | Control — Apply to each | Itera sobre certs vencidas |
| **Acción 3a** | Email urgente al colaborador | To: EmailColaborador · CC: rrhh@empresa.com · Prioridad: Alta |
| **Acción 3b** | Teams (opcional) | Post message in Teams Channel: #alertas-certs |
| **Acción 4** | SharePoint — Update item | `AlertaEnviadaVencida = Yes` |

### Acción de Teams (opcional)

```
// Acción: "Post message in a chat or channel (V2)"
Team:    RRHH Interno
Channel: alertas-certificaciones
Message:
  🔴 **CERT VENCIDA**
  Colaborador: @{items('Apply_to_each')?['Colaborador']} (@{items('Apply_to_each')?['Legajo']})
  Certificación: @{items('Apply_to_each')?['Title']}
  Proveedor: @{items('Apply_to_each')?['Proveedor']}
  Venció: @{items('Apply_to_each')?['FechaVencimiento']}
  👉 @{concat(variables('SiteUrl'), '/SitePages/CertTrack.aspx')}
```

---

## Flow 3 — Resumen Semanal

Se ejecuta todos los lunes y envía a los administradores RRHH un resumen consolidado del estado de todas las certificaciones: vencidas, por vencer y vigentes.

### Diagrama del flujo

| Paso | Tipo | Detalle |
|---|---|---|
| **Trigger** | Scheduled | Recurrence: Lunes 09:00. Interval: 1 · Frequency: Week · On these days: Monday |
| **Acciones 1, 2, 3** | SharePoint (en paralelo) | 3x Get items: Vencidas / Por vencer 30d / Por vencer 60d |
| **Acción 4** | Variables | Inicializar: `countVencidas`, `countPorVencer`, `countOk`, `tablaVencidas` (string HTML), `tablaPorVencer` |
| **Acción 5** | Apply to each × 2 | Construir filas HTML para cada grupo (concatenar `<tr>` a las variables) |
| **Acción 6** | Email HTML | Body: HTML completo con tablas de estado · IsHtml: Yes · Destinatario: rrhh@empresa.com |

### Construcción del HTML en el loop

```
// Dentro de "Apply to each" para vencidas:
// Acción: Append to string variable — tablaVencidas

Value:
<tr>
  <td>@{items('Apply_to_each')?['Legajo']}</td>
  <td>@{items('Apply_to_each')?['Colaborador']}</td>
  <td>@{items('Apply_to_each')?['Title']}</td>
  <td>@{items('Apply_to_each')?['Proveedor']}</td>
  <td style="color:red">@{items('Apply_to_each')?['FechaVencimiento']}</td>
</tr>
```

---

## Templates de Email

Copiar en el campo **Body** de la acción *Send an email (V2)* en Power Automate. Activar **IsHtml: Yes**.

### Template 1 — Alerta 30 días (empleado)

**Para:** `@{items('Apply_to_each')?['EmailColaborador']}` · **CC:** rrhh@empresa.com  
**Asunto:** ⚠️ Tu certificación vence pronto — `@{items('Apply_to_each')?['Title']}`

```html
<div style="font-family:sans-serif;max-width:600px;margin:0 auto">
  <div style="background:#1a1e2c;padding:16px 24px;border-radius:8px 8px 0 0">
    <h2 style="color:#4f8ef7;margin:0;font-size:18px">CertTrack IT</h2>
  </div>
  <div style="border:1px solid #e5e7eb;padding:24px">
    <h3 style="color:#d97706">
      ⚠️ Certificación próxima a vencer
    </h3>
    <p>Hola <strong>@{items('Apply_to_each')?['Colaborador']}</strong>,</p>
    <p>Tu certificación <strong>@{items('Apply_to_each')?['Title']}</strong>
    vence el <strong style="color:#d97706">@{items('Apply_to_each')?['FechaVencimiento']}</strong>.</p>
    <table style="width:100%;border-collapse:collapse;margin:16px 0">
      <tr style="background:#f9fafb">
        <td style="padding:8px 12px;font-weight:bold">Proveedor</td>
        <td style="padding:8px 12px">@{items('Apply_to_each')?['Proveedor']}</td>
      </tr>
      <tr>
        <td style="padding:8px 12px;font-weight:bold">N° Certificado</td>
        <td style="padding:8px 12px">@{items('Apply_to_each')?['NumeroCertificado']}</td>
      </tr>
    </table>
    <a href="@{variables('AppUrl')}"
       style="display:inline-block;background:#4f8ef7;color:white;
              padding:10px 24px;border-radius:6px;text-decoration:none;font-weight:bold">
      Gestionar en CertTrack →
    </a>
  </div>
  <div style="background:#f9fafb;padding:12px 24px;font-size:12px;color:#6b7280">
    Mensaje automático · CertTrack IT · @{formatDateTime(utcNow(),'dd/MM/yyyy')}
  </div>
</div>
```

### Template 2 — Certificación vencida (urgente)

**Para:** EmailColaborador · **CC:** rrhh@empresa.com · **Importancia:** Alta  
**Asunto:** 🔴 ACCIÓN REQUERIDA — Certificación vencida: `@{items('Apply_to_each')?['Title']}`

Contenido: mensaje de alerta con fondo rojo destacando que la certificación ha vencido, tabla con datos de la cert (nombre, legajo, fecha de vencimiento, días vencida) y enlace a CertTrack IT para gestionar la renovación.

### Template 3 — Resumen semanal (admin)

**Para:** rrhh@empresa.com · Lunes 09:00  
**Asunto:** 📊 Resumen Semanal — Estado de Certificaciones | semana del `@{formatDateTime(utcNow(),'dd/MM/yyyy')}`

Contenido: tabla resumen con métricas (vigentes, por vencer ≤30 días, vencidas sin renovar, en roadmap) seguida de tabla detallada con los vencimientos urgentes de la semana (legajo, colaborador, certificación, días restantes).

---

## Roadmap Técnico

Plan de implementación por fases.

### Fase 1 · Semanas 1–3: MVP y Notificaciones básicas

**Semanas 1–2:**
- Crear las 5 listas en SP
- Cargar maestros (PowerShell)
- Configurar grupos y permisos
- Embeber la app en la página SP
- Cargar colaboradores y primeras certificaciones

**Semana 3:**
- Crear Flow 1 (alerta 30 días) y Flow 2 (vencida)
- Probar con un subconjunto de datos
- Agregar el campo `Manager` si ya está disponible

### Fase 2 · Mes 2–3: Roadmap y presupuesto

- Activar módulo Roadmap
- Configurar presupuesto anual
- Crear Flow 3 (resumen semanal)
- Training a admins de RRHH
- Definir y agregar los campos "a definir" (banda, presupuesto individual, modalidad, etc.)
- Actualizar la app para mostrarlos

### Fase 3 · Mes 3–4+: Integraciones avanzadas

- Sincronización con Azure AD para datos de colaboradores
- SPFx Web Part (si se migra a páginas modernas)
- Power BI embedded con los datos de SP
- Alerta a Teams además de email
- Generación de reportes PDF con Power Automate
- Integración con plataformas de verificación de certs (Credly, Acclaim)
- Portal de auto-consulta para clientes

---

## Campos a Definir

Esta sección es el punto de partida para definir los campos adicionales del colaborador. Completar la tabla con el equipo de RRHH y tecnología.

### Proceso recomendado para definir campos

1. **Workshop con RRHH y líderes técnicos** — Identificar qué información se necesita para reportes, proyecciones de certificaciones y asignación a clientes.
2. **Priorizar por impacto** — ¿Qué campos son necesarios para los *Power Automate flows* o reportes de clientes? Empezar por esos.
3. **Agregar a la lista SP y a la app** — Agregar la columna en SharePoint, luego actualizar el modal de colaboradores en la app (sección `fgrid` del modal de colaborador).

### Tabla de definición

| Campo | ¿Para qué se usaría? | ¿Obligatorio? | Prioridad | Estado |
|---|---|---|---|---|
| **Manager / Jefe directo** | Notificar al jefe en alertas de vencimiento | No | Alta | Por definir |
| **Banda salarial** | Correlación cert ↔ banda | No | Media | Por definir |
| **Presupuesto anual individual** | Control de gasto por persona | No | Media | Por definir |
| **Modalidad de trabajo** | Datos de RRHH | No | Baja | Por definir |
| **País / Sede** | Reportes multi-región | No | Media | Por definir |
| **Activo / Inactivo** | Dar de baja sin eliminar | No | Alta | Por definir |
| **Fecha de baja** | Historial | No | Media | Por definir |
| **Certificaciones obligatorias por rol** | Alertar certs faltantes | No | Alta | Por definir |

> ✅ **La app está diseñada para escalar:** cualquier campo nuevo en la lista `Colaboradores` de SharePoint puede incorporarse al modal de edición en la app sin cambios estructurales. Solo se agrega un nuevo `<div class="fg">` en el HTML del modal y se mapea en la función `saveColab()`.
