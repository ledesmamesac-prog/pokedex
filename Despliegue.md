# 🚀 Guía de Despliegue — PokeDex en Azure Static Web Apps

> Documentación técnica detallada del proceso de despliegue de la aplicación PokeDex en Azure Static Web Apps, incluyendo configuración de seguridad, errores encontrados y soluciones aplicadas.

---

## 📋 Tabla de Contenidos

1. [Pre-requisitos](#1-pre-requisitos)
2. [Preparación del Repositorio GitHub](#2-preparación-del-repositorio-github)
3. [Creación del Recurso en Azure](#3-creación-del-recurso-en-azure)
4. [Configuración de Seguridad](#4-configuración-de-seguridad-headers-http)
5. [Verificación del Despliegue](#5-verificación-del-despliegue)
6. [Auditoría de Seguridad](#6-auditoría-de-seguridad)
7. [Errores Encontrados y Soluciones](#7-errores-encontrados-y-soluciones)

---

## 1. Pre-requisitos

Antes de iniciar el despliegue, verificar que se cuenta con:

- ✅ Cuenta activa en [Azure for Students](https://azure.microsoft.com/es-es/free/students/) con suscripción válida
- ✅ Cuenta en [GitHub](https://github.com)
- ✅ Git instalado localmente
- ✅ Código fuente de la aplicación PokeDex
- ✅ Navegador moderno (Chrome o Edge recomendado)

**Verificar instalación de Git:**

```bash
git --version
# Salida esperada: git version 2.x.x
```

---

## 2. Preparación del Repositorio GitHub

### 2.1 — Crear el repositorio

1. Inicia sesión en [https://github.com](https://github.com)
2. Haz clic en **"New"** (botón verde, esquina superior derecha)
3. Configura el repositorio:
   - **Repository name:** `pokedex`
   - **Visibility:** `Public`
   - **Initialize with README:** ✅ (marca esta opción)
4. Haz clic en **"Create repository"**

### 2.2 — Clonar el código fuente original

El código base se obtiene del repositorio de la academia:

```bash
# Clonar el repositorio fuente
git clone https://github.com/rcuello/ac4dem1a.git

# Navegar a la carpeta del proyecto PokeDex
cd ac4dem1a/sistemas-distribuidos/poke-dex-lab
```

### 2.3 — Configurar tu repositorio local

```bash
# Crear carpeta local para el proyecto
mkdir pokedex
cd pokedex

# Inicializar git
git init

# Copiar los archivos del proyecto clonado
xcopy ..\ac4dem1a\sistemas-distribuidos\poke-dex-lab\* . /E /H /Y.

# Verificar que los archivos estén presentes
dir
```

### 2.4 — Agregar el archivo de configuración de seguridad

Crear el archivo `staticwebapp.config.json` en la raíz del proyecto:

```bash
# Crear el archivo de configuración
touch staticwebapp.config.json
```

Contenido del archivo (ver sección 4 para el contenido completo):

```json
{
  "globalHeaders": {
    "Content-Security-Policy": "default-src 'self'; ...",
    "Strict-Transport-Security": "max-age=31536000; includeSubDomains; preload",
    "X-Content-Type-Options": "nosniff",
    "X-Frame-Options": "DENY",
    "Referrer-Policy": "no-referrer",
    "Permissions-Policy": "camera=(), microphone=(), geolocation=(), payment=()"
  }
}
```

### 2.5 — Subir el código al repositorio

```bash
# Agregar el repositorio remoto (reemplaza TU-USUARIO con tu usuario de GitHub)
git remote add origin https://github.com/TU-USUARIO/pokedex.git

# Agregar todos los archivos
git add .

# Primer commit
git commit -m "feat: initial deploy - PokeDex app with security headers"

# Subir al repositorio
git push -u origin main
```

> ⚠️ **Nota:** Si el branch por defecto es `master` en lugar de `main`, usa `git push -u origin master`

---

## 3. Creación del Recurso en Azure

### 3.1 — Acceder al Portal de Azure

1. Ingresa a [https://portal.azure.com](https://portal.azure.com)
2. Inicia sesión con tu cuenta institucional

### 3.2 — Crear un nuevo recurso Static Web Apps

1. En la barra de búsqueda superior, escribe **"Static Web Apps"**
2. Selecciona **"Static Web Apps"** en los resultados
3. Haz clic en **"+ Create"** (o **"+ Crear"**)

### 3.3 — Configurar el recurso

Completa el formulario con los siguientes valores:

**Pestaña: Basics**

| Campo | Valor |
|---|---|
| Subscription | Azure for Students |
| Resource Group | `rg-pokedex` (crear nuevo) |
| Name | `pokedex-pueblopaleta` |
| Plan type | Free |
| Region | East US 2 (o la más cercana disponible) |
| Source | GitHub |

**Conexión con GitHub:**

1. Haz clic en **"Sign in with GitHub"**
2. Autoriza a Azure para acceder a tu cuenta de GitHub
3. Selecciona:
   - **Organization:** tu usuario de GitHub
   - **Repository:** `pokedex`
   - **Branch:** `main`

**Build Details:**

| Campo | Valor |
|---|---|
| Build Presets | `Custom` |
| App location | `/` |
| Api location | (dejar vacío) |
| Output location | (dejar vacío) |

4. Haz clic en **"Review + Create"**
5. Revisa la configuración y haz clic en **"Create"**

### 3.4 — Esperar el despliegue automático

Azure automáticamente:
1. Crea un archivo de GitHub Actions en tu repositorio (`.github/workflows/azure-static-web-apps-*.yml`)
2. Ejecuta el pipeline de CI/CD
3. Despliega la aplicación

```bash
# Verificar que el archivo de workflow fue creado en tu repo
git pull origin main
dir .github\workflows\
# Deberías ver algo como: azure-static-web-apps-xxxx.yml
```

**Tiempo estimado:** 2-5 minutos

### 3.5 — Obtener la URL pública

1. En el portal de Azure, ve al recurso creado
2. En la sección **"Overview"**, copia la **URL** que aparece en el campo **"URL"**
3. La URL tendrá el formato: `https://TU-APP.azurestaticapps.net`

---

## 4. Configuración de Seguridad (Headers HTTP)

El archivo `staticwebapp.config.json` es el mecanismo nativo de Azure Static Web Apps para configurar encabezados HTTP. Debe estar en la **raíz del proyecto**.

### Contenido completo del archivo:

```json
{
  "globalHeaders": {
    "Content-Security-Policy": "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline'; img-src 'self' data: https://raw.githubusercontent.com https://pokeapi.co https://assets.pokemon.com; connect-src 'self' https://pokeapi.co; font-src 'self'; object-src 'none'; base-uri 'self'; form-action 'self'; frame-ancestors 'none'; upgrade-insecure-requests",
    "Strict-Transport-Security": "max-age=31536000; includeSubDomains; preload",
    "X-Content-Type-Options": "nosniff",
    "X-Frame-Options": "DENY",
    "Referrer-Policy": "no-referrer",
    "Permissions-Policy": "camera=(), microphone=(), geolocation=(), payment=()",
    "X-XSS-Protection": "1; mode=block"
  },
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/images/*.{png,jpg,gif,svg}", "/css/*", "/js/*"]
  },
  "mimeTypes": {
    ".json": "application/json",
    ".css": "text/css",
    ".js": "application/javascript",
    ".html": "text/html",
    ".svg": "image/svg+xml"
  }
}
```

### Explicación de cada encabezado:

| Encabezado | Configuración | Propósito |
|---|---|---|
| `Content-Security-Policy` | `default-src 'self'` + excepciones para PokeAPI | Previene XSS bloqueando recursos no autorizados |
| `Strict-Transport-Security` | `max-age=31536000; preload` | Fuerza HTTPS por 1 año con preload en navegadores |
| `X-Content-Type-Options` | `nosniff` | Bloquea detección automática de MIME types |
| `X-Frame-Options` | `DENY` | Impide que la app sea embebida en iframes |
| `Referrer-Policy` | `no-referrer` | No envía información de origen en peticiones |
| `Permissions-Policy` | Sin permisos a hardware | Reduce superficie de ataque del navegador |
| `X-XSS-Protection` | `1; mode=block` | Activa filtro XSS de navegadores legacy |

### Aplicar los cambios:

```bash
# Verificar que el archivo existe en la raíz
type staticwebapp.config.json

# Agregar, commit y push
git add staticwebapp.config.json
git commit -m "security: add HTTP security headers for A+ rating"
git push origin main
```

> ⚙️ El pipeline de Azure se ejecutará automáticamente y los headers quedarán activos en 2-3 minutos.

---

## 5. Verificación del Despliegue

### 5.1 — Verificar que la app carga correctamente

1. Abre la URL pública en el navegador: `https://TU-APP.azurestaticapps.net`
2. Verifica que la aplicación PokeDex carga sin errores
3. Abre las **DevTools** (`F12`) → pestaña **Console**
4. Confirma que no hay errores 404, 500 ni errores de JavaScript

### 5.2 — Verificar HTTPS

1. En la barra de direcciones del navegador, debe aparecer el **candado** 🔒
2. Haz clic en el candado → **"La conexión es segura"**

### 5.3 — Verificar los headers en DevTools

1. Abre DevTools (`F12`) → pestaña **Network**
2. Recarga la página (`Ctrl + R`)
3. Haz clic en la primera petición (el `index.html`)
4. Ve a la pestaña **"Headers"** → sección **"Response Headers"**
5. Confirma que aparecen los headers de seguridad configurados:

```
content-security-policy: default-src 'self'; ...
strict-transport-security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
x-frame-options: DENY
referrer-policy: no-referrer
permissions-policy: camera=(), microphone=(), ...
```

---

## 6. Auditoría de Seguridad

### 6.1 — Escaneo en securityheaders.com

1. Ingresa a [https://securityheaders.com](https://securityheaders.com)
2. En el campo de URL, escribe: `https://TU-APP.azurestaticapps.net`
3. Marca la opción **"Hide results"** si deseas privacidad
4. Haz clic en **"Scan"**

### 6.2 — Resultado esperado

Con la configuración del archivo `staticwebapp.config.json` incluido, el resultado esperado es:

```
Calificación: A+
```

Los siguientes headers deben aparecer en verde (✅):
- ✅ Content-Security-Policy
- ✅ Strict-Transport-Security
- ✅ X-Content-Type-Options
- ✅ X-Frame-Options
- ✅ Referrer-Policy
- ✅ Permissions-Policy

### 6.3 — Captura del resultado

Tomar captura de pantalla del resultado del escaneo para incluir en la entrega.

---

## 7. Errores Encontrados y Soluciones

### ❌ Error 1: Pipeline de GitHub Actions falla en el primer push

**Síntoma:**
```
Error: No such file or directory
```

**Causa:** La ruta del `App location` no coincide con la estructura del repositorio.

**Solución:**
Editar el archivo `.github/workflows/azure-static-web-apps-*.yml` y ajustar:
```yaml
app_location: "/"        # Raíz del repositorio
api_location: ""         # Sin API
output_location: ""      # Sin build step (app estática)
```

---

### ❌ Error 2: Recursos de PokéAPI bloqueados por CSP

**Síntoma:** En consola del navegador aparece:
```
Refused to connect to 'https://pokeapi.co/api/v2/pokemon' 
because it violates the following Content Security Policy directive: "connect-src 'self'"
```

**Causa:** El CSP por defecto bloquea peticiones a dominios externos.

**Solución:** Agregar `https://pokeapi.co` al `connect-src` del CSP:
```json
"Content-Security-Policy": "... connect-src 'self' https://pokeapi.co; ..."
```

---

### ❌ Error 3: Imágenes de Pokémon no cargan

**Síntoma:** Las imágenes de los Pokémon aparecen rotas (ícono de imagen rota).

**Causa:** Las imágenes se cargan desde `https://raw.githubusercontent.com` o `https://assets.pokemon.com`, que no están permitidos en el `img-src`.

**Solución:** Agregar las fuentes de imágenes al CSP:
```json
"Content-Security-Policy": "... img-src 'self' data: https://raw.githubusercontent.com https://pokeapi.co https://assets.pokemon.com; ..."
```

---

### ❌ Error 4: Calificación B en lugar de A+

**Síntoma:** El escaneo devuelve calificación B o A, no A+.

**Causa:** Falta el encabezado `Permissions-Policy` o `frame-ancestors` en el CSP.

**Solución:**
1. Verificar que `Permissions-Policy` esté presente en el `staticwebapp.config.json`
2. Agregar `frame-ancestors 'none'` al CSP (equivale a X-Frame-Options pero más moderno)
3. Hacer push y esperar 2-3 minutos para que Azure aplique los cambios
4. Volver a escanear en securityheaders.com

---

## ✅ Checklist Final

Antes de entregar, verificar:

- [ ] La aplicación carga en la URL pública sin errores
- [ ] No hay errores en la consola del navegador (F12)
- [ ] HTTPS activo (candado en la barra de direcciones)
- [ ] Los headers de seguridad son visibles en DevTools → Network → Headers
- [ ] El escaneo en securityheaders.com muestra calificación **A+**
- [ ] `README.md` y `Despliegue.md` están en el repositorio
- [ ] `staticwebapp.config.json` está en la raíz del repositorio

---

## 🔗 Recursos de Referencia

- [Documentación Azure Static Web Apps](https://docs.microsoft.com/azure/static-web-apps/)
- [Configuración de headers en Azure SWA](https://docs.microsoft.com/azure/static-web-apps/configuration)
- [securityheaders.com](https://securityheaders.com)
- [OWASP Secure Headers Project](https://owasp.org/www-project-secure-headers/)
- [Content Security Policy Reference](https://content-security-policy.com/)

---

*Documentación técnica elaborada para el caso de estudio Pueblo Paleta Inc. — Sistemas Distribuidos*
