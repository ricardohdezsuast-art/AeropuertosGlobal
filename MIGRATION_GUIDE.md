# 🚀 GUÍA PASO A PASO - MIGRACIÓN A REPOSITORIO CORPORATIVO

**Proyecto:** AeropuertosGlobal  
**Origen:** GitHub Personal (hubhernan/AeropuertosGlobal)  
**Destino:** Repositorio Corporativo + SharePoint  
**Fecha:** 2026-07-08

---

## 📋 ÍNDICE

1. [Pre-requisitos](#pre-requisitos)
2. [Paso 1: Limpieza y Seguridad](#paso-1)
3. [Paso 2: Crear Repositorio Corporativo](#paso-2)
4. [Paso 3: Migración del Código](#paso-3)
5. [Paso 4: Configuración de Producción](#paso-4)
6. [Paso 5: Integración con SharePoint](#paso-5)
7. [Paso 6: Pruebas y Validación](#paso-6)
8. [Paso 7: Documentación y Entrega](#paso-7)

---

## ✅ PRE-REQUISITOS {#pre-requisitos}

### **Accesos y Permisos**

- [ ] Cuenta de GitHub corporativa con permisos de creación de repos
- [ ] Acceso a SharePoint corporativo (editor/propietario del sitio)
- [ ] Credenciales de base de datos PostgreSQL corporativa
- [ ] Acceso a Azure Portal / Render.com (si se usa)

### **Herramientas Instaladas**

- [ ] Git 2.40+
- [ ] Python 3.12+
- [ ] PostgreSQL 16+ con PostGIS
- [ ] VS Code o IDE de preferencia
- [ ] Azure CLI (si se usa Azure)

### **Revisión de Archivos**

- [ ] Leer [SECURITY_AUDIT.md](SECURITY_AUDIT.md) completo
- [ ] Revisar [README.md](README.md) actualizado
- [ ] Revisar [SHAREPOINT_INTEGRATION.md](SHAREPOINT_INTEGRATION.md)
- [ ] Revisar [ROUTE_VALIDATION.md](ROUTE_VALIDATION.md)

---

## 🔒 PASO 1: Limpieza y Seguridad {#paso-1}

### **1.1 Verificar Archivos Sensibles**

Ejecutar en PowerShell:

```powershell
cd C:\Users\Ricardo.Hernandez\AeropuertosGlobal

# Verificar que NO existan archivos sensibles
Get-ChildItem -Recurse -Include *.env,*.key,*.pem

# Verificar que la carpeta de datos esté ignorada
git status | Select-String "Aeropuertos/"
# Si aparece, es un problema. Debe estar en .gitignore
```

**Resultado esperado:** ✅ No debe aparecer ningún archivo `.env` ni carpeta `Aeropuertos/`

### **1.2 Revisar Historial de Git**

```powershell
# Buscar credenciales en commits históricos
git log --all --full-history --source -S 'password' -S 'secret' -S 'api_key'

# Buscar archivos eliminados que puedan tener credenciales
git log --all --full-history -- **/*.env
```

**⚠️ SI SE ENCUENTRAN CREDENCIALES EN EL HISTORIAL:**

```powershell
# OPCIÓN A: Limpiar historial (destructivo, coordinar con equipo)
git filter-branch --force --index-filter `
  "git rm -rf --cached --ignore-unmatch Aeropuertos/PNR3_Jan-June_2026.xlsx" `
  --prune-empty --tag-name-filter cat -- --all

# OPCIÓN B: Crear repo nuevo sin historial (más seguro)
# Ver paso 3.2
```

### **1.3 Actualizar .gitignore**

```powershell
# El .gitignore ya fue actualizado en pasos anteriores
# Verificar que incluya:
Get-Content .gitignore | Select-String "Aeropuertos/","\.env","\*.xlsx"
```

**Resultado esperado:**
```
Aeropuertos/
.env
*.xlsx
```

### **1.4 Generar Nueva SECRET_KEY**

```powershell
# Generar nueva clave para producción
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

**Guardar esta clave de forma segura** (Azure Key Vault, password manager corporativo, etc.)

---

## 🏢 PASO 2: Crear Repositorio Corporativo {#paso-2}

### **2.1 Crear Repositorio en GitHub Corporativo**

1. Ir a https://github.com/EMPRESA
2. Click en **New repository**
3. Configurar:
   - **Name:** `AeropuertosGlobal`
   - **Description:** `Sistema de análisis geoespacial de datos PNR3 - Uso corporativo interno`
   - **Visibility:** 🔒 **Private** (IMPORTANTE)
   - **Initialize:** ❌ **NO** marcar "Add README" (ya lo tenemos)
4. Click **Create repository**

### **2.2 Anotar URL del Repo**

```
https://github.com/EMPRESA/AeropuertosGlobal.git
```

---

## 📤 PASO 3: Migración del Código {#paso-3}

### **OPCIÓN A: Migración con Historial (Recomendado si el historial está limpio)**

```powershell
cd C:\Users\Ricardo.Hernandez\AeropuertosGlobal

# Verificar estado actual
git status
git log --oneline | Select-Object -First 10

# Agregar remote corporativo
git remote add corporativo https://github.com/EMPRESA/AeropuertosGlobal.git

# Verificar remotes
git remote -v

# Push al repositorio corporativo
git push corporativo main

# O si la rama se llama master:
# git push corporativo master
```

### **OPCIÓN B: Migración SIN Historial (Recomendado si hay credenciales en historial)**

```powershell
cd C:\Users\Ricardo.Hernandez\AeropuertosGlobal

# Eliminar carpeta .git (elimina todo el historial)
Remove-Item -Recurse -Force .git

# Inicializar nuevo repositorio limpio
git init

# Configurar usuario
git config user.name "Tu Nombre Corporativo"
git config user.email "tu.email@empresa.com"

# Agregar todos los archivos (respetando .gitignore)
git add .

# Verificar qué se va a commitear
git status

# ⚠️ IMPORTANTE: Verificar que NO aparezcan:
# - Carpeta Aeropuertos/
# - Archivos .env
# - Archivos .xlsx
# Si aparecen, revisar .gitignore

# Crear commit inicial
git commit -m "Initial commit: Proyecto AeropuertosGlobal migrado a repositorio corporativo

- Sistema de análisis geoespacial de datos PNR3
- Django 5.1.4 + GeoDjango + PostGIS
- MapLibre GL JS para visualización
- API REST completa
- Documentación corporativa incluida
"

# Conectar con repositorio corporativo
git remote add origin https://github.com/EMPRESA/AeropuertosGlobal.git

# Push
git branch -M main
git push -u origin main
```

### **3.1 Verificación Post-Push**

```powershell
# Verificar en navegador:
# https://github.com/EMPRESA/AeropuertosGlobal

# Debe aparecer:
# ✅ README.md
# ✅ SECURITY_AUDIT.md
# ✅ SHAREPOINT_INTEGRATION.md
# ✅ .gitignore
# ✅ .env.example
# ✅ requirements.txt
# ✅ Dockerfile
# ❌ NO debe aparecer: Aeropuertos/, .env, *.xlsx
```

---

## ⚙️ PASO 4: Configuración de Producción {#paso-4}

### **4.1 Obtener MapTiler API Key Corporativa**

1. Ir a https://cloud.maptiler.com/
2. Crear cuenta corporativa o usar existente
3. Ir a **Account** → **Keys**
4. Crear nueva key: `AeropuertosGlobal-Produccion`
5. **Restricciones:** Agregar dominios permitidos:
   - `https://empresa.sharepoint.com`
   - `https://aeropuertos.azurewebsites.net` (si aplica)
6. Guardar key de forma segura

### **4.2 Configurar Base de Datos PostgreSQL**

**Opción A: Azure Database for PostgreSQL**

```bash
# Crear servidor PostgreSQL
az postgres flexible-server create \
  --name aeropuertos-db \
  --resource-group AeropuertosRG \
  --location centralus \
  --admin-user dbadmin \
  --admin-password "Password123!" \
  --sku-name Standard_B1ms \
  --tier Burstable \
  --version 16

# Habilitar PostGIS
az postgres flexible-server parameter set \
  --resource-group AeropuertosRG \
  --server-name aeropuertos-db \
  --name azure.extensions \
  --value "POSTGIS,POSTGIS_TOPOLOGY"

# Crear base de datos
az postgres flexible-server db create \
  --resource-group AeropuertosRG \
  --server-name aeropuertos-db \
  --database-name aeropuertos_db
```

**Opción B: Render.com PostgreSQL (Gratuito)**

1. Ir a https://dashboard.render.com/
2. **New** → **PostgreSQL**
3. Nombre: `aeropuertos-db`
4. Plan: **Free**
5. **Create Database**
6. Copiar **Internal Database URL**

**Opción C: PostgreSQL Corporativo Existente**

Contactar al DBA corporativo para:
- Crear base de datos `aeropuertos_db`
- Habilitar extensión PostGIS
- Obtener credenciales

### **4.3 Desplegar Backend Django**

**Opción A: Render.com (Rápido, Free Tier disponible)**

```bash
# El archivo render.yaml ya está configurado
# Solo necesitas:
1. Ir a https://dashboard.render.com/
2. **New** → **Blueprint**
3. Conectar repositorio: EMPRESA/AeropuertosGlobal
4. Configurar variables de entorno en el dashboard:
   - DJANGO_SECRET_KEY=<clave-generada-en-paso-1.4>
   - MAPTILER_API_KEY=<clave-obtenida-en-paso-4.1>
   - DATABASE_URL=<se-genera-automáticamente>
5. **Apply**
```

**Opción B: Azure App Service**

```bash
# Crear App Service
az webapp create \
  --resource-group AeropuertosRG \
  --plan AeropuertosPlan \
  --name aeropuertos-global \
  --runtime "PYTHON:3.12" \
  --deployment-container-image-name DOCKER|aeropuertos:latest

# Configurar variables de entorno
az webapp config appsettings set \
  --resource-group AeropuertosRG \
  --name aeropuertos-global \
  --settings \
    DJANGO_SECRET_KEY="<clave>" \
    MAPTILER_API_KEY="<clave>" \
    DATABASE_URL="<url-bd>" \
    DEBUG="False" \
    ALLOWED_HOSTS="aeropuertos-global.azurewebsites.net"

# Desplegar
az webapp deployment source config \
  --resource-group AeropuertosRG \
  --name aeropuertos-global \
  --repo-url https://github.com/EMPRESA/AeropuertosGlobal \
  --branch main \
  --manual-integration
```

### **4.4 Cargar Datos Iniciales**

```powershell
# Conectarse al servidor de producción vía SSH/Azure CLI

# Activar entorno virtual
source venv/bin/activate  # Linux/Mac
venv\Scripts\activate     # Windows

# Ejecutar migraciones
python manage.py migrate

# Crear superusuario
python manage.py createsuperuser

# Cargar datos de aeropuertos (si tienes fixture)
python manage.py loaddata aeropuertos_catalog.json

# Cargar datos PNR (desde archivo Excel local, NO subir al repo)
# Subir el Excel temporalmente vía FTP/SCP
python manage.py cargar_pnr PNR3_Jan-June_2026.xlsx

# Recolectar archivos estáticos
python manage.py collectstatic --noinput

# Eliminar el Excel del servidor
rm PNR3_Jan-June_2026.xlsx
```

---

## 🌐 PASO 5: Integración con SharePoint {#paso-5}

### **5.1 Configurar Django para SharePoint**

Editar `aeropuertos_project/aeropuertos_project/settings.py` en el servidor:

```python
# Permitir iframe desde SharePoint
X_FRAME_OPTIONS = 'ALLOW-FROM https://empresa.sharepoint.com'

# Configurar CORS (instalar: pip install django-cors-headers)
INSTALLED_APPS += ['corsheaders']
MIDDLEWARE = ['corsheaders.middleware.CorsMiddleware'] + MIDDLEWARE

CORS_ALLOWED_ORIGINS = [
    'https://empresa.sharepoint.com',
    'https://empresa-my.sharepoint.com',
]

# Forzar HTTPS
SECURE_SSL_REDIRECT = True
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True
```

Redeploy:

```bash
git add aeropuertos_project/aeropuertos_project/settings.py
git commit -m "Configurar CORS y X-Frame-Options para SharePoint"
git push origin main
```

### **5.2 Crear Página en SharePoint**

1. Ir al sitio de SharePoint donde se publicará
2. **New** → **Page**
3. Título: `Análisis de Tráfico Aéreo PNR3`
4. **Add a web part** → **Embed**
5. Pegar código:

```html
<div style="width: 100%; height: 100vh; min-height: 900px;">
  <iframe 
    src="https://aeropuertos-global.azurewebsites.net/"
    width="100%" 
    height="100%"
    style="border: none; border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.1);"
    frameborder="0"
    allowfullscreen
    sandbox="allow-scripts allow-same-origin allow-forms allow-popups"
    title="Mapa Interactivo de Aeropuertos Globales - PNR3 H1 2026">
  </iframe>
</div>
```

6. **Publish**

### **5.3 Configurar Permisos**

1. Click en **⚙️** (Settings) → **Site permissions**
2. Configurar según necesidad:
   - **Privado:** Solo miembros autorizados
   - **Público (interno):** Todos los empleados
3. Agregar grupos/usuarios

---

## ✅ PASO 6: Pruebas y Validación {#paso-6}

### **6.1 Pruebas Funcionales**

Checklist de validación:

- [ ] **Backend Django:**
  - [ ] `/` carga correctamente
  - [ ] `/dashboard/` carga correctamente
  - [ ] `/admin/` accesible con credenciales
  - [ ] `/api/pnr/resumen/` devuelve JSON válido
  - [ ] `/api/aeropuertos/geojson/` devuelve GeoJSON

- [ ] **Mapa Interactivo:**
  - [ ] Mapa base se carga (MapTiler)
  - [ ] Aeropuertos se visualizan
  - [ ] Rutas se dibujan correctamente
  - [ ] Click en aeropuerto abre panel de detalle
  - [ ] Filtros por mes funcionan
  - [ ] Búsqueda de aeropuertos funciona
  - [ ] Dashboard alterna correctamente

- [ ] **SharePoint:**
  - [ ] Iframe carga sin errores
  - [ ] No hay errores de CORS en console (F12)
  - [ ] No hay errores de X-Frame-Options
  - [ ] Todas las interacciones funcionan dentro del iframe
  - [ ] Responsive en diferentes tamaños de pantalla

### **6.2 Pruebas de Seguridad**

```powershell
# Verificar que credenciales NO estén expuestas
Invoke-WebRequest -Uri "https://aeropuertos-global.azurewebsites.net/" | Select-String "password","secret","api_key"
# No debe encontrar nada

# Verificar HTTPS
Invoke-WebRequest -Uri "http://aeropuertos-global.azurewebsites.net/"
# Debe redirigir a HTTPS

# Verificar headers de seguridad
(Invoke-WebRequest -Uri "https://aeropuertos-global.azurewebsites.net/").Headers
# Debe incluir: X-Frame-Options, X-Content-Type-Options, Strict-Transport-Security
```

### **6.3 Pruebas de Rendimiento**

- [ ] Tiempo de carga inicial < 3 segundos
- [ ] Interacciones fluidas (60 FPS)
- [ ] API responde < 1 segundo
- [ ] Filtros actualizan < 500ms

### **6.4 Pruebas de Compatibilidad**

Probar en:
- [ ] Chrome/Edge (Chromium)
- [ ] Firefox
- [ ] Safari (si hay usuarios Mac)
- [ ] Dispositivos móviles (responsive)

---

## 📚 PASO 7: Documentación y Entrega {#paso-7}

### **7.1 Documentación de Usuario Final**

Crear en SharePoint o Wiki corporativa:

```markdown
# Manual de Usuario - Mapa de Aeropuertos Global

## Acceso
URL: https://empresa.sharepoint.com/sites/Analytics/AeropuertosGlobal

## Funcionalidades
1. **Visualizar rutas aéreas**: Click en botones de filtro de mes
2. **Buscar aeropuerto**: Usar barra de búsqueda (IATA, ciudad, país)
3. **Ver detalles**: Click en cualquier aeropuerto del mapa
4. **Dashboard**: Click en botón "VER DASHBOARD" para gráficas avanzadas

## Soporte
Contactar a: soporte-ti@empresa.com
```

### **7.2 Documentación Técnica**

En el repositorio GitHub, verificar que existan:

- [x] [README.md](README.md) - Documentación principal
- [x] [SECURITY_AUDIT.md](SECURITY_AUDIT.md) - Auditoría de seguridad
- [x] [SHAREPOINT_INTEGRATION.md](SHAREPOINT_INTEGRATION.md) - Guía de SharePoint
- [x] [ROUTE_VALIDATION.md](ROUTE_VALIDATION.md) - Validación de rutas
- [x] [.env.example](.env.example) - Plantilla de configuración
- [ ] CHANGELOG.md (crear con historial de versiones)
- [ ] CONTRIBUTING.md (si se permite contribución interna)

### **7.3 Transferencia de Conocimiento**

Organizar sesión con equipo de TI/Desarrollo:

1. **Arquitectura del sistema** (30 min)
2. **Flujo de datos** (15 min)
3. **Procedimientos de deployment** (20 min)
4. **Troubleshooting común** (15 min)
5. **Q&A** (20 min)

### **7.4 Plan de Mantenimiento**

Documentar en Wiki corporativa:

```markdown
# Plan de Mantenimiento - AeropuertosGlobal

## Actualizaciones de Datos
- **Frecuencia:** Mensual (cuando se reciban nuevos PNR)
- **Responsable:** Analista de Datos
- **Proceso:**
  1. Recibir Excel de PNR
  2. Conectarse al servidor de producción
  3. Ejecutar: `python manage.py cargar_pnr archivo.xlsx`
  4. Verificar en `/admin/` que los datos se cargaron
  5. Eliminar Excel del servidor

## Backups de Base de Datos
- **Frecuencia:** Diario (automático)
- **Retención:** 30 días
- **Responsable:** DBA Corporativo
- **Comando manual:**
  ```bash
  pg_dump -h host -U user aeropuertos_db > backup_$(date +%Y%m%d).sql
  ```

## Actualizaciones de Código
- **Frecuencia:** Según necesidad
- **Proceso:**
  1. Crear branch en GitHub: `feature/nueva-funcionalidad`
  2. Desarrollar y probar localmente
  3. Pull Request → Revisión de código
  4. Merge a `main`
  5. Deploy automático o manual según configuración

## Monitoreo
- **URL de salud:** `/admin/` (verificar que carga)
- **Logs:** Azure Portal / Render Dashboard
- **Alertas:** Configurar en Azure Monitor (uptime, errores 500)
```

---

## 🎉 CHECKLIST FINAL

Antes de dar por completada la migración:

### **Repositorio**
- [ ] Código migrado a GitHub corporativo
- [ ] Repositorio marcado como **Private**
- [ ] Sin archivos sensibles en commits
- [ ] README.md completo y actualizado
- [ ] .gitignore configurado correctamente
- [ ] .env.example incluido
- [ ] Documentación de seguridad incluida

### **Backend en Producción**
- [ ] Django desplegado y accesible vía HTTPS
- [ ] Base de datos PostgreSQL configurada
- [ ] PostGIS habilitado
- [ ] Variables de entorno configuradas
- [ ] Migraciones ejecutadas
- [ ] Datos iniciales cargados
- [ ] Archivos estáticos recolectados
- [ ] Superusuario creado

### **SharePoint**
- [ ] Página creada
- [ ] Iframe funcional
- [ ] Sin errores de CORS
- [ ] Sin errores de X-Frame-Options
- [ ] Permisos configurados
- [ ] Pruebas en diferentes navegadores

### **Seguridad**
- [ ] DJANGO_SECRET_KEY única y segura
- [ ] DEBUG=False en producción
- [ ] ALLOWED_HOSTS configurado
- [ ] HTTPS forzado
- [ ] API keys protegidas
- [ ] No hay credenciales hardcodeadas
- [ ] Datos sensibles NO en repositorio

### **Documentación**
- [ ] Manual de usuario creado
- [ ] Documentación técnica completa
- [ ] Plan de mantenimiento documentado
- [ ] Transferencia de conocimiento realizada
- [ ] Contactos de soporte definidos

---

## 📞 SOPORTE POST-MIGRACIÓN

### **Contactos**

| Rol | Contacto | Responsabilidad |
|-----|----------|-----------------|
| Desarrollador | desarrollo@empresa.com | Código y funcionalidad |
| DBA | dba@empresa.com | Base de datos |
| DevOps | devops@empresa.com | Infraestructura y deployment |
| Seguridad | seguridad@empresa.com | Auditorías y compliance |
| Usuario Final | soporte-ti@empresa.com | Soporte de primer nivel |

### **Recursos**

- **Repositorio:** https://github.com/EMPRESA/AeropuertosGlobal
- **SharePoint:** https://empresa.sharepoint.com/sites/Analytics/AeropuertosGlobal
- **Backend:** https://aeropuertos-global.azurewebsites.net/
- **Wiki Interna:** https://empresa.sharepoint.com/wiki/AeropuertosGlobal

---

## 🎊 ¡MIGRACIÓN COMPLETADA!

Si todos los checkpoints están marcados, la migración ha sido exitosa.

**Próximos Pasos:**
1. Anunciar disponibilidad a usuarios finales
2. Monitorear logs durante la primera semana
3. Recolectar feedback de usuarios
4. Planear mejoras futuras

---

**Documento creado:** 2026-07-08  
**Última actualización:** 2026-07-08  
**Versión:** 1.0  
**Autor:** Especialista en Desarrollo y Seguridad
