# 📋 GUÍA PASO A PASO: INTEGRACIÓN EN SHAREPOINT

**Aplicación:** AeropuertosGlobal  
**URL:** https://aeropuertos-web.onrender.com  
**Fecha:** 2026-07-08

---

## ✅ PRE-REQUISITOS

Antes de comenzar, asegúrate de tener:

- [ ] Acceso a SharePoint de SITA (sita.sharepoint.com)
- [ ] Permisos de edición en el sitio donde publicarás
- [ ] La prueba local funcionando (test_iframe_local.html)
- [ ] Aplicación corriendo en Render.com

---

## 📍 FASE 1: PREPARACIÓN (YA COMPLETADA ✅)

### 1.1 Verificación Local
- ✅ Archivo de prueba creado: `test_iframe_local.html`
- ⏳ Abrir en navegador y verificar que funciona
- ⏳ Confirmar que el mapa carga correctamente

### 1.2 URL de Producción
```
https://aeropuertos-web.onrender.com
```

---

## 📍 FASE 2: ACCESO A SHAREPOINT (5-10 minutos)

### 2.1 Identificar tu Sitio de SharePoint

**Opción A: Si ya sabes el URL de tu sitio**
- Ir directamente a: `https://sita.sharepoint.com/sites/TU-SITIO`

**Opción B: Si no conoces el URL**
1. Ir a: https://sita.sharepoint.com
2. Click en el icono de "Sitios" (menú superior)
3. Buscar tu sitio o equipo
4. Anotar la URL completa

**Ejemplo de URLs de SharePoint:**
```
https://sita.sharepoint.com/sites/Analytics
https://sita.sharepoint.com/sites/DataScience
https://sita.sharepoint.com/teams/TuEquipo
```

### 2.2 Verificar Permisos

**Necesitas nivel de permisos:** Miembro o Propietario

**Para verificar:**
1. En tu sitio, click en ⚙️ (Settings) → "Site permissions"
2. Deberías ver "Edit" o "Owner" junto a tu nombre
3. Si no tienes permisos:
   - Contactar al dueño del sitio
   - O solicitar crear un sitio nuevo

---

## 📍 FASE 3: CREAR PÁGINA EN SHAREPOINT (10 minutos)

### 3.1 Crear Nueva Página

**Pasos:**

1. En tu sitio de SharePoint, click en **"+ New"** (arriba a la izquierda)

2. Selecciona **"Page"**

3. Elige plantilla:
   - **"Blank"** (Recomendado - página en blanco)
   - O cualquier otra según tu preferencia

4. Nombrar la página:
   ```
   Título: Análisis PNR3 - Aeropuertos Global
   ```

5. La página se abrirá en modo edición

---

### 3.2 Agregar Web Part de Inserción

**Pasos:**

1. En la página en blanco, verás un signo **"+"** 
   - Click en el **"+"** donde quieras insertar el mapa

2. En el menú que aparece, buscar:
   - **"Embed"** (Insertar) o
   - **"Script Editor"** o
   - **"Content Editor"**

   **SI NO VES "EMBED":**
   - Busca "Microsoft Forms"
   - O cualquier web part que permita código HTML/iframe

3. Se agregará un cuadro de edición

---

### 3.3 Insertar Código Iframe

**OPCIÓN A: Código Básico (Simple)**

Copia y pega este código en el Web Part:

```html
<iframe 
  src="https://aeropuertos-web.onrender.com/" 
  width="100%" 
  height="900px"
  frameborder="0"
  allow="geolocation"
  style="border: none; display: block;">
</iframe>
```

**OPCIÓN B: Código con Título (Recomendado)**

```html
<div style="background: white; padding: 20px; border-radius: 8px; box-shadow: 0 2px 4px rgba(0,0,0,0.1);">
  <h2 style="color: #0078d4; margin-bottom: 15px;">
    📊 Análisis de Rutas Aeroportuarias - PNR3
  </h2>
  <p style="color: #666; margin-bottom: 20px;">
    Visualización interactiva de 223,248 vuelos y 35M pasajeros (ENE-JUN 2026)
  </p>
  <iframe 
    src="https://aeropuertos-web.onrender.com/" 
    width="100%" 
    height="900px"
    frameborder="0"
    allow="geolocation"
    style="border: 1px solid #ddd; border-radius: 4px;">
  </iframe>
  <p style="color: #999; font-size: 12px; margin-top: 10px;">
    💡 Primera carga puede tardar 30-60 segundos | 
    <a href="https://aeropuertos-web.onrender.com/admin/" target="_blank">Admin</a> | 
    <a href="https://aeropuertos-web.onrender.com/dashboard/" target="_blank">Dashboard</a>
  </p>
</div>
```

**OPCIÓN C: Código Full Screen (Para página dedicada)**

```html
<style>
  body { margin: 0; padding: 0; overflow: hidden; }
  .fullscreen-iframe { 
    position: fixed; 
    top: 0; 
    left: 0; 
    width: 100%; 
    height: 100vh; 
    border: none; 
  }
</style>
<iframe 
  src="https://aeropuertos-web.onrender.com/" 
  class="fullscreen-iframe"
  allow="geolocation">
</iframe>
```

---

### 3.4 Configurar el Web Part

**Después de pegar el código:**

1. Click fuera del cuadro de código
2. El iframe debería aparecer (puede tardar unos segundos)
3. Si no aparece inmediatamente, espera 30-60 segundos
4. Ajusta la altura si es necesario:
   - Cambiar `height="900px"` a `height="1200px"` para más espacio

---

### 3.5 Guardar y Publicar

**Pasos:**

1. Click en **"Save as draft"** (Guardar como borrador)
   - Verifica que todo se ve bien

2. Si todo está correcto, click en **"Publish"** (Publicar)

3. Confirmación:
   - Verás un mensaje: "Page published successfully"

4. Copia la URL de la página:
   ```
   https://sita.sharepoint.com/sites/TU-SITIO/SitePages/Analisis-PNR3.aspx
   ```

---

## 📍 FASE 4: RESOLUCIÓN DE PROBLEMAS COMUNES

### ❌ Problema 1: "This content cannot be displayed in a frame"

**Causa:** SharePoint está bloqueando el iframe por seguridad.

**Solución A: Configurar CORS en Django (RECOMENDADO)**

1. Ir a Render Dashboard → aeropuertos-web → Environment → Edit

2. Agregar esta variable (si no existe):
   ```
   CORS_ALLOWED_ORIGINS=https://sita.sharepoint.com,https://sita-my.sharepoint.com
   ```

3. Agregar también:
   ```
   CSRF_TRUSTED_ORIGINS=https://aeropuertos-web.onrender.com,https://sita.sharepoint.com
   ```

4. Save Changes y esperar 2 minutos

**Solución B: Solicitar a IT de SITA**

Enviar ticket a IT con:
```
Asunto: Whitelist de dominio para iframe en SharePoint

Solicitud:
Permitir iframe del dominio: aeropuertos-web.onrender.com
En sitio SharePoint: [TU-SITIO-URL]
Aplicación: Análisis PNR3 - Aeropuertos Global
Justificación: Visualización de datos de vuelos internos

Configuración necesaria:
- Content Security Policy: frame-src 'self' https://aeropuertos-web.onrender.com
- X-Frame-Options: ALLOW-FROM https://sita.sharepoint.com
```

---

### ❌ Problema 2: El iframe aparece pero vacío

**Causa:** La aplicación de Render está "dormida" (free tier).

**Solución:**
1. Abrir en otra pestaña: https://aeropuertos-web.onrender.com
2. Esperar 30-60 segundos a que "despierte"
3. Recargar la página de SharePoint (F5)

---

### ❌ Problema 3: No encuentro el Web Part "Embed"

**SharePoint puede tener diferentes nombres según versión:**

**Alternativa 1: Script Editor**
- Buscar "Script Editor" en los Web Parts
- Funciona igual que Embed

**Alternativa 2: Content Editor**
- Buscar "Content Editor"
- Pegar código HTML

**Alternativa 3: Code Snippet**
- Algunas versiones tienen "Code Snippet"

**Alternativa 4: HTML integrado**
1. Agregar Web Part de "Text"
2. Click en `</>` (código HTML)
3. Pegar el código iframe

---

### ❌ Problema 4: No tengo permisos

**Solución:**
1. Contactar al dueño del sitio SharePoint
2. Solicitar permisos de "Miembro" o "Colaborador"
3. O crear un sitio nuevo (si tienes ese permiso)

**Para crear sitio nuevo:**
1. Ir a: https://sita.sharepoint.com
2. Click en "Create site"
3. Seleccionar "Team site" o "Communication site"
4. Nombrar: "Análisis PNR3"

---

## 📍 FASE 5: CONFIGURACIÓN AVANZADA (OPCIONAL)

### 5.1 Agregar Botón de Pantalla Completa

Código mejorado con botón:

```html
<div id="mapa-container" style="background: white; padding: 20px; border-radius: 8px;">
  <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 15px;">
    <h2 style="color: #0078d4; margin: 0;">📊 Análisis PNR3</h2>
    <button onclick="toggleFullscreen()" style="background: #0078d4; color: white; border: none; padding: 10px 20px; border-radius: 4px; cursor: pointer;">
      🔍 Pantalla Completa
    </button>
  </div>
  <iframe 
    id="mapa-iframe"
    src="https://aeropuertos-web.onrender.com/" 
    width="100%" 
    height="900px"
    frameborder="0"
    allow="geolocation"
    style="border: 1px solid #ddd; border-radius: 4px;">
  </iframe>
</div>

<script>
function toggleFullscreen() {
  const iframe = document.getElementById('mapa-iframe');
  if (!document.fullscreenElement) {
    iframe.requestFullscreen();
  } else {
    document.exitFullscreen();
  }
}
</script>
```

---

### 5.2 Agregar Enlaces Rápidos

```html
<div style="margin-top: 15px; padding: 15px; background: #f3f2f1; border-radius: 4px;">
  <strong>🔗 Enlaces Rápidos:</strong><br>
  <a href="https://aeropuertos-web.onrender.com/" target="_blank">Mapa Principal</a> | 
  <a href="https://aeropuertos-web.onrender.com/dashboard/" target="_blank">Dashboard</a> | 
  <a href="https://aeropuertos-web.onrender.com/admin/" target="_blank">Administración</a> | 
  <a href="https://aeropuertos-web.onrender.com/api/pnr/resumen/" target="_blank">API</a>
</div>
```

---

### 5.3 Agregar Instrucciones de Uso

```html
<div style="margin-bottom: 20px; padding: 15px; background: #e3f2fd; border-left: 4px solid #2196F3;">
  <strong>💡 Cómo usar esta visualización:</strong>
  <ul style="margin: 10px 0;">
    <li>🖱️ Click en aeropuertos para ver detalles</li>
    <li>🎚️ Usa los filtros laterales para cambiar vistas</li>
    <li>📊 Círculos más grandes = Mayor volumen de pasajeros</li>
    <li>🔵 Líneas azules = Rutas de vuelo activas</li>
  </ul>
</div>
```

---

## 📍 FASE 6: CONFIGURACIÓN DE PERMISOS

### 6.1 Decidir Quién Puede Ver la Página

**En SharePoint, después de publicar:**

1. Click en **"..."** (tres puntos) en la esquina superior derecha
2. Seleccionar **"Page permissions"** o **"Manage access"**
3. Opciones:
   - **Everyone:** Todos en SITA pueden ver
   - **Members:** Solo miembros del sitio
   - **Specific people:** Solo usuarios específicos

**Recomendación:** Empezar con "Members" y expandir después.

---

### 6.2 Agregar a Navegación del Sitio

Para que sea fácil de encontrar:

1. Ir a **Settings** ⚙️ → **"Site navigation"**
2. Click en **"+ Add link"**
3. Configurar:
   - **Text to display:** Análisis PNR3
   - **Address:** [URL-de-tu-página]
4. Click **"OK"**

Ahora aparecerá en el menú lateral del sitio.

---

## 📍 FASE 7: VERIFICACIÓN FINAL

### 7.1 Checklist de Pruebas

- [ ] La página se carga correctamente
- [ ] El iframe muestra el mapa
- [ ] Los aeropuertos son visibles
- [ ] Las rutas se dibujan
- [ ] El panel lateral funciona
- [ ] Los filtros responden
- [ ] Los enlaces rápidos funcionan
- [ ] Permisos configurados correctamente
- [ ] Página agregada a navegación

### 7.2 Probar desde Otro Navegador

1. Abrir en modo incógnito
2. Iniciar sesión con credenciales de SITA
3. Navegar a la página
4. Verificar que todo funciona

### 7.3 Probar desde Dispositivo Móvil

1. Abrir SharePoint Mobile App o navegador móvil
2. Ir a tu página
3. Verificar responsividad

---

## 📍 FASE 8: COMPARTIR CON EL EQUIPO

### 8.1 Enviar Anuncio por Email

**Plantilla de email:**

```
Asunto: 📊 Nueva Herramienta: Análisis de Rutas Aeroportuarias PNR3

Estimado equipo,

Me complace compartir una nueva herramienta de visualización de datos:

🔗 URL: [URL-DE-TU-PÁGINA-SHAREPOINT]

Características:
• 223,248 vuelos analizados (ENE-JUN 2026)
• 35M pasajeros
• 294 aeropuertos
• Visualización interactiva de rutas
• Filtros dinámicos
• Dashboard de estadísticas

Cómo acceder:
1. Ir al link arriba
2. Explorar el mapa interactivo
3. Usar filtros para diferentes vistas

¿Preguntas? Contactarme directamente.

Saludos,
[Tu nombre]
```

### 8.2 Crear Post en Teams (Si aplica)

1. Ir al canal de Teams relacionado
2. Crear nuevo post
3. Incluir:
   - Screenshot del mapa
   - Link a SharePoint
   - Breve descripción

---

## 📍 FASE 9: MANTENIMIENTO Y ACTUALIZACIONES

### 9.1 Monitoreo

**Verificar semanalmente:**
- [ ] Render.com está activo (https://dashboard.render.com)
- [ ] No hay errores en logs
- [ ] Datos están actualizados

### 9.2 Actualizar Datos

**Cuando tengas nuevos datos PNR:**

1. Ir a Render Shell
2. Ejecutar:
   ```bash
   python manage.py cargar_pnr NUEVO_ARCHIVO.xlsx
   ```
3. Verificar en el mapa
4. Anunciar actualización al equipo

### 9.3 Actualizar Código

**Si haces cambios en el código:**

1. Push a GitHub:
   ```powershell
   git add .
   git commit -m "Actualización: [descripción]"
   git push github main
   ```

2. Render desplegará automáticamente (2-5 minutos)

3. Verificar en SharePoint que funciona

---

## 🆘 SOPORTE

### Contactos SITA

- **IT SharePoint:** [correo-it@sita.aero]
- **Seguridad:** Para whitelist de dominios
- **Administrador del sitio:** [dueño-del-sitio@sita.aero]

### Recursos

- **Render Dashboard:** https://dashboard.render.com
- **Aplicación:** https://aeropuertos-web.onrender.com
- **Admin:** https://aeropuertos-web.onrender.com/admin/
- **Documentación:** Ver archivos README.md, DEPLOYMENT_SUCCESS.md

### Troubleshooting Rápido

| Problema | Solución Rápida |
|----------|----------------|
| Iframe vacío | Esperar 60 seg, recargar (F5) |
| Error 403/404 | Verificar URL correcta |
| No se ve en SharePoint | Verificar permisos de página |
| Lento | Upgrade Render a Starter ($7/mes) |

---

## ✅ RESUMEN DE COMANDOS/CÓDIGO ÚTILES

### Código Iframe Básico
```html
<iframe src="https://aeropuertos-web.onrender.com/" width="100%" height="900px" frameborder="0"></iframe>
```

### Agregar CORS en Render
```
Variable: CORS_ALLOWED_ORIGINS
Valor: https://sita.sharepoint.com,https://sita-my.sharepoint.com
```

### Agregar CSRF en Render
```
Variable: CSRF_TRUSTED_ORIGINS  
Valor: https://aeropuertos-web.onrender.com,https://sita.sharepoint.com
```

---

**📞 ¿Necesitas ayuda adicional?**

Consulta los otros archivos de documentación:
- `DEPLOYMENT_SUCCESS.md` - Resumen completo
- `SHAREPOINT_INTEGRATION.md` - Detalles técnicos
- `README.md` - Documentación técnica

---

**Creado:** 2026-07-08  
**Última actualización:** 2026-07-08  
**Versión:** 1.0
