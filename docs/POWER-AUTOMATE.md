# POWER-AUTOMATE.md — Configurar Alertas Automáticas

**Objetivo:** Automatizar notificaciones por email cuando certificados están por vencer  
**Timeline:** 45 min  
**Prerequisitos:** Power Automate license (incluido en M365), listas SP creadas

---

## 📧 Flujos Recomendados

### Flow 1: Alerta 30 días Antes de Vencer

**Trigger:** Diario (11 PM)  
**Acción:** Revisar certificados con fecha vencimiento < 30 días  
**Envío:** Email al colaborador + Admin

**Crear:**

```
Power Automate → Create → Scheduled cloud flow
Name: "CertTrack - 30 días antes de vencer"
Runs every: 1 Day at 11:00 PM
```

**Pasos:**

```
1. Add action → SharePoint → Get items
   Site: [tu sitio certtrack]
   List: Certificaciones
   Filter: 
     AND [FechaVencimiento] is within next 30 days
     AND [FechaVencimiento] is on or after today

2. Add action → For each (para iterar items)
   Select output from previous step

3. Inside For each:
   a. Get user profile
      Email: [colabId]  → para obtener email del colaborador
      
   b. Send an email (V2) - Office 365 Outlook
      To: [Email del colaborador]
      Subject: ⚠️ Certificación por Vencer: @{items('For_each')?['Title']}
      Body:
      ```
      Hola,
      
      Tu certificación está por vencer en 30 días:
      
      📌 Certificación: @{items('For_each')?['Title']}
      📅 Vencimiento: @{items('For_each')?['FechaVencimiento']}
      
      Por favor, planifica su renovación.
      
      Saludos,
      CertTrack Team
      ```
      
   c. Send an email (V2) - Office 365 Outlook
      [ADMIN COPY]
      To: admin@tenant.com
      Subject: 📊 [ADMIN] Certificación por vencer: @{items('For_each')?['Title']}
      Body: 
      ```
      NOTIFICACIÓN PARA ADMINISTRADOR
      
      El siguiente certificado del colaborador está por vencer:
      
      👤 Colaborador: [Obtener de colabId]
      📌 Certificación: @{items('For_each')?['Title']}
      📅 Vencimiento: @{items('For_each')?['FechaVencimiento']}
      
      Acción requerida:
      - Verificar que el colaborador renueva
      - Actualizar fecha en CertTrack
      
      [Link a CertTrack]
      ```

4. Save & Test
   → Esperar a que se ejecute mañana
   → O trigger manual para test
```

---

### Flow 2: Alerta en Fecha de Vencimiento

**Trigger:** Diario (9 AM)  
**Acción:** Notificar certificados VENCIDOS hoy  
**Envío:** Email a colaborador + Admin

**Similar a Flow 1, pero:**

```
Filter en "Get items":
  [FechaVencimiento] is today
```

---

### Flow 3: Reporte Semanal para Admin

**Trigger:** Semanal (Lunes 9 AM)  
**Acción:** Resumen de estado general  
**Envío:** Email a admin

**Crear:**

```
Power Automate → Create → Scheduled cloud flow
Name: "CertTrack - Reporte Semanal"
Runs: Every Monday at 9:00 AM
```

**Pasos:**

```
1. Get items: Certificaciones
   Filter: [FechaVencimiento] is within next 30 days

2. Compose: Crear HTML table
   ```
   <table style="border-collapse:collapse;width:100%;">
   <tr style="background:#007bff;color:white;">
     <th style="padding:10px;border:1px solid #ddd;">Colaborador</th>
     <th style="padding:10px;border:1px solid #ddd;">Certificación</th>
     <th style="padding:10px;border:1px solid #ddd;">Vencimiento</th>
   </tr>
   @{items('For_each')?['Title']}
   ...
   </table>
   ```

3. Send an email (V2)
   To: admin@tenant.com
   Subject: 📊 CertTrack Semanal - Certificaciones por Vencer
   Body: [HTML table anterior]
```

---

## 🔌 Integración con Formularios Dinámicos

Si se agregan formularios para "Crear Certificación":

```
Trigger: When item is created in Certificaciones list
Actions:
  1. Get item details
  2. Calculate próxima fecha vencimiento
  3. Create Roadmap entry automático
  4. Send email a admin: "Nueva cert registrada"
```

---

## 🔐 Permisos Necesarios

```
Power Automate Connection:
  ✓ Office 365 Outlook (para enviar emails)
  ✓ SharePoint Online (para leer listas)
  
Nota: Los flows se ejecutan con permisos de quién creó el flow.
      Si admin lo crea, puede acceder a listas de admin.
```

---

## ✅ Checklist

```
[ ] Flow 1 creado y testeado (30 días)
[ ] Flow 2 creado (vencimiento)
[ ] Flow 3 creado (reporte semanal)
[ ] Emails recibidos correctamente
[ ] Links en email funcionan (llevan a CertTrack)
[ ] Emails no van a spam (revisar carpeta de spam)
```

---

## 🧪 Testing

### Test Flow 1 (30 días antes)

```
1. Crear certificado con FechaVencimiento = HOY + 25 días
2. Ir a Power Automate → Mis flujos
3. Click flow → Test → Ejecutar manualmente
4. Verificar que email llega en 2 min
5. Eliminar certificado de prueba
```

### Test Flow 2 (Hoy vencido)

```
1. Crear certificado con FechaVencimiento = HOY
2. Ejecutar Flow 2 manualmente
3. Verificar email
4. Limpiar datos de prueba
```

---

## 📊 Monitoreo

**Para revisar si flows se ejecutan:**

```
Power Automate → Mis flujos → [Flow name]
  → 28-day run history
  → Si ves "Failed", expandir para ver error

Errores comunes:
  ❌ "Access denied" → usuario no en grupo correcto
  ❌ "Item not found" → colabId no corresponde a lista
  ❌ "Email invalid" → email no en formato correcto
```

---

## 🚀 Evolución Futura

### Notificaciones Push (Teams/Slack)

Si en futuro se requiere notificaciones más inmediatas:

```
Power Automate → Teams → Post message in chat
  → Enviar alerta a canal de CertTrack en Teams
  → Más visible que email
```

### Webhooks

Si en futuro la app necesita notificaciones desde Power Automate:

```
Flow → When HTTP request received
  → Llamar endpoint en app
  → Actualizar UI en tiempo real
```

---

**Última actualización:** 2026-05-04  
**Siguiente:** Volver a [DEPLOYMENT.md](./DEPLOYMENT.md)
