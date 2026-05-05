# FAQ-TROUBLESHOOTING.md — Preguntas Frecuentes & Solución de Problemas

**Para:** Todos (Admin + Empleados)  
**Propósito:** Resolver issues sin contactar soporte  
**Última actualización:** 2026-05-04

---

## 📋 Tabla de Contenidos

1. [Problemas de Acceso](#problemas-de-acceso)
2. [Problemas de Datos](#problemas-de-datos)
3. [Problemas de Notificaciones](#problemas-de-notificaciones)
4. [Problemas de Performance](#problemas-de-performance)
5. [FAQ General](#faq-general)
6. [Cuándo Contactar Soporte](#cuándo-contactar-soporte)

---

## Problemas de Acceso

### "No puedo abrir la app"

**Síntomas:**
- Hago click en link y no pasa nada
- O me pide login repetidamente
- O veo error 404 / 403

**Soluciones:**

```
Paso 1: Verifica el link
  ✓ El link es correctamente copiado?
  ✓ No tiene espacios al inicio/final?
  ✓ Comienza con https://
  
Paso 2: Limpiar cache del navegador
  Chrome/Edge:
    1. Press: Ctrl + Shift + Delete
    2. Select: "All time"
    3. Checkmark: Cookies, Cache
    4. Click "Clear data"
  
Paso 3: Cerrar todas pestañas de SharePoint
  - SharePoint a veces tiene cache
  - Cerrar todo y abrir fresh link
  
Paso 4: Cambiar navegador
  - ¿Probaste Chrome? → Intenta Edge
  - ¿Probaste Firefox? → Intenta Chrome
  - Safari tiene mejores resultados en Mac
  
Paso 5: Si aún no funciona
  - Contactar admin (te dará link nuevo)
```

---

### "Me pide login pero está bloqueada mi cuenta"

**Síntomas:**
- Error: "Access Denied"
- O: "You don't have permission"

**Soluciones:**

```
1. Verificar que estás usando CORRECT login
   - ¿Estás en cuenta corporativa?
   - ¿O login personal?
   → Logout + Login con cuenta corporativa

2. Verificar grupo Azure AD
   - Admin debe:
     ☑ Ir a Azure AD
     ☑ Buscar "CertTrack_Empleados"
     ☑ Verificar que estés en la lista
     ☑ Si NO → agregar
   
   Después:
   - Esperar 5-10 min (caché de AD)
   - Logout / Login
   - Intentar de nuevo

3. Verificar permisos sitio SharePoint
   - Admin verifica: Settings → Site permissions
   - Verificar que grupo tiene "Edit" o superior
   - Si no → agregar / actualizar permisos
```

---

### "La app carga pero está en blanco / incompleta"

**Síntomas:**
- Página abre pero está vacía
- O falta sidebar / header
- O solo ve texto sin estilos (colores, fuentes)

**Soluciones:**

```
Paso 1: Reload página
  - Press F5 (o Cmd+R en Mac)
  - Esperar 2-3 segundos
  
Paso 2: Limpiar cache
  - Ctrl + Shift + Delete (ver sección anterior)
  - Reload

Paso 3: Usar incognito (sin cache)
  - Chrome: Ctrl + Shift + N
  - Edge: Ctrl + Shift + P
  - Abrir link
  - Si funciona → problema de cache confirmado
  
Paso 4: Verificar JavaScript
  - Press F12 (DevTools)
  - Click "Console" tab
  - ¿Hay errores en rojo?
  → Si sí → nota el error y contacta soporte
  
Paso 5: Verificar Google Fonts
  - Si está en red corporativa:
    - ¿Red bloquea CDN externo?
    - Fallback a Arial debería verse (sin bonito)
    - Si ni Arial se ve → problema de red corporativa
    → Contacta IT corporativo
```

---

## Problemas de Datos

### "No veo mis certificaciones"

**Síntomas:**
- Home page dice "Total: 0"
- "Mis Certificaciones" tabla está vacía

**Soluciones para Empleado:**

```
Opción 1: Certificaciones aún no creadas
  - Normal si es primer uso
  - Admin debe crearlas (o tú si tienes acceso)
  - Solución: Contactar admin

Opción 2: Certificaciones son de OTRO empleado
  - Sistema solo muestra TUS certs
  - Si Admin vio tus certs pero TÚ no → permisos
  - Solución: Admin verifica item-level security

Opción 3: Refrescamiento de datos (live mode)
  - Si recién agregaron certs:
    1. Reload página (F5)
    2. Esperar 5-10 segundos
    3. Si sigue sin aparecer → cache
    → Limpiar cache (ver sección anterior)
```

**Soluciones para Admin:**

```
Verificar:
  1. ¿Certificación se creó?
     → Ir a SharePoint list "Certificaciones"
     → Buscar el item
     → Si NO existe → nunca se guardó (error al guardar)
     → Si existe → issue en sync entre app y SP
  
  2. ¿Empleado correcto asignado?
     → Item debe tener campo "colabId" = ID del empleado
     → Si colabId diferente → se ve en otro empleado
  
  3. ¿Permisos correctos?
     → Item-level security habilitado?
     → Empleado puede LEER el item?
     → Admin verifica permisos en SP directamente
```

---

### "Veo certificación de otro empleado (que NO debería ver)"

**Síntomas:**
- Como empleado, veo certs que no son míos
- Esto es **SECURITY ISSUE**

**CRÍTICO:**

```
⚠️  SECURITY BREACH - Report immediately:

1. LOGOUT inmediatamente
2. Contactar admin
3. Admin debe:
   a. Desactivar usuario si es necesario
   b. Revisar qué vio
   c. Auditar SharePoint permisos
   d. Contactar security team

Este es un problema grave que requiere investigación.
```

---

### "Mi certificación muestra fecha incorrecta"

**Síntomas:**
- Guardé fecha "2027-05-15"
- Sistema muestra "2026-05-15" (diferente)

**Soluciones:**

```
Paso 1: Verificar que guardaste correctamente
  - Modal decía "2027-05-15"?
  - Click "Guardar"?
  - Viste alerta "ok"?
  
Paso 2: Reload página
  - Fecha correcta después de reload?
  - Si no → problema de sincronización

Paso 3: Editar de nuevo
  - Click "Editar" en esa fila
  - Cambiar a fecha correcta
  - Guardar
  
Paso 4: Si persiste error
  - Contactar admin
  - Puede haber problema en SP directamente
  - Admin verifica en lista SP
```

---

### "Creé certificación pero no aparece en tabla"

**Síntomas:**
- Llenaste formulario
- Clickeaste "Guardar"
- Viste alerta "ok"
- Pero certificación NO aparece en tabla

**Soluciones:**

```
Paso 1: Reload página (F5)
  - A veces toma 1-2 segundos en renderizar
  
Paso 2: Cambiar de página y volver
  - Click sidebar → otra página
  - Click back a "Mis Certificaciones"
  - A veces refresh fuerza actualización
  
Paso 3: Buscar por nombre
  - Si tabla tiene búsqueda
  - Buscar nombre que creaste
  - Si aparece → está allí (problema de filtro)
  
Paso 4: Verificar en SharePoint (admin)
  - Ir a SharePoint list "Certificaciones"
  - Buscar el item
  - Si está en SP pero NO en app → problema de sincronización
  - Contactar tech lead
```

---

## Problemas de Notificaciones

### "No recibo emails de alertas"

**Síntomas:**
- Certificación está próxima a vencer
- Pero no llega email

**Soluciones:**

```
Paso 1: Verificar que hay alerta
  - App → "Alertas"
  - ¿Muestra la alerta en tabla?
  - Si NO → sin alerta, sin email
  
Paso 2: Verificar spam/junk
  - Revisar carpeta "Spam" o "Junk"
  - El email podría estar allí
  - Si lo encuentras → marcar como "Not spam"
  
Paso 3: Verificar email registrado
  - Admin verifica:
    ☑ Empleado en lista "Colaboradores"
    ☑ Email correcto en campo
    ☑ No hay typos
  
Paso 4: Verificar Power Automate
  - (Admin) Power Automate → Flow
  - ¿Está habilitado?
  - ¿Última ejecución fue exitosa?
  - Si error → revisar detalles
  
Paso 5: Waiting for next scheduled run
  - Flows se ejecutan en horarios programados (ej: 11 PM)
  - Si alerta se creó hoy → tomar mañana para email
```

---

### "Email de alerta llegó pero con datos incorrectos"

**Síntomas:**
- Email dice "Cert X vence" pero debería ser "Cert Y"
- O fecha es incorrecta

**Soluciones:**

```
1. Verificar app directamente
   - App muestra datos correctos?
   - Si SÍ → problema en Power Automate
   - Si NO → problema en datos
   
2. Si problema en app:
   - Editar certificación
   - Cambiar a datos correctos
   - Guardar
   - Próximo email (mañana) tendrá datos correctos
   
3. Si problema en Power Automate:
   - Admin debe revisar flow
   - Posible: Mapeo incorrecto de campos
   - Contactar tech lead para debug
```

---

## Problemas de Performance

### "Tabla/App muy lenta, freezea mucho"

**Síntomas:**
- Tabla carga lentamente (> 3 segundos)
- Click en botón no responde inmediatamente
- Navegación entre páginas es lenta

**Soluciones:**

```
Paso 1: Verificar internet
  - Test speed: speedtest.net
  - Esperar si red congestionada
  
Paso 2: Cerrar otras pestañas
  - Cada pestaña usa memoria
  - Muchas pestañas = app lenta
  - Cerrar 10+ pestañas abiertas
  
Paso 3: Resetear datos locales
  - DevTools (F12) → Application
  - Clear LocalStorage
  - Clear SessionStorage
  - Reload página
  
Paso 4: Cambiar navegador
  - ¿Chrome lento? → Intenta Edge
  - Firefox a veces más rápido
  
Paso 5: Contactar admin
  - Si persiste incluso con internet rápido
  - Posible: Tabla SP con demasiados items
  - Posible: Problema de red corporativa
  - Admin puede investigar
```

---

## FAQ General

### P: ¿Qué navegadores soporta?

**R:**
```
Excelente soporte:
  ✅ Chrome (cualquier versión reciente)
  ✅ Edge (cualquier versión reciente)
  ✅ Firefox (cualquier versión reciente)

Soporte limitado:
  ⚠️  Safari (funciona pero puede haber issues)
  ⚠️  IE 11 (funciona muy básico)

No soportado:
  ❌ Internet Explorer 10 o anterior
  ❌ Navegadores muy viejos (pre-2018)
```

---

### P: ¿Qué información me pide la app?

**R:**
```
Información NUNCA pide:
  ❌ Password
  ❌ Credenciales de banco
  ❌ Información personal (CPF, DNI)

Información que SÍ usa:
  ✅ Tu nombre (para saludar)
  ✅ Tu email (para alertas)
  ✅ Tu área de trabajo
  ✅ Tus certificaciones
```

---

### P: ¿Es seguro usar en red WiFi pública?

**R:**
```
Técnicamente funciona, pero NO recomendamos.

Riesgo: En WiFi pública, alguien podría:
  - Ver que estás usando CertTrack
  - Potencialmente ver certificaciones (encrypted pero mejor prevenir)

Mejor:
  ✓ Usar en red corporativa
  ✓ O en home WiFi personal
  ✓ Si unavoidable: usar VPN corporativo
```

---

### P: ¿Puedo acceder desde teléfono?

**R:**
```
Sí, pero experiencia limitada.

Funciona:
  ✓ Ver certificaciones
  ✓ Ver alertas
  ✓ Navegar páginas

No es fácil:
  ⚠️ Tablas pequeñas (scroll horizontal)
  ⚠️ Algunos botones pueden ser difíciles de tocar
  ⚠️ Formularios ajustados pero funcionales

Recomendación:
  ✓ Usar en desktop/tablet si posible
  ✓ Teléfono para consultas rápidas
```

---

### P: ¿Mis datos están seguros?

**R:**
```
Sí, están en SharePoint Online que:
  ✓ Es encriptado (HTTPS)
  ✓ Cumple estándares de seguridad corporativos
  ✓ Tiene backups automáticos
  ✓ Acceso restringido por permisos

Privacidad:
  ✓ Empleados solo ven SUS datos
  ✓ Admins ven todos (por necesidad del rol)
  ✓ No se comparte con terceros
```

---

### P: ¿Quién tiene acceso a mis certificaciones?

**R:**
```
Tienen acceso:
  ✅ TÚ (tu cuenta)
  ✅ Admin de CertTrack (por rol)
  ✅ Tu manager (si admin lo permite)
  
NO tienen acceso:
  ❌ Otros empleados (privacidad)
  ❌ Público (es interno corporativo)
  ❌ Terceros (no se comparte)
```

---

### P: ¿Qué pasa si dejo la empresa?

**R:**
```
Cuando employee sale:
  1. Admin desactiva tu cuenta
  2. Ya no puedes acceder
  3. Tus datos quedan en histórico de SP
  4. Empresa conserva datos 6-12 meses (legal/compliance)
  5. Después se archivan o eliminan según policy
```

---

## Cuándo Contactar Soporte

### Contacta si...

```
❌ CRÍTICO (Respuesta rápida):
  - ¿No puedes acceder en absoluto?
  - ¿Viste datos de otro empleado? (Security issue)
  - ¿App se crashea?
  - Email: soporte@nttdata.com

⚠️  IMPORTANTE (Dentro 1 día):
  - ¿Datos incorrecto en certificación importante?
  - ¿Alerta no funciona 48+ horas?
  - ¿Error consistente en navegador?
  
ℹ️  INFORMACIÓN (Cuando tengas tiempo):
  - ¿Pregunta sobre cómo usar?
  → Primero intenta FAQ-TROUBLESHOOTING
  → Luego guía de usuario correspondiente
  → Si no encontras → contacta
```

### Cómo Reportar

```
Email a: tech-lead@nttdata.com

Incluir:
  1. Descripción clara del problema
  2. Pasos para reproducirlo
  3. Screenshot (F12 → Console si error técnico)
  4. Navegador y version (ej: Chrome 120.0)
  5. Cuándo pasó (fecha/hora)
  
Ejemplo de email bien hecho:
  
  Subject: [CertTrack] Error al crear certificación
  
  Problema:
  - Intento crear certificación
  - Lleno formulario correctamente
  - Click "Guardar"
  - Aparece error rojo: "Invalid date format"
  
  Pasos:
  1. Click "+ Nueva Cert"
  2. Llenar nombre, vendor, etc.
  3. Fecha Vencimiento: 2027-05-15
  4. Click "Guardar"
  → Error!
  
  Info:
  - Navegador: Chrome 120.0
  - Fecha: 2026-05-04 14:30 PM
  - Screenshot adjunto
  
  Gracias,
  [Tu nombre]
```

---

## Resumen Rápido

**Si algo no funciona:**

```
Paso 1: Limpiar cache (Ctrl+Shift+Del)
Paso 2: Reload página (F5)
Paso 3: Revisar esta FAQ
Paso 4: Contactar admin (para empleados)
Paso 5: Si persiste → contactar soporte
```

---

**Última actualización:** 2026-05-04  
**Próxima revisión:** 2026-06-04
