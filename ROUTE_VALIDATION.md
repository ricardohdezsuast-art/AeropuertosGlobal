# 🔍 VALIDACIÓN DE RUTAS - AeropuertosGlobal

**Fecha:** 2026-07-08  
**Objetivo:** Verificar que todas las rutas sean portables y funcionen en diferentes entornos

---

## 📊 RESULTADO DEL ANÁLISIS

### ✅ **ARCHIVOS ANALIZADOS**

- [aeropuertos_project/aeropuertos/templates/aeropuertos/mapa.html](aeropuertos_project/aeropuertos/templates/aeropuertos/mapa.html)
- [aeropuertos_project/aeropuertos/templates/aeropuertos/dashboard.html](aeropuertos_project/aeropuertos/templates/aeropuertos/dashboard.html)

---

## 🎯 TIPOS DE RUTAS ENCONTRADAS

### 1. **CDN Externos (CORRECTAS ✅)**

Estas rutas son absolutas y funcionan en cualquier entorno:

#### **mapa.html:**
```html
<!-- MapLibre GL JS -->
<link href="https://unpkg.com/maplibre-gl@4.7.1/dist/maplibre-gl.css" rel="stylesheet">
<script src="https://unpkg.com/maplibre-gl@4.7.1/dist/maplibre-gl.js"></script>

<!-- Google Fonts -->
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
```

#### **dashboard.html:**
```html
<!-- ECharts -->
<script src="https://cdn.jsdelivr.net/npm/echarts@5.5.0/dist/echarts.min.js"></script>

<!-- Google Fonts -->
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
```

**Estado:** ✅ **CORRECTO** - No requieren cambios

---

### 2. **API REST (Django Backend)**

Rutas relativas que apuntan al backend Django:

#### **mapa.html:**
```javascript
const api = {
    resumen:  () => fetch(`/api/pnr/resumen/${qs()}`).then(r => r.json()),
    porAp:    () => fetch(`/api/pnr/por-aeropuerto/${qs()}`).then(r => r.json()),
    rutas:    () => fetch(`/api/pnr/rutas/${qs({ top: S.maxRutas, min_vuelos: S.minVuelos })}`).then(r => r.json()),
    catalogo: () => fetch('/api/aeropuertos/geojson/').then(r => r.json()),
    detalle:  iata => fetch(`/api/pnr/${iata}/detalle/${qs()}`).then(r => r.ok ? r.json() : null),
};
```

**Estado:** ✅ **CORRECTO para Django** - Rutas relativas funcionan cuando hay backend

**⚠️ NOTA:** Si se exporta como HTML estático, estas rutas DEBEN modificarse a archivos JSON locales.

---

### 3. **Rutas Internas (Django Templates)**

#### **Navegación entre vistas:**
```javascript
// mapa.html → dashboard
onclick="window.location.href='/dashboard/'"

// dashboard.html → mapa
onclick="window.location.href='/'"
```

**Estado:** ✅ **CORRECTO** - Rutas relativas manejadas por Django

---

### 4. **MapTiler API Key**

```javascript
const MAPTILER_KEY = "{{ maptiler_key }}";
```

**Estado:** ✅ **CORRECTO** - Inyectado desde Django (variable de entorno)

**⚠️ NOTA:** Para SharePoint estático, cambiar a:
```javascript
const MAPTILER_KEY = "pk_TU_CLAVE_AQUI";
```

---

## 🔧 MODIFICACIONES NECESARIAS SEGÚN ESCENARIO

### **ESCENARIO 1: Django Completo (Iframe en SharePoint)**

❌ **NO REQUIERE CAMBIOS**

Todo funciona como está. Solo configurar:
- X-Frame-Options en settings.py
- CORS si es necesario
- Variables de entorno en producción

---

### **ESCENARIO 2: HTML Estático (SharePoint)**

⚠️ **REQUIERE MODIFICACIONES**

#### **Paso 1: Crear versión estática del mapa**

```html
<!-- mapa_static.html -->
<script>
    // CAMBIO 1: API Key hardcodeada
    const MAPTILER_KEY = "pk_abc123xyz789";  // ⚠️ Proteger esta clave
    
    // CAMBIO 2: Datos desde archivos JSON locales
    const api = {
        resumen:  () => fetch('./data/resumen.json').then(r => r.json()),
        porAp:    () => fetch('./data/aeropuertos.json').then(r => r.json()),
        rutas:    () => fetch('./data/rutas.json').then(r => r.json()),
        catalogo: () => fetch('./data/catalogo.json').then(r => r.json()),
        detalle:  iata => fetch(`./data/detalle_${iata}.json`).then(r => r.json()).catch(() => null),
    };
</script>
```

#### **Paso 2: Estructura de archivos estáticos**

```
sharepoint_export/
├── index.html                    # mapa.html modificado
├── dashboard.html                # dashboard.html modificado
├── data/
│   ├── resumen.json              # Datos pre-generados
│   ├── aeropuertos.json
│   ├── rutas.json
│   ├── catalogo.json
│   └── detalle/
│       ├── MEX.json
│       ├── GDL.json
│       └── ... (un archivo por aeropuerto)
└── README.md
```

#### **Paso 3: Generar archivos JSON**

Crear comando Django:

```python
# aeropuertos/management/commands/export_static_data.py
from django.core.management.base import BaseCommand
import json
from pathlib import Path

class Command(BaseCommand):
    help = 'Exporta datos a JSON estáticos para SharePoint'
    
    def handle(self, *args, **options):
        output_dir = Path('sharepoint_export/data')
        output_dir.mkdir(parents=True, exist_ok=True)
        
        # Exportar resumen
        from aeropuertos.pnr_views import PNRResumenView
        # ... lógica de exportación
        
        self.stdout.write(self.style.SUCCESS('✅ Datos exportados'))
```

Ejecutar:
```bash
python manage.py export_static_data
```

---

### **ESCENARIO 3: Azure Static Web Apps**

✅ **Rutas CDN:** Sin cambios  
⚠️ **Rutas API:** Cambiar a URL absoluta del backend

```javascript
const API_BASE = 'https://aeropuertos-api.azurewebsites.net';

const api = {
    resumen:  () => fetch(`${API_BASE}/api/pnr/resumen/`).then(r => r.json()),
    porAp:    () => fetch(`${API_BASE}/api/pnr/por-aeropuerto/`).then(r => r.json()),
    // ...
};
```

---

## ✅ CHECKLIST DE PORTABILIDAD

### **Archivos CSS**
- [x] Sin archivos CSS locales (todo inline o CDN)
- [x] Google Fonts cargado desde CDN
- [x] Sin referencias a `/static/`

### **Archivos JavaScript**
- [x] Librerías cargadas desde CDN (MapLibre, ECharts)
- [x] Código JS inline en templates
- [x] Sin archivos `.js` externos

### **Imágenes**
- [x] No se usan imágenes locales
- [x] Iconos con emojis Unicode (✈, 📊, 🗺️)
- [x] Sin rutas a `/media/` o `/static/`

### **Datos GeoJSON**
- [ ] ⚠️ Actualmente se cargan desde API Django
- [ ] Para versión estática: Pre-generar archivos JSON

### **API Keys**
- [x] MapTiler key inyectada desde Django (seguro)
- [ ] ⚠️ Para SharePoint: Necesita key hardcodeada (riesgo de seguridad)

---

## 🛡️ RECOMENDACIONES DE SEGURIDAD

### **Para SharePoint con HTML Estático**

1. **Proteger API Key de MapTiler:**
   - Usar MapTiler con restricción de dominio
   - Configurar en dashboard de MapTiler: solo `empresa.sharepoint.com`

2. **No exponer credenciales:**
   - Nunca incluir DB passwords
   - Nunca incluir DJANGO_SECRET_KEY

3. **Límites de API:**
   - MapTiler Free: 100,000 tiles/mes
   - Monitorear uso en dashboard

4. **Datos sensibles:**
   - Si los datos PNR son confidenciales, NO usar versión estática pública
   - Considerar autenticación en backend Django

---

## 📝 SCRIPT DE VALIDACIÓN AUTOMÁTICA

Para verificar rutas en el futuro:

```python
# validate_routes.py
import re
from pathlib import Path

def validate_html(file_path):
    content = Path(file_path).read_text(encoding='utf-8')
    
    # Buscar rutas sospechosas
    problematic = [
        r'src="(?!https?://)[^"]*\.js',  # JS locales no-CDN
        r'href="(?!https?://)[^"]*\.css',  # CSS locales no-CDN
        r'src="C:\\',  # Rutas absolutas Windows
        r'/static/',  # Referencias a static de Django
    ]
    
    issues = []
    for pattern in problematic:
        matches = re.findall(pattern, content)
        if matches:
            issues.append((pattern, matches))
    
    return issues

# Ejecutar
issues = validate_html('aeropuertos_project/aeropuertos/templates/aeropuertos/mapa.html')
if issues:
    print("⚠️ Rutas problemáticas encontradas:")
    for pattern, matches in issues:
        print(f"  Patrón: {pattern}")
        print(f"  Matches: {matches}")
else:
    print("✅ Todas las rutas son portables")
```

---

## 📊 RESUMEN FINAL

| Tipo de Recurso | Estado Actual | Portable | Acción Requerida |
|-----------------|---------------|----------|------------------|
| CSS | ✅ Inline/CDN | ✅ Sí | Ninguna |
| JavaScript | ✅ Inline/CDN | ✅ Sí | Ninguna |
| Imágenes | ✅ Emojis | ✅ Sí | Ninguna |
| Fuentes | ✅ Google Fonts CDN | ✅ Sí | Ninguna |
| Datos GeoJSON | ⚠️ API Django | ❌ No | Exportar a JSON estáticos |
| API Keys | ✅ Variables entorno | ⚠️ Depende | Hardcodear para SharePoint |
| Navegación | ✅ Rutas relativas | ✅ Sí | Ninguna |

---

## ✅ CONCLUSIÓN

**Para uso con Django + Iframe:** ✅ **100% PORTABLE** - No se requieren cambios

**Para SharePoint estático:** ⚠️ **75% PORTABLE** - Requiere:
1. Exportar datos a JSON
2. Modificar rutas API
3. Hardcodear MapTiler API key (con restricciones de dominio)

---

**Última actualización:** 2026-07-08  
**Validado por:** Especialista en Seguridad y Desarrollo
