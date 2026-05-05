# 📚 CertTrack IT — Documentación Centralizada

**Bienvenido a la documentación de CertTrack IT.**

Este directorio contiene toda la información técnica, operacional y de deployment para la aplicación.

---

## 🗂️ Estructura de Documentación

### 🚀 Para Começar (Si es tu primera vez)

1. **[DEPLOYMENT.md](./DEPLOYMENT.md)** ⭐ **EMPEZAR AQUÍ**
   - Guía completa de instalación y deployment
   - Paso a paso para demo mode y live mode
   - Testing checklist
   - Troubleshooting

### 📋 Documentación Técnica

2. **[SHAREPOINT-SETUP.md](./SHAREPOINT-SETUP.md)**
   - Crear listas en SharePoint
   - Configurar permisos y grupos Azure AD
   - Cargar datos iniciales
   - Item-level security setup

3. **[POWER-AUTOMATE.md](./POWER-AUTOMATE.md)**
   - Flujos automáticos para alertas por email
   - Reportes semanales
   - Notificaciones de vencimiento

### 📖 Documentación Proyecto

4. **[ARQUITECTURA.md](./ARQUITECTURA.md)** *(próximamente)*
   - Diagrama de componentes
   - Flujo de datos
   - Decisiones arquitectónicas

5. **[CONFIGURACION.md](./CONFIGURACION.md)** *(próximamente)*
   - Variables CFG
   - Modo demo vs live
   - Ambiente setup

---

## 📊 Matriz de Roles

**¿Quién debería leer qué?**

| Rol | Documentos |
|-----|-----------|
| **👨‍💼 Project Owner** | DEPLOYMENT (overview), README |
| **🧑‍💻 Tech Lead** | DEPLOYMENT, SHAREPOINT-SETUP, POWER-AUTOMATE, ARQUITECTURA |
| **☁️ SharePoint Admin** | SHAREPOINT-SETUP, DEPLOYMENT (live mode) |
| **⚙️ Power Automate Admin** | POWER-AUTOMATE |
| **👥 End User (Admin)** | *Próximamente: GUIA-USUARIO-ADMIN.md* |
| **👥 End User (Empleado)** | *Próximamente: GUIA-USUARIO-EMPLEADO.md* |

---

## 🎯 Flujo de Deployment

```
┌─────────────────┐
│ DEPLOYMENT.md   │ ← Lee primero (opciones A/B)
└────────┬────────┘
         │
    ┌────▼─────┐
    │ Opción A  │  (Demo - 15 min)
    │Upload SP  │  → Testing checklist
    └──────────┘
    
    ┌────▼─────┐
    │ Opción B  │  (Live - 4-6 hrs)
    │ Web Part  │  → Sigue SHAREPOINT-SETUP
    └───┬──────┘   → Configura POWER-AUTOMATE
        │          → Testing checklist
        │          → Go live
        │
    ┌───▼──────────┐
    │ Monitoreo    │ (Post-deployment)
    │Alertas, logs │
    └──────────────┘
```

---

## ❓ FAQ Rápida

### P: ¿Por dónde empiezo?

**R:** Lee [DEPLOYMENT.md](./DEPLOYMENT.md) → Escoge Opción A (rápido) o B (producción)

### P: ¿Necesito crear listas en SharePoint?

**R:** 
- **Opción A (demo):** No
- **Opción B (live):** Sí, ver [SHAREPOINT-SETUP.md](./SHAREPOINT-SETUP.md)

### P: ¿Puedo tener emails automáticos?

**R:** Sí, ver [POWER-AUTOMATE.md](./POWER-AUTOMATE.md)

### P: ¿Cómo actualizo la app en futuro?

**R:** 
1. Descargar nueva versión
2. Hacer cambios necesarios
3. Subir a SharePoint (reemplaza archivo)
4. Testing

---

## 📅 Historial & Versioning

| Versión | Fecha | Estado | Cambios |
|---------|-------|--------|---------|
| **1.0** | 2026-05-04 | ✅ Release | Estructura inicial, DEPLOYMENT + SHAREPOINT-SETUP + POWER-AUTOMATE |
| **1.1** | 2026-06-01 | 📋 Planeado | Agregar ARQUITECTURA, CONFIGURACION, guías de usuario |
| **2.0** | 2026-09-01 | 📋 Planeado | Modularización JS (si necesario) |

---

## 🔗 Enlaces Rápidos

- **[Archivo principal: certtrack-it-full.html](../src/certtrack-it-full.html)**
- **[Instructiones proyecto: CLAUDE.md](../CLAUDE.md)**
- **[Carpeta datos demo: /data](../data/)**

---

## 📞 Soporte & Escalation

Si encuentras un problema:

1. **Revisar FAQ en DEPLOYMENT.md** (troubleshooting section)
2. **Buscar error en console (F12)**
3. **Contactar tech lead** (archivo TBD: CONTACTOS.md)

---

## 🚀 Próximas Actualizaciones Documentación

- [ ] ARQUITECTURA.md — Diagrama técnico
- [ ] CONFIGURACION.md — Variables de configuración
- [ ] GUIA-USUARIO-ADMIN.md — Cómo usar como admin
- [ ] GUIA-USUARIO-EMPLEADO.md — Cómo usar como empleado
- [ ] TESTING.md — Test cases detallados
- [ ] TROUBLESHOOTING.md — FAQ extendido
- [ ] CHANGELOG.md — Historial de cambios
- [ ] CONTACTOS.md — Equipo responsable

---

## 💡 Mejores Prácticas

### Mantener Documentación Actualizada

```
1. Si cambias CFG, actualizar CONFIGURACION.md
2. Si agregas flujo SP, actualizar POWER-AUTOMATE.md
3. Si creces a múltiples tenants, actualizar DEPLOYMENT (roadmap SPFx)
4. Cada trimestre: revisar docs, marcar obsoletas
```

### Versionado

```
Dentro de cada documento:
- "Última actualización: [fecha]"
- "Versión del código: [v1.0]"
- Así sabes cuándo actualizar
```

---

## 📄 Licencia & Uso

Esta documentación es **interna de NTT DATA EMEAL**. No distribuir fuera de la organización.

---

**Última actualización:** 2026-05-04  
**Proxima revisión:** 2026-06-04
