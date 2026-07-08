# ✈️ AeropuertosGlobal - Sistema de Análisis de Tráfico Aéreo PNR3

> **Aplicación web corporativa para análisis geoespacial de datos de vuelos y pasajeros**

![Estado](https://img.shields.io/badge/Estado-Producción-green)
![Versión](https://img.shields.io/badge/Versión-1.0.0-blue)
![Django](https://img.shields.io/badge/Django-5.1.4-darkgreen)
![PostGIS](https://img.shields.io/badge/PostGIS-Enabled-orange)

---

## 📋 Tabla de Contenidos

- [Descripción](#descripción)
- [Características](#características)
- [Arquitectura](#arquitectura)
- [Requisitos](#requisitos)
- [Instalación](#instalación)
- [Configuración](#configuración)
- [Uso](#uso)
- [API REST](#api-rest)
- [Despliegue](#despliegue)
- [Seguridad](#seguridad)
- [Contribución](#contribución)
- [Licencia](#licencia)

---

## 📖 Descripción

**AeropuertosGlobal** es una plataforma web desarrollada con Django y GeoDjango para el análisis y visualización interactiva de:

- ✈️ **Datos PNR3** (Passenger Name Record) del H1 2026 (Enero-Junio)
- 🌍 **Mapas interactivos** de rutas aéreas globales
- 📊 **Dashboards analíticos** con métricas de tráfico
- 🗺️ **Análisis geoespacial** con PostGIS
- 📡 **API REST** para integración con otros sistemas

### **Casos de Uso**

1. Visualización de rutas y volúmenes de pasajeros
2. Análisis de tendencias por aeropuerto, aerolínea y ruta
3. Identificación de patrones temporales (hora, día, mes)
4. Integración de datos de tráfico aéreo en reportes corporativos

---

## ✨ Características

### **Frontend**

- 🗺️ **Mapa interactivo** con MapLibre GL JS
- 🎨 **Interfaz moderna** con diseño responsive
- 🔍 **Búsqueda en tiempo real** de aeropuertos
- 📈 **Visualizaciones dinámicas** (Sankey, Heatmaps, Gráficas)
- 🎛️ **Filtros interactivos** (mes, volumen, rutas)

### **Backend**

- 🚀 **Django 5.1.4** con GeoDjango
- 🗄️ **PostgreSQL 16** + **PostGIS 3.4**
- 📡 **API REST** con Django REST Framework
- 🔐 **Seguridad** con variables de entorno
- 🐳 **Docker** compatible

### **Análisis Geoespacial**

- 📍 Consultas espaciales optimizadas
- 🌐 Cálculo de rutas geodésicas
- 🗃️ Soporte para GeoJSON nativo
- 🎯 Clustering de aeropuertos

---

## 🏗️ Arquitectura

```
┌─────────────────────────────────────────────────┐
│                  FRONTEND                        │
│  ┌──────────────┐  ┌──────────────┐             │
│  │  Mapa GL JS  │  │  Dashboard   │             │
│  │  (MapLibre)  │  │  (ECharts)   │             │
│  └──────┬───────┘  └──────┬───────┘             │
└─────────┼──────────────────┼──────────────────────┘
          │                  │
          │    AJAX/Fetch    │
          ▼                  ▼
┌─────────────────────────────────────────────────┐
│              DJANGO REST API                     │
│  ┌──────────────────────────────────────────┐   │
│  │  /api/pnr/resumen/                       │   │
│  │  /api/pnr/por-aeropuerto/                │   │
│  │  /api/pnr/rutas/                         │   │
│  │  /api/pnr/<iata>/detalle/                │   │
│  │  /api/aeropuertos/geojson/               │   │
│  └───────────────┬──────────────────────────┘   │
└──────────────────┼──────────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────────┐
│         PostgreSQL 16 + PostGIS 3.4             │
│  ┌──────────────┐  ┌──────────────┐            │
│  │ aeropuertos  │  │ vuelos_pnr   │            │
│  │   (Point)    │  │  (relacional)│            │
│  └──────────────┘  └──────────────┘            │
└─────────────────────────────────────────────────┘
```

---

## 💻 Requisitos

### **Obligatorios**

- Python 3.12+
- PostgreSQL 16+ con extensión PostGIS 3.4+
- GDAL 3.8+ (librerías geoespaciales)

### **Recomendados**

- Docker y Docker Compose
- Node.js 18+ (solo para desarrollo de frontend)
- Git 2.40+

---

## 🚀 Instalación

### **Opción 1: Instalación Local (Desarrollo)**

#### 1. Clonar el repositorio

```bash
git clone https://github.com/EMPRESA/AeropuertosGlobal.git
cd AeropuertosGlobal/aeropuertos_project
```

#### 2. Crear entorno virtual

```bash
python -m venv venv
source venv/bin/activate  # Linux/Mac
venv\Scripts\activate     # Windows
```

#### 3. Instalar dependencias

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

#### 4. Configurar variables de entorno

Crear archivo `.env` en `aeropuertos_project/.env`:

```env
# Django
DJANGO_SECRET_KEY=tu-clave-secreta-generada
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1

# Base de datos PostgreSQL + PostGIS
DB_NAME=aeropuertos_db
DB_USER=postgres
DB_PASSWORD=tu_password
DB_HOST=localhost
DB_PORT=5432

# Servicios externos
MAPTILER_API_KEY=tu_clave_maptiler
```

#### 5. Crear base de datos

```bash
# Conectar a PostgreSQL y ejecutar:
CREATE DATABASE aeropuertos_db;
\c aeropuertos_db
CREATE EXTENSION postgis;
```

#### 6. Migraciones

```bash
python manage.py migrate
```

#### 7. Cargar datos (opcional)

```bash
# Si tienes archivos de datos:
python manage.py cargar_pnr ruta/al/archivo.xlsx
```

#### 8. Crear superusuario

```bash
python manage.py createsuperuser
```

#### 9. Iniciar servidor

```bash
python manage.py runserver
```

🌐 Acceder a: `http://localhost:8000`

---

### **Opción 2: Docker (Producción)**

```bash
docker build -t aeropuertos-global .
docker run -p 8000:8000 \
  -e DJANGO_SECRET_KEY=<clave> \
  -e DATABASE_URL=<url> \
  -e MAPTILER_API_KEY=<clave> \
  aeropuertos-global
```

---

## ⚙️ Configuración

### **Variables de Entorno Completas**

| Variable | Descripción | Requerida | Ejemplo |
|----------|-------------|-----------|---------|
| `DJANGO_SECRET_KEY` | Clave secreta de Django | ✅ Sí | `django-insecure-abc123...` |
| `DEBUG` | Modo debug (solo desarrollo) | ⚠️ No | `False` |
| `ALLOWED_HOSTS` | Hosts permitidos (CSV) | ✅ Sí | `ejemplo.com,www.ejemplo.com` |
| `DATABASE_URL` | URL de conexión PostgreSQL | ⚠️ Opcional | `postgres://user:pass@host:5432/db` |
| `DB_NAME` | Nombre de la base de datos | ✅ Sí | `aeropuertos_db` |
| `DB_USER` | Usuario de PostgreSQL | ✅ Sí | `postgres` |
| `DB_PASSWORD` | Contraseña de PostgreSQL | ✅ Sí | `password123` |
| `DB_HOST` | Host de PostgreSQL | ✅ Sí | `localhost` |
| `DB_PORT` | Puerto de PostgreSQL | ⚠️ No | `5432` |
| `MAPTILER_API_KEY` | API Key de MapTiler | ✅ Sí | `pk_aBcD1234...` |

### **Generar SECRET_KEY seguro**

```bash
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

---

## 📚 Uso

### **Mapa Principal**

`/` → Visualización interactiva de rutas y aeropuertos

**Controles:**
- 🔍 Buscar aeropuerto por IATA, ciudad o país
- 🗓️ Filtrar por mes (Ene-Jun 2026)
- 🎛️ Alternar capas (rutas, volumen, catálogo)
- 📊 Click en aeropuerto para análisis detallado

### **Dashboard Analítico**

`/dashboard/` → Gráficas avanzadas con ECharts

**Visualizaciones:**
- 🌐 Diagrama Sankey de flujos internacionales
- 🕐 Heatmap de densidad por día/hora
- 📈 Tendencias mensuales

### **Panel de Administración**

`/admin/` → Gestión de datos (requiere credenciales)

---

## 📡 API REST

### **Base URL:** `/api/`

### **Endpoints Disponibles**

#### 1. **Resumen General**
```http
GET /api/pnr/resumen/?mes=1
```
**Respuesta:**
```json
{
  "total_vuelos": 150234,
  "total_pasajeros": 12456789,
  "total_aerolineas": 45,
  "total_aeropuertos": 234,
  "total_pares_od": 3456
}
```

#### 2. **Aeropuertos por Volumen**
```http
GET /api/pnr/por-aeropuerto/
```
**Parámetros:**
- `mes` (opcional): Filtrar por mes (1-6)

#### 3. **Rutas Principales**
```http
GET /api/pnr/rutas/?top=200&min_vuelos=25
```
**Parámetros:**
- `top`: Máximo de rutas a retornar
- `min_vuelos`: Filtro de vuelos mínimos

#### 4. **Detalle de Aeropuerto**
```http
GET /api/pnr/MEX/detalle/
```
**Respuesta:** Análisis completo por aeropuerto IATA

#### 5. **Catálogo GeoJSON**
```http
GET /api/aeropuertos/geojson/
```

---

## 🚢 Despliegue

### **Render.com**

El archivo `render.yaml` incluye configuración preconfigurada:

```yaml
services:
  - type: web
    name: aeropuertos-web
    env: docker
    plan: free
```

### **Azure Static Web Apps / GitHub Pages**

Para exportar versión estática (mapa standalone):

1. Generar HTML estático con API keys inyectadas
2. Recolectar archivos estáticos:
   ```bash
   python manage.py collectstatic --noinput
   ```
3. Configurar servidor web (nginx/Apache) o usar CDN

### **SharePoint (Iframe)**

Ver sección [Integración con SharePoint](#sharepoint) más abajo.

---

## 🔐 Seguridad

### **Medidas Implementadas**

✅ Variables de entorno para credenciales  
✅ `.gitignore` configurado correctamente  
✅ HTTPS forzado en producción (WhiteNoise)  
✅ Validación de SECRET_KEY  
✅ Protección CSRF habilitada  

### **Recomendaciones Adicionales**

⚠️ **Cambiar API keys** antes de despliegue público  
⚠️ **Habilitar autenticación** en la API REST  
⚠️ **Configurar CORS** apropiadamente  
⚠️ **Rate limiting** en endpoints sensibles  
⚠️ **Backups regulares** de base de datos  

### **Datos Sensibles**

🚫 **NO INCLUIR en el repositorio:**
- Archivos Excel con datos PNR
- Credenciales de base de datos
- API keys
- Archivos `.env`

---

## 📤 Integración con SharePoint {#sharepoint}

### **Método 1: Iframe (Recomendado)**

En tu página de SharePoint, agregar Web Part "Embed":

```html
<iframe 
  src="https://tu-dominio.azurewebsites.net/" 
  width="100%" 
  height="800px" 
  frameborder="0"
  allowfullscreen>
</iframe>
```

### **Método 2: Exportar HTML Estático**

1. **Generar página estática:**
   ```bash
   python manage.py collectstatic
   # Copiar archivos de staticfiles/ a SharePoint
   ```

2. **Configurar rutas relativas** (ya implementado)

3. **Subir a biblioteca de SharePoint:**
   - Subir `mapa.html` + carpetas CSS/JS
   - Configurar permisos de acceso

### **Consideraciones de Seguridad SharePoint**

⚠️ **X-Frame-Options**: Configurar en Django settings:
```python
X_FRAME_OPTIONS = 'ALLOW-FROM https://empresa.sharepoint.com'
```

⚠️ **CORS**: Permitir origen de SharePoint:
```python
CORS_ALLOWED_ORIGINS = ['https://empresa.sharepoint.com']
```

---

## 🤝 Contribución

### **Flujo de Trabajo**

1. Fork del repositorio
2. Crear rama feature: `git checkout -b feature/nueva-funcionalidad`
3. Commit cambios: `git commit -m 'Agregar nueva funcionalidad'`
4. Push a la rama: `git push origin feature/nueva-funcionalidad`
5. Crear Pull Request

### **Estándares de Código**

- **Python**: PEP 8
- **JavaScript**: ESLint (estándar)
- **Commits**: Conventional Commits

---

## 📄 Licencia

**Uso Corporativo Interno**  
© 2026 [NOMBRE DE LA EMPRESA]. Todos los derechos reservados.

Este software es propiedad exclusiva de [EMPRESA] y su uso está restringido a personal autorizado. No se permite redistribución, modificación o uso comercial fuera de la organización sin autorización expresa.

---

## 📞 Soporte y Contacto

- **Equipo de Desarrollo:** desarrollo@empresa.com
- **Soporte Técnico:** soporte-ti@empresa.com
- **Documentación:** [Wiki Interna](https://empresa.sharepoint.com/wiki)
- **Reportar Incidencias:** [Sistema de Tickets](https://tickets.empresa.com)

---

## 🗂️ Estructura del Proyecto

```
AeropuertosGlobal/
├── aeropuertos_project/          # Proyecto Django principal
│   ├── aeropuertos/              # App principal
│   │   ├── management/           # Comandos personalizados
│   │   ├── migrations/           # Migraciones de BD
│   │   ├── templates/            # Plantillas HTML
│   │   │   └── aeropuertos/
│   │   │       ├── mapa.html     # Mapa principal
│   │   │       └── dashboard.html # Dashboard
│   │   ├── admin.py              # Config admin panel
│   │   ├── api_views.py          # Vistas API (aeropuertos)
│   │   ├── pnr_views.py          # Vistas API (PNR)
│   │   ├── models.py             # Modelos de BD
│   │   ├── serializers.py        # Serializers REST
│   │   ├── urls.py               # URLs de la app
│   │   └── views.py              # Vistas principales
│   ├── aeropuertos_project/      # Configuración
│   │   ├── settings.py           # Settings Django
│   │   ├── urls.py               # URLs globales
│   │   └── wsgi.py               # WSGI entry point
│   ├── manage.py                 # CLI Django
│   └── aeropuertos_catalog.json  # Catálogo de aeropuertos
├── Dockerfile                    # Imagen Docker
├── render.yaml                   # Config Render.com
├── requirements.txt              # Dependencias Python
├── .gitignore                    # Archivos ignorados
├── .dockerignore                 # Archivos ignorados Docker
├── README.md                     # Este archivo
└── SECURITY_AUDIT.md             # Auditoría de seguridad

Archivos NO incluidos (sensibles):
├── .env                          # Variables de entorno
├── Aeropuertos/                  # Datos PNR (Excel)
└── venv/                         # Entorno virtual
```

---

## 📝 Changelog

### v1.0.0 (2026-07-08)
- ✨ Lanzamiento inicial
- 🗺️ Mapa interactivo con MapLibre GL
- 📊 Dashboard analítico con ECharts
- 📡 API REST completa
- 🐳 Soporte Docker
- 📚 Documentación completa

---

**Última actualización:** 2026-07-08  
**Mantenido por:** Equipo de Desarrollo Corporativo
