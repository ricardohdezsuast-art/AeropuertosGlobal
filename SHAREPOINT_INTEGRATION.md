# 📤 GUÍA DE INTEGRACIÓN CON SHAREPOINT

**Proyecto:** AeropuertosGlobal  
**Fecha:** 2026-07-08  
**Versión:** 1.0

---

## 📋 Índice

1. [Opciones de Integración](#opciones)
2. [Método 1: Iframe (Recomendado)](#metodo-1-iframe)
3. [Método 2: HTML Estático](#metodo-2-html-estatico)
4. [Método 3: Azure Static Web Apps + Iframe](#metodo-3-azure)
5. [Configuración de Seguridad](#seguridad)
6. [Troubleshooting](#troubleshooting)

---

## 🎯 Opciones de Integración {#opciones}

| Método | Complejidad | Ventajas | Desventajas |
|--------|-------------|----------|-------------|
| **Iframe** | 🟢 Baja | Fácil, mantiene funcionalidad completa | Requiere backend activo |
| **HTML Estático** | 🟡 Media | No requiere backend | Funcionalidad limitada (sin API Django) |
| **Azure + Iframe** | 🟠 Alta | Profesional, escalable | Requiere configuración cloud |

---

## 🖼️ MÉTODO 1: Iframe (Recomendado) {#metodo-1-iframe}

### **Descripción**

Incrusta la aplicación completa de Django mediante un iframe en una página de SharePoint.

### **Requisitos**

- ✅ Backend Django desplegado y accesible (Render, Azure, servidor corporativo)
- ✅ URL pública o accesible desde la red corporativa
- ✅ Certificado SSL (HTTPS) configurado

### **Pasos de Implementación**

#### **1. Desplegar el Backend Django**

**Opción A: Render.com (Gratuito)**

```bash
# El archivo render.yaml ya está configurado
# Ir a https://render.com y conectar el repositorio de GitHub
# Configurar las variables de entorno en el dashboard de Render:
# - DJANGO_SECRET_KEY
# - MAPTILER_API_KEY
# - DATABASE_URL (se genera automáticamente)
```

**Opción B: Azure Web App**

```bash
# Crear Azure Web App con PostgreSQL
az webapp create --resource-group AeropuertosRG \
  --plan AeropuertosPlan --name aeropuertos-global \
  --runtime "PYTHON:3.12"

# Configurar variables de entorno
az webapp config appsettings set --resource-group AeropuertosRG \
  --name aeropuertos-global \
  --settings DJANGO_SECRET_KEY="tu-clave" \
             MAPTILER_API_KEY="tu-clave" \
             DATABASE_URL="postgresql://..."
```

**Opción C: Servidor Corporativo IIS**

Ver documentación de despliegue Django en IIS (fuera del alcance de esta guía).

#### **2. Configurar Django para SharePoint**

Editar [aeropuertos_project/aeropuertos_project/settings.py](aeropuertos_project/aeropuertos_project/settings.py):

```python
# Permitir que SharePoint incruste la app en iframe
X_FRAME_OPTIONS = 'ALLOW-FROM https://empresa.sharepoint.com'

# O usar CSP más específico
SECURE_CONTENT_SECURITY_POLICY = {
    'frame-ancestors': ['https://empresa.sharepoint.com', 'https://*.sharepoint.com']
}

# Configurar CORS si la API se consume desde SharePoint
CORS_ALLOWED_ORIGINS = [
    'https://empresa.sharepoint.com',
    'https://empresa-my.sharepoint.com',
]

# Instalar django-cors-headers si no está:
# pip install django-cors-headers
# Agregar 'corsheaders' a INSTALLED_APPS
# Agregar 'corsheaders.middleware.CorsMiddleware' a MIDDLEWARE (al inicio)
```

#### **3. Crear Página en SharePoint**

1. Ir a tu sitio de SharePoint
2. Crear nueva página (Página → Nueva)
3. Agregar Web Part → **Embed**
4. Pegar el siguiente código:

```html
<iframe 
  src="https://tu-app.onrender.com/"
  width="100%" 
  height="900px"
  frameborder="0"
  allowfullscreen
  sandbox="allow-scripts allow-same-origin allow-forms allow-popups"
  title="Mapa de Aeropuertos Global">
</iframe>
```

**Ajustes recomendados:**
- **width**: `100%` (ocupa todo el ancho)
- **height**: `900px` o más (ajustar según necesidad)
- **sandbox**: Configurar permisos necesarios
- **title**: Para accesibilidad

#### **4. Publicar y Probar**

- Guardar la página
- Publicar
- Verificar que el mapa carga correctamente
- Probar interacciones (zoom, clicks, filtros)

### **Ventajas del Método Iframe**

✅ Mantiene toda la funcionalidad (API, BD, filtros dinámicos)  
✅ Fácil actualización (cambios en backend se reflejan automáticamente)  
✅ No requiere modificar archivos en SharePoint  
✅ Soporte para autenticación (si se implementa)

### **Desventajas**

⚠️ Requiere backend activo 24/7  
⚠️ Posibles problemas de CORS/X-Frame-Options  
⚠️ Latencia de red si el servidor está lejos

---

## 📄 MÉTODO 2: HTML Estático {#metodo-2-html-estatico}

### **Descripción**

Exportar el mapa como archivos HTML/CSS/JS estáticos y subirlos directamente a SharePoint.

### **⚠️ LIMITACIONES IMPORTANTES**

- ❌ **La API REST de Django NO funcionará** (sin backend)
- ❌ No habrá filtros dinámicos por mes/aeropuerto
- ❌ No se pueden cargar datos desde la base de datos
- ✅ Solo funciona si **todos los datos están en archivos JSON estáticos**

### **Cuándo usar este método**

- Los datos NO cambian frecuentemente
- No se necesitan consultas dinámicas
- Se pueden pre-generar archivos JSON con todos los datos

### **Pasos de Implementación**

#### **1. Modificar el Código para Modo Estático**

Crear archivo [aeropuertos_project/aeropuertos/templates/aeropuertos/mapa_static.html](aeropuertos_project/aeropuertos/templates/aeropuertos/mapa_static.html):

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Aeropuertos Global - Mapa Estático</title>
    
    <!-- IMPORTANTE: Rutas relativas -->
    <link href="https://unpkg.com/maplibre-gl@4.7.1/dist/maplibre-gl.css" rel="stylesheet">
    <script src="https://unpkg.com/maplibre-gl@4.7.1/dist/maplibre-gl.js"></script>
    
    <style>
        /* Copiar todo el CSS del mapa.html aquí */
    </style>
</head>
<body>
    <!-- Copiar estructura HTML -->
    
    <script>
        // CAMBIO CRÍTICO: API key hardcodeada (solo para versión estática)
        const MAPTILER_KEY = "TU_CLAVE_MAPTILER_AQUI";
        
        // CAMBIO CRÍTICO: Datos cargados desde archivos JSON locales
        async function cargarDatos() {
            const catalogo = await fetch('./data/aeropuertos_catalog.json').then(r => r.json());
            const pnrAeropuertos = await fetch('./data/pnr_aeropuertos.json').then(r => r.json());
            const pnrRutas = await fetch('./data/pnr_rutas.json').then(r => r.json());
            
            // Continuar con lógica del mapa...
        }
        
        cargarDatos();
    </script>
</body>
</html>
```

#### **2. Exportar Datos a JSON**

Crear comando Django para exportar datos:

```bash
# Crear archivo: aeropuertos/management/commands/export_static.py
python manage.py export_static
```

Esto generará:
```
static_export/
├── index.html              # Mapa estático
├── data/
│   ├── aeropuertos_catalog.json
│   ├── pnr_aeropuertos.json
│   └── pnr_rutas.json
└── assets/
    ├── css/
    └── js/
```

#### **3. Subir a SharePoint**

1. Ir a **Documentos** en SharePoint
2. Crear carpeta `AeropuertosGlobal`
3. Subir todos los archivos manteniendo la estructura
4. Crear página → Embed → Agregar:

```html
<iframe 
  src="/sites/TuSitio/Documentos/AeropuertosGlobal/index.html"
  width="100%" 
  height="900px">
</iframe>
```

### **Actualización de Datos**

Para actualizar datos:
1. Volver a ejecutar `python manage.py export_static`
2. Reemplazar archivos JSON en SharePoint
3. Limpiar caché del navegador

---

## ☁️ MÉTODO 3: Azure Static Web Apps + Iframe {#metodo-3-azure}

### **Descripción**

Hospedar el frontend en Azure Static Web Apps y el backend Django separado.

### **Arquitectura**

```
SharePoint (iframe)
    ↓
Azure Static Web App (HTML/JS)
    ↓ (API calls)
Backend Django (Azure Web App / Render)
    ↓
PostgreSQL + PostGIS
```

### **Ventajas**

✅ CDN global de Azure (ultra rápido)  
✅ Separación frontend/backend  
✅ Escalable y profesional  
✅ SSL/HTTPS automático

### **Pasos**

1. **Crear Static Web App en Azure:**

```bash
az staticwebapp create \
  --name aeropuertos-frontend \
  --resource-group AeropuertosRG \
  --source https://github.com/EMPRESA/AeropuertosGlobal \
  --location "Central US" \
  --branch main \
  --app-location "/static_frontend"
```

2. **Configurar API Backend URL:**

```javascript
// En el frontend, cambiar las llamadas API:
const API_BASE = 'https://aeropuertos-api.azurewebsites.net/api';
const api = {
    resumen: () => fetch(`${API_BASE}/pnr/resumen/`).then(r => r.json()),
    // ...
};
```

3. **Configurar CORS en Django:**

```python
CORS_ALLOWED_ORIGINS = [
    'https://aeropuertos-frontend.azurestaticapps.net',
]
```

4. **Incrustar en SharePoint:**

```html
<iframe 
  src="https://aeropuertos-frontend.azurestaticapps.net/"
  width="100%" 
  height="900px">
</iframe>
```

---

## 🔐 Configuración de Seguridad {#seguridad}

### **X-Frame-Options**

Django por defecto bloquea iframes. Configurar en `settings.py`:

```python
# Opción 1: Permitir solo SharePoint
X_FRAME_OPTIONS = 'ALLOW-FROM https://empresa.sharepoint.com'

# Opción 2: Permitir todos (menos seguro)
X_FRAME_OPTIONS = 'SAMEORIGIN'

# Opción 3: Deshabilitar (NO RECOMENDADO)
# X_FRAME_OPTIONS = None
```

### **Content Security Policy (CSP)**

```python
# Instalar: pip install django-csp
INSTALLED_APPS += ['csp']
MIDDLEWARE += ['csp.middleware.CSPMiddleware']

CSP_FRAME_ANCESTORS = ['https://empresa.sharepoint.com', 'https://*.sharepoint.com']
```

### **CORS (si se hace fetch desde SharePoint)**

```python
# Instalar: pip install django-cors-headers
INSTALLED_APPS += ['corsheaders']
MIDDLEWARE = ['corsheaders.middleware.CorsMiddleware'] + MIDDLEWARE

CORS_ALLOWED_ORIGINS = [
    'https://empresa.sharepoint.com',
    'https://empresa-my.sharepoint.com',
]

# O permitir todo (desarrollo)
# CORS_ALLOW_ALL_ORIGINS = True
```

### **HTTPS/SSL**

⚠️ **OBLIGATORIO**: SharePoint requiere HTTPS para iframes externos.

- Render.com: SSL automático ✅
- Azure Web App: SSL automático ✅
- Servidor propio: Configurar Let's Encrypt

### **Autenticación (Opcional)**

Para restringir acceso:

```python
# settings.py
MIDDLEWARE += ['django.contrib.auth.middleware.AuthenticationMiddleware']

# En views.py
from django.contrib.auth.decorators import login_required

@login_required
def mapa_view(request):
    # ...
```

---

## 🔧 Troubleshooting {#troubleshooting}

### **Problema: El iframe aparece en blanco**

**Posibles causas:**
1. X-Frame-Options bloqueando
2. Error 500 en el backend
3. URL incorrecta
4. Firewall corporativo

**Solución:**
```python
# settings.py
X_FRAME_OPTIONS = None  # Temporalmente para debug
DEBUG = True  # Ver errores detallados
```

Revisar la consola del navegador (F12 → Console).

### **Problema: CORS errors en console**

```
Access to fetch at '...' from origin 'https://empresa.sharepoint.com' has been blocked by CORS policy
```

**Solución:**
```python
# settings.py
CORS_ALLOWED_ORIGINS = ['https://empresa.sharepoint.com']
# O temporalmente:
CORS_ALLOW_ALL_ORIGINS = True
```

### **Problema: Mixed Content (HTTP/HTTPS)**

```
Mixed Content: The page was loaded over HTTPS, but requested an insecure resource
```

**Solución:**
- Asegurar que TODAS las URLs sean HTTPS
- No usar `http://` en ningún fetch/script/link

### **Problema: El mapa no carga (MapTiler)**

**Verificar:**
1. API key de MapTiler configurada correctamente
2. Límite de requests no excedido
3. Dominio autorizado en MapTiler dashboard

### **Problema: Los datos no cargan**

**Verificar:**
1. Backend está corriendo (`/api/pnr/resumen/` responde)
2. Base de datos tiene datos cargados
3. Variables de entorno configuradas
4. Logs de Django (`heroku logs` / Azure logs)

---

## ✅ Checklist de Deployment

Antes de integrar en SharePoint corporativo:

- [ ] Backend desplegado y accesible vía HTTPS
- [ ] Variables de entorno configuradas correctamente
- [ ] X-Frame-Options permite SharePoint
- [ ] CORS configurado (si aplica)
- [ ] API key de MapTiler activa
- [ ] Datos cargados en PostgreSQL
- [ ] Pruebas en navegador (Chrome, Edge, Safari)
- [ ] Pruebas en dispositivos móviles
- [ ] Documentación de usuario final creada
- [ ] Credenciales de admin respaldadas
- [ ] Plan de backup de BD establecido

---

## 📚 Recursos Adicionales

- [Documentación de Django Deployment](https://docs.djangoproject.com/en/5.1/howto/deployment/)
- [MapTiler Documentation](https://docs.maptiler.com/)
- [SharePoint Embed Web Part](https://support.microsoft.com/en-us/office/use-the-embed-web-part-721f3b2f-437f-45ef-ac4e-df29dba74de8)
- [Django CORS Headers](https://github.com/adamchainz/django-cors-headers)
- [Azure Static Web Apps Docs](https://learn.microsoft.com/en-us/azure/static-web-apps/)

---

**Última actualización:** 2026-07-08  
**Contacto:** Equipo de Desarrollo TI
