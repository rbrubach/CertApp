# GUIA-USUARIO-EMPLEADO.md — Manual para Empleados

**Para:** Empleados / Usuarios finales de CertTrack IT  
**Nivel:** Básico  
**Tiempo:** 10-15 minutos lectura  
**Última actualización:** 2026-05-04

---

## 📋 Tabla de Contenidos

1. [Acceso & Login](#acceso--login)
2. [Home Page - Mi Dashboard](#home-page---mi-dashboard)
3. [Mis Certificaciones](#mis-certificaciones)
4. [Alertas de Vencimiento](#alertas-de-vencimiento)
5. [Skills Matrix](#skills-matrix)
6. [Entender Estados](#entender-estados)
7. [Tips & Best Practices](#tips--best-practices)
8. [FAQ Empleado](#faq-empleado)

---

## Acceso & Login

### Cómo Acceder

**Tu admin te enviará un link como este:**

```
https://tenant.sharepoint.com/sites/certtrack/Paginas/certtrack.aspx
```

**Pasos:**

```
1. Click en el link (puede estar en email o Teams)
2. SharePoint abre automáticamente
3. Si estás logueado, verás la app instantáneamente
4. Si no → SharePoint pide login
5. Ingresa tu email corporativo
6. La app carga
7. ¡Listo! Ves tu dashboard personal
```

### ¿Si no puedo acceder?

```
Posibles razones:

1. No estoy en grupo "CertTrack_Empleados"
   → Contacta tu admin
   
2. Link está incorrecto
   → Verifica que copiaste bien o pide al admin
   
3. Navegador no abre SharePoint
   → Usa Chrome o Edge (mejor soporte)
   → Borra cookies y cache si sigue fallando
```

---

## Home Page - Mi Dashboard

**Pantalla que ves al entrar:**

```
┌─────────────────────────────────────────┐
│ CertTrack IT            [Tu nombre ▼]   │
├─────────────────────────────────────────┤
│ Sidebar          │ Main Content         │
│ Home (active)    │                      │
│ Mis Certs        │  📊 TUS ESTADÍSTICAS │
│ Alertas          │  ┌──────┬──────┐    │
│ Skills           │  │Total │Vigent│    │
│                  │  │ 4    │  3   │    │
│                  │  └──────┴──────┘    │
│                  │  ┌──────┬──────┐    │
│                  │  │P.Venc│Vencid│    │
│                  │  │ 1    │  0   │    │
│                  │  └──────┴──────┘    │
│                  │                      │
│                  │  📅 PROXIMOS 30 DÍAS │
│                  │  (mostrada si hay    │
│                  │   algún vencimiento) │
└─────────────────────────────────────────┘
```

### Qué Significan los Números

| Métrica | Qué Es | Ejemplo |
|---------|--------|---------|
| **Total** | Cuántas certs tienes registradas | 4 certs |
| **Vigentes** | Que NO van a vencer en próximos 30 días | 3 certs |
| **Por Vencer** | Que vencen en próximos 30 días | 1 cert |
| **Vencidas** | Que ya expiraron (pasó fecha) | 0 certs |

**En tu ejemplo:** Tienes 4 certificaciones, 3 están bien, 1 está por vencer pronto.

---

## Mis Certificaciones

### Ver Mis Certificaciones

```
Pasos:
1. Sidebar → Click "Mis Certificaciones"
2. Tabla aparece con TUS certs

Columnas:
  - Nombre: "GCP Associate Cloud Engineer"
  - Entidad: "Google Cloud"
  - Vencimiento: "2027-05-15"
  - Estado: [badge verde/naranja/rojo]
  - Acciones: [botones]
```

### Entender Cada Certificación

Ejemplo de fila:

```
┌─────────────────────────────────────────────────┐
│ GCP Associate Cloud Engineer │ Google │ Vigente │
│ Vencimiento: 2027-05-15     │ 750+ días       │
├─────────────────────────────────────────────────┤
│ [Ver] [Editar] [Compartir?]                     │
└─────────────────────────────────────────────────┘
```

**¿Qué significa cada parte?**

- **Nombre:** Nombre exacto de la certificación
- **Entidad:** Quién la emite (Google, Microsoft, Amazon, etc.)
- **Vencimiento:** Fecha en que expira
- **Estado:** Verde=vigente, Naranja=próxima a vencer, Rojo=vencida
- **Días:** Días que faltan (750+ días = mucho tiempo aún)

### Actualizar Mi Certificación

**Caso:** Renové mi GCP, necesito actualizar fecha.

```
Pasos:
1. Buscar la cert en tabla
2. Click botón "Editar"
3. Modal se abre con formulario
4. Cambiar "Fecha Vencimiento": 2027-05-15 → 2030-05-15
5. Click "Guardar"
6. Alerta verde: "Certificación actualizada"
7. Tabla se actualiza automáticamente
8. Estado vuelve a verde
```

---

## Alertas de Vencimiento

### Qué Son Alertas

Notificaciones automáticas que te avisan cuando una cert va a vencer.

**Cómo funciona:**

```
Fecha HOY: 2026-05-04
Mi AWS vence en: 2026-05-30 (26 días)
→ Está en rango de 30 días
→ Sistema genera ALERTA
→ Recibo email diciendo "Tu AWS vence en 26 días"
```

### Ver Mis Alertas

```
Pasos:
1. Sidebar → Click "Alertas"
2. Tabla muestra alertas ACTIVAS para ti

Columnas:
  - Certificación
  - Vencimiento
  - Días faltantes
  - Acciones: Renovar / Marcar resuelto
```

**Estados de color:**

```
🟢 Verde  = Vigente (sin alerta)
🟠 Naranja = Por vencer (ALERTA - actúa)
🔴 Rojo   = Vencida (CRÍTICA - actúa YA)
```

### Qué Hacer Cuando Recibo Alerta

**Opción 1: Voy a Renovar (Recomendado)**

```
Pasos:
1. Inscribirse en examen de renovación
2. Estudiar si es necesario
3. Tomar examen
4. Obtener certificado nuevo
5. Comunicar a admin: "Ya renové"
6. Admin actualiza fecha
```

**Opción 2: Ya Renové**

```
Pasos:
1. Ir a "Mis Certificaciones"
2. Click "Editar" en esa cert
3. Cambiar fecha vencimiento a futura
4. Click "Guardar"
5. Alerta desaparece automáticamente
```

**Opción 3: No Voy a Renovar**

```
Contactar admin: "No necesito renovar GCP"
Admin puede:
- Marcar alerta como resuelta
- O eliminar certificación
```

### Recibir Alertas por Email

Las alertas también se envían por email automáticamente.

```
Email de ejemplo:

Subject: ⚠️ Certificación por Vencer: GCP Associate

Contenido:
  Hola Juan,
  
  Tu certificación está por vencer:
  
  📌 Certificación: GCP Associate Cloud Engineer
  📅 Fecha de vencimiento: 2026-05-30
  ⏰ Días restantes: 26 días
  
  Por favor, planifica su renovación.
  
  Acceder: [Link]
  
  Saludos,
  CertTrack Team
```

---

## Skills Matrix

### Qué Es Skills Matrix

Visualización de tus certificaciones agrupadas por área de expertise.

**Ejemplo:**

```
┌──────────────────────────────┐
│ CLOUD                        │
│ ✅ GCP Associate             │
│ ✅ Azure Administrator       │
│ ⚠️ AWS Solutions Arch (Ven)  │
├──────────────────────────────┤
│ INFRAESTRUCTURA              │
│ ✅ Linux Administration      │
├──────────────────────────────┤
│ BASES DE DATOS               │
│ ✅ Oracle DBA                │
└──────────────────────────────┘
```

### Cómo Usar

```
Acceder: Sidebar → "Skills"

Uso:
  - Ver qué áreas tengo cobertura
  - Identificar gaps (dónde faltan certs)
  - Presentar a tu manager como evidence de learning
  - Usar para portfolio / CV
```

---

## Entender Estados

### Tres Estados Posibles

#### 🟢 VIGENTE
- Certificación es válida
- Vence en > 30 días
- ¿Qué hacer?: Mantener y disfruta de tu certificación

#### 🟠 POR VENCER
- Certificación sigue siendo válida POR AHORA
- Vence en 0-30 días
- ¿Qué hacer?: **Actúa** — Planifica renovación

#### 🔴 VENCIDA
- Certificación ya NO es válida
- Pasó la fecha de vencimiento
- ¿Qué hacer?: **Actúa YA** — Renueva urgentemente

### Ejemplos

```
Hoy es: 2026-05-04

Cert A: Vence 2027-01-04  →  Vigente (240+ días)
Cert B: Vence 2026-06-04  →  Por Vencer (31 días)
Cert C: Vence 2026-05-30  →  Por Vencer (26 días)
Cert D: Vence 2026-04-30  →  Vencida (-4 días)
```

---

## Tips & Best Practices

### ✅ Hacer

```
1. Revisar "Mis Certificaciones" mensualmente
   - Saber cuáles vencen
   - Planificar renewals
   - No sorpresas

2. Actuar cuando vea alerta
   - Dentro de 30 días no es mucho tiempo
   - Exámenes toman semanas de estudio
   - Mejor temprano que tarde

3. Actualizar inmediatamente cuando renueves
   - Después de obtener cert → avisar a admin
   - O auto-actualizar en la app
   - Así alertas desaparecen

4. Guardar tus badges / certificados digitales
   - Descargables desde proveedor
   - Útil para CV / LinkedIn
   - Backup en caso que necesites prove

5. Comunicar con tu manager
   - Informar cuando obtengas certs
   - Discutir plan de renewals
   - Alinear con objetivos de carrera
```

### ❌ NO hacer

```
1. NO ignorar alertas
   - "Tengo tiempo" → No, 30 días pasa rápido
   - Resultado: Cert vence y te desacreditas

2. NO esperar hasta último día
   - Examen falla → Sin backup
   - Mejor: Planificar 2-3 meses antes

3. NO dejar certs vencidas sin renovar
   - Si necesitas la cert, renueva
   - Si no necesitas, comunica al admin

4. NO compartir certificados falsos
   - Eres responsable de la integridad
   - Si hay error, reportar

5. NO ignorar mails de vencimiento
   - El sistema te avisa por razón
   - Acción requerida
```

---

## FAQ Empleado

### P: ¿Cómo sé cuándo vence mi certificación?

**R:** Dos formas:

Opción 1: Home page
```
Dashboard muestra "Por Vencer: 1 cert"
Click en ese número → va a "Alertas"
```

Opción 2: Mis Certificaciones
```
Click "Mis Certificaciones" en sidebar
Tabla muestra fecha vencimiento
Badge color: Verde=ok, Naranja=pronto, Rojo=vencida
```

### P: Renové mi cert, ¿cómo lo actualizo?

**R:**
```
1. Click "Mis Certificaciones"
2. Find la cert renovada
3. Click "Editar"
4. Cambiar "Fecha Vencimiento" a nueva fecha
5. Click "Guardar"
6. ¡Listo! Dashboard se actualiza
```

### P: ¿Puedo ver certs de otros empleados?

**R:** No (por privacidad).
```
Tu vista SOLO muestra TUS certs
Si necesitas ver de otros:
- Contacta admin (si tienes razón válida)
- Admin puede generar reporte
```

### P: ¿Qué pasa si mi cert vence?

**R:** 
```
Corto plazo:
  - Alerta se pone ROJA
  - Email dice "CRÍTICO"

Impacto:
  - Ya no puedes usarla en CV/LinkedIn
  - Para clientes: pérdida de credibilidad
  
Solución:
  - Renovarla ASAP
  - O eliminarla si no la necesitas
  
Nota: En futuro, podría afectar asignaciones de proyecto
```

### P: ¿Puedo eliminar certificaciones?

**R:** Técnicamente sí, pero no recomendamos.

```
Si cert es VÁLIDA:
  - NO eliminar
  - Es evidence de tu expertise
  
Si cert es ERROR (ej: registrada 2 veces):
  - Contactar admin
  - Admin elimina la duplicada
```

### P: ¿Cómo comparto mi skills con mi manager?

**R:**
```
Opción 1: Skills Matrix
  - Pantalla está diseñada para esto
  - Screenshot + compartir

Opción 2: Reporte PDF
  - Admin puede generar (si futura función)
  
Opción 3: Manual
  - Listar tus certs en email / doc
```

### P: ¿Hay límite de certificaciones?

**R:** No. La app soporta todos los que necesites (10, 100, 1000...).

### P: ¿Si cambio de área/equipo?

**R:**
```
Tu admin actualiza tu datos:
  - Email (si cambió)
  - Área (Cloud → Infraestructura)
  - Rol (Junior → Senior)

Tus certs permanecen (no se pierden)
Sistema actualiza vistas automáticamente
```

### P: ¿Tengo acceso a reportes?

**R:**
```
Básico: Sí, tu propio dashboard (home page)
  - Tus stats
  - Tus alertas
  - Tu skills matrix

Avanzado: No (eso es para admin)
  - Reportes de todo el equipo
  - Análisis de tendencias
  - Si necesitas → pedir a admin
```

### P: ¿Quién es mi contacto si tengo problemas?

**R:**
```
Nivel 1: Tu admin de CertTrack
  - Actualizar certs
  - Resolver alertas

Nivel 2: Tu manager
  - Si admin no responde
  - Para decisiones de carrera

Nivel 3: Tech support
  - Si app tiene error técnico
  - Email: tech-lead@nttdata.com
```

---

## Checklist Mi Primer Mes

```
Semana 1:
  [ ] Abrir app y ver home page
  [ ] Revisar "Mis Certificaciones"
  [ ] Revisar "Mis Alertas"
  [ ] Entender qué vence pronto (si hay)

Semana 2-3:
  [ ] Si hay alerta → planificar renovación
  [ ] Si vencimiento < 60 días → empezar estudio

Semana 4:
  [ ] Revisar alertas nuevamente
  [ ] Comunicar plan de renewal al admin/manager

Status: Ready to use ✅
```

---

## Contacto & Soporte

Preguntas o problemas:

```
Primero: Revisar esta guía (FAQ Empleado)
Luego: Contactar a tu admin
Si no se resuelve: 
  - Email: tech-lead@nttdata.com
  - Describir qué pasó + screenshot
```

---

**Última actualización:** 2026-05-04  
**Documento fácil de entender para empleados sin experiencia técnica**
