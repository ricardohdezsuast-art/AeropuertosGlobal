# ✅ CHECKLIST RÁPIDO - MIGRACIÓN AEROPUERTOSGLOBAL

**Usa esta lista para verificar cada paso de la migración**

---

## 🔒 FASE 1: SEGURIDAD (30 min)

```
┌─────────────────────────────────────────────────┐
│ VERIFICACIÓN DE ARCHIVOS SENSIBLES             │
└─────────────────────────────────────────────────┘

□ Verificar que Aeropuertos/ está en .gitignore
□ Verificar que .env está en .gitignore
□ Buscar credenciales en historial de Git
□ Generar nueva DJANGO_SECRET_KEY
   Comando: python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
□ Guardar SECRET_KEY de forma segura
□ Obtener MAPTILER_API_KEY corporativa
```

---

## 🏢 FASE 2: REPOSITORIO (15 min)

```
┌─────────────────────────────────────────────────┐
│ CREAR REPOSITORIO CORPORATIVO                   │
└─────────────────────────────────────────────────┘

□ Ir a https://github.com/EMPRESA
□ Crear nuevo repositorio
   Nombre: AeropuertosGlobal
   Visibilidad: ★ PRIVATE ★
   NO inicializar con README
□ Copiar URL del repositorio
□ Anotar URL: https://github.com/EMPRESA/AeropuertosGlobal.git
```

---

## 📤 FASE 3: MIGRACIÓN DE CÓDIGO (20 min)

### **Opción A: Con historial (si está limpio)**

```powershell
cd C:\Users\Ricardo.Hernandez\AeropuertosGlobal
git remote add corporativo https://github.com/EMPRESA/AeropuertosGlobal.git
git push corporativo main
```

```
□ Ejecutar comandos de arriba
□ Verificar en GitHub que el código se subió
□ Verificar que NO aparezca: Aeropuertos/, .env, *.xlsx
```

### **Opción B: Sin historial (si hay credenciales históricas)**

```powershell
cd C:\Users\Ricardo.Hernandez\AeropuertosGlobal
Remove-Item -Recurse -Force .git
git init
git add .
git commit -m "Initial commit: Proyecto AeropuertosGlobal migrado"
git remote add origin https://github.com/EMPRESA/AeropuertosGlobal.git
git branch -M main
git push -u origin main
```

```
□ Ejecutar comandos de arriba
□ Verificar en GitHub
□ Confirmar que NO hay archivos sensibles
```

---

## ⚙️ FASE 4: INFRAESTRUCTURA (2-4 horas)

### **Opción A: Render.com** ⭐ Recomendado para inicio rápido

```
┌─────────────────────────────────────────────────┐
│ RENDER.COM SETUP                                │
└─────────────────────────────────────────────────┘

□ Ir a https://dashboard.render.com/
□ New → Blueprint
□ Conectar repositorio: EMPRESA/AeropuertosGlobal
□ Configurar variables de entorno:
   □ DJANGO_SECRET_KEY=<tu-clave-generada>
   □ MAPTILER_API_KEY=<tu-clave-maptiler>
   □ (DATABASE_URL se genera automáticamente)
□ Click "Apply"
□ Esperar deployment (5-10 min)
□ Obtener URL: https://aeropuertos-web.onrender.com
□ Verificar que carga: /admin/
```

### **Opción B: Azure App Service** 💼 Más corporativo

```
□ Crear Resource Group
□ Crear Azure Database for PostgreSQL
□ Habilitar PostGIS
□ Crear Azure App Service (Python 3.12)
□ Configurar variables de entorno
□ Conectar con GitHub para CI/CD
□ Verificar deployment
```

---

## 💾 FASE 5: BASE DE DATOS (1-2 horas)

```
┌─────────────────────────────────────────────────┐
│ CONFIGURAR POSTGRESQL + POSTGIS                 │
└─────────────────────────────────────────────────┘

□ Conectarse al servidor de producción (SSH/Cloud Shell)
□ Ejecutar migraciones:
   python manage.py migrate
□ Crear superusuario:
   python manage.py createsuperuser
□ Cargar catálogo de aeropuertos:
   python manage.py loaddata aeropuertos_catalog.json
□ Subir Excel PNR temporalmente (NO al repo)
□ Cargar datos PNR:
   python manage.py cargar_pnr PNR3_Jan-June_2026.xlsx
□ Eliminar Excel del servidor
□ Recolectar archivos estáticos:
   python manage.py collectstatic --noinput
```

---

## 🌐 FASE 6: SHAREPOINT (1-2 horas)

```
┌─────────────────────────────────────────────────┐
│ INTEGRACIÓN CON SHAREPOINT                      │
└─────────────────────────────────────────────────┘

PASO 1: CONFIGURAR DJANGO
□ Editar settings.py:
   X_FRAME_OPTIONS = 'ALLOW-FROM https://empresa.sharepoint.com'
□ Instalar django-cors-headers
□ Configurar CORS_ALLOWED_ORIGINS
□ Commit y push

PASO 2: CREAR PÁGINA SHAREPOINT
□ Ir a sitio de SharePoint
□ New → Page
□ Título: "Análisis de Tráfico Aéreo PNR3"
□ Add web part → Embed
□ Pegar código iframe (ver SHAREPOINT_INTEGRATION.md)
□ Publicar página

PASO 3: CONFIGURAR PERMISOS
□ Configurar acceso (privado/público interno)
□ Agregar usuarios/grupos autorizados
```

---

## ✅ FASE 7: PRUEBAS (2-3 horas)

```
┌─────────────────────────────────────────────────┐
│ VALIDACIÓN FUNCIONAL                            │
└─────────────────────────────────────────────────┘

BACKEND
□ https://tu-app.com/ carga
□ https://tu-app.com/dashboard/ carga
□ https://tu-app.com/admin/ accesible
□ https://tu-app.com/api/pnr/resumen/ devuelve JSON

MAPA
□ Mapa base se visualiza (MapTiler)
□ Aeropuertos aparecen en el mapa
□ Rutas se dibujan
□ Click en aeropuerto abre detalle
□ Filtros por mes funcionan
□ Búsqueda funciona
□ Botón dashboard funciona

SHAREPOINT
□ Iframe carga sin errores
□ Abrir consola (F12) → Sin errores CORS
□ Sin errores X-Frame-Options
□ Todas las interacciones funcionan
□ Responsive (probar en móvil)

SEGURIDAD
□ No hay credenciales expuestas en código fuente
□ HTTPS activo (candado verde en navegador)
□ DEBUG=False en producción
```

---

## 📚 FASE 8: DOCUMENTACIÓN (30 min)

```
┌─────────────────────────────────────────────────┐
│ CREAR DOCUMENTACIÓN DE USUARIO                  │
└─────────────────────────────────────────────────┘

□ Crear página en Wiki corporativa
□ Incluir:
   □ URL de acceso
   □ Guía de uso básica
   □ Capturas de pantalla
   □ Contacto de soporte
□ Compartir con usuarios finales
□ Organizar sesión de capacitación (opcional)
```

---

## 🎉 FASE 9: LANZAMIENTO

```
┌─────────────────────────────────────────────────┐
│ GO LIVE                                         │
└─────────────────────────────────────────────────┘

□ Todas las fases anteriores completadas
□ Anuncio a usuarios finales
□ Monitorear logs primera semana
□ Recolectar feedback
□ Documentar issues (si hay)
□ Planear mejoras futuras
```

---

## 🆘 TROUBLESHOOTING RÁPIDO

### **Problema: Iframe en blanco**
```
1. Abrir consola (F12)
2. Buscar errores
3. Verificar X-Frame-Options en settings.py
4. Verificar URL es HTTPS
```

### **Problema: Mapa no carga**
```
1. Verificar MAPTILER_API_KEY configurada
2. Revisar límites de uso en MapTiler dashboard
3. Verificar dominio autorizado en MapTiler
```

### **Problema: API no responde**
```
1. Verificar backend está corriendo
2. Verificar DATABASE_URL configurada
3. Revisar logs del servidor
4. Ejecutar migraciones si falta
```

### **Problema: CORS errors**
```
1. Instalar django-cors-headers
2. Agregar 'corsheaders' a INSTALLED_APPS
3. Agregar middleware CORS
4. Configurar CORS_ALLOWED_ORIGINS
5. Redeploy
```

---

## 📞 AYUDA

| Necesitas ayuda con... | Consultar |
|------------------------|-----------|
| Seguridad | [SECURITY_AUDIT.md](SECURITY_AUDIT.md) |
| Migración paso a paso | [MIGRATION_GUIDE.md](MIGRATION_GUIDE.md) |
| SharePoint | [SHAREPOINT_INTEGRATION.md](SHAREPOINT_INTEGRATION.md) |
| Arquitectura general | [README.md](README.md) |
| Rutas y portabilidad | [ROUTE_VALIDATION.md](ROUTE_VALIDATION.md) |
| Resumen ejecutivo | [RESUMEN_EJECUTIVO.md](RESUMEN_EJECUTIVO.md) |

---

## ⏱️ TIEMPO ESTIMADO TOTAL

```
┌─────────────────────────────────────────────────┐
│ DESGLOSE DE TIEMPO                              │
└─────────────────────────────────────────────────┘

Seguridad:             30 min  ████
Repositorio:           15 min  ██
Migración código:      20 min  ███
Infraestructura:    2-4 hrs    ████████████████████
Base de datos:      1-2 hrs    ██████████
SharePoint:         1-2 hrs    ██████████
Pruebas:            2-3 hrs    ████████████████
Documentación:      30 min     ████

TOTAL:              7-15 hrs   (1-2 días de trabajo)
```

---

## 🎯 PRIORIDADES

### **CRÍTICO (Hacer primero)**
1. ✅ Verificar seguridad (no hay credenciales)
2. ✅ Migrar código a GitHub corporativo
3. ⚠️ Configurar backend en producción

### **IMPORTANTE**
4. ⚠️ Cargar datos en base de datos
5. ⚠️ Integrar con SharePoint
6. ⚠️ Pruebas completas

### **OPCIONAL**
7. ⏳ Documentación de usuario
8. ⏳ Capacitación
9. ⏳ Monitoreo avanzado

---

**Última actualización:** 2026-07-08  
**Versión:** 1.0  

✅ **Marca cada checkbox conforme completes las tareas**
