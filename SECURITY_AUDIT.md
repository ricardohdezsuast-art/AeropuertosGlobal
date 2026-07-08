# 🔒 AUDITORÍA DE SEGURIDAD - AeropuertosGlobal

**Fecha:** 2026-07-08  
**Auditor:** Especialista en Seguridad Corporativa  
**Proyecto:** Migración de GitHub Personal → Repositorio Corporativo

---

## 📊 RESUMEN EJECUTIVO

✅ **Estado General:** El proyecto está relativamente seguro  
⚠️ **Elementos Críticos Identificados:** 3  
🔧 **Acciones Requeridas:** 5

---

## 🚨 ARCHIVOS SENSIBLES IDENTIFICADOS

### 1. **DATOS CONFIDENCIALES**
| Archivo | Riesgo | Acción |
|---------|--------|--------|
| `Aeropuertos/PNR3_Jan-June_2026.xlsx` | 🔴 **CRÍTICO** | **ELIMINAR del repositorio**. Datos de pasajeros PNR3. |
| `aeropuertos_project/aeropuertos_catalog.json` | 🟡 **BAJO** | Revisar contenido (probablemente público). |

**Justificación:**  
- El archivo Excel contiene datos de **Passenger Name Record (PNR)**, que son **información confidencial** regulada por IATA y leyes de privacidad.
- No debe estar en ningún repositorio de código, incluso si es privado.

---

### 2. **CREDENCIALES Y CLAVES DE API**

#### ✅ **CORRECTO** (No hay credenciales hardcodeadas)
- ✅ No se encontraron API keys en código fuente
- ✅ Las credenciales se cargan desde variables de entorno (`.env`)
- ✅ El archivo `.env` NO está en el repositorio (ignorado correctamente)

#### ⚠️ **CLAVES REQUERIDAS** (configurar en entorno)
```bash
# Variables que deben configurarse en producción:
DJANGO_SECRET_KEY=<generar-clave-segura>
MAPTILER_API_KEY=<clave-maptiler>
DB_NAME=aeropuertos_db
DB_USER=<usuario-db>
DB_PASSWORD=<contraseña-db>
DB_HOST=<host-db>
DB_PORT=5432
DEBUG=False
ALLOWED_HOSTS=<dominio-corporativo>
```

---

### 3. **ARCHIVOS DE CONFIGURACIÓN**

| Archivo | Estado | Acción |
|---------|--------|--------|
| `.gitignore` | ✅ **EXISTE** | Mejorar con patrones corporativos |
| `render.yaml` | ⚠️ **PÚBLICO** | Revisar configuración de deployment |
| `Dockerfile` | ✅ **SEGURO** | Sin credenciales |
| `requirements.txt` | ✅ **SEGURO** | Sin versiones vulnerables conocidas |

---

## 🔐 REVISIÓN DE CÓDIGO

### **settings.py**
```python
# ✅ BUENA PRÁCTICA: Uso de variables de entorno
SECRET_KEY = os.getenv('DJANGO_SECRET_KEY', 'clave-insegura-solo-para-desarrollo')
DEBUG = os.getenv('DEBUG', 'True') == 'True'

# ⚠️ MEJORAR: El valor por defecto de SECRET_KEY es inseguro
# Recomendación: Fallar si no está definida en producción
if not os.getenv('DJANGO_SECRET_KEY') and not DEBUG:
    raise ValueError("DJANGO_SECRET_KEY debe estar definida en producción")
```

### **mapa.html**
```javascript
// ✅ CORRECTO: API key inyectada desde Django (no hardcodeada)
const MAPTILER_KEY = "{{ maptiler_key }}";

// ⚠️ NOTA: Para SharePoint, necesitarás generar el HTML estático
```

---

## 📂 ESTRUCTURA DE ARCHIVOS

### **Archivos que DEBEN ignorarse (ya están en .gitignore):**
```
✅ venv/
✅ __pycache__/
✅ .env
✅ *.sqlite3
✅ staticfiles/
✅ Aeropuertos/  ← ⚠️ CRÍTICO: Contiene Excel sensible
✅ Complementos/
```

### **Archivos que DEBEN incluirse:**
```
✅ requirements.txt
✅ Dockerfile
✅ manage.py
✅ aeropuertos_project/
✅ README.md
✅ .gitignore
⚠️ render.yaml (revisar antes)
```

---

## 🛡️ RECOMENDACIONES DE SEGURIDAD

### **CRÍTICAS (Implementar antes de migrar)**
1. ✅ **Verificar que `Aeropuertos/` está en `.gitignore`** (ya está)
2. 🔴 **ELIMINAR historial de Git si el Excel fue commiteado antes**
   ```bash
   git filter-branch --force --index-filter \
     "git rm -rf --cached --ignore-unmatch Aeropuertos/PNR3_Jan-June_2026.xlsx" \
     --prune-empty --tag-name-filter cat -- --all
   ```
3. 🔴 **Rotar DJANGO_SECRET_KEY** (generar una nueva para producción)
4. 🟡 **Configurar ALLOWED_HOSTS** con dominios corporativos específicos

### **IMPORTANTES**
5. 🟡 **Implementar autenticación** en la API REST (actualmente es pública)
6. 🟡 **Configurar CORS** si se despliega en dominios diferentes
7. 🟡 **Habilitar HTTPS** en producción (desactivar DEBUG)

### **RECOMENDADAS**
8. 🟢 Agregar logging de accesos a la API
9. 🟢 Implementar rate limiting en endpoints críticos
10. 🟢 Documentar procedimientos de backup de BD

---

## ✅ CHECKLIST PRE-MIGRACIÓN

- [ ] Revisar historial de commits para credenciales expuestas
- [ ] Verificar que el Excel NO está en ningún commit histórico
- [ ] Generar nuevo DJANGO_SECRET_KEY
- [ ] Obtener MAPTILER_API_KEY corporativa
- [ ] Actualizar `.gitignore` con patrones corporativos
- [ ] Crear README corporativo
- [ ] Documentar variables de entorno necesarias
- [ ] Configurar CI/CD corporativo (si aplica)
- [ ] Pruebas de seguridad en entorno de staging

---

## 📞 CONTACTO Y SOPORTE

Para preguntas sobre seguridad, contactar a:
- Equipo de Seguridad Informática Corporativa
- Oficial de Privacidad de Datos (DPO)

**Nota:** Este documento es CONFIDENCIAL y de uso interno exclusivo.
