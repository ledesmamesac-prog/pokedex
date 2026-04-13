# 🎮 PokeDex — Pueblo Paleta Inc.

> Aplicación web Angular para explorar especies de Pokémon, desplegada de forma segura en Azure Static Web Apps con calificación **A+** en seguridad.

---

## 📋 Tabla de Contenidos

- [Descripción del Proyecto](#descripción-del-proyecto)
- [Tecnologías Utilizadas](#tecnologías-utilizadas)
- [Estructura del Repositorio](#estructura-del-repositorio)
- [Seguridad Implementada](#seguridad-implementada)
- [URL de la Aplicación](#url-de-la-aplicación)
- [Reflexión Técnica](#reflexión-técnica)

---

## 📖 Descripción del Proyecto

**PokeDex** es una aplicación web desarrollada por PumasLab para Pueblo Paleta Inc. Permite a los usuarios explorar diferentes especies de Pokémon con información detallada sobre sus características y habilidades.

La aplicación está construida en **Angular** y consume datos en tiempo real desde la **PokeAPI GraphQL** (`beta.pokeapi.co/graphql/v1beta`). Está desplegada en **Azure Static Web Apps** con configuración de seguridad que obtiene calificación **A+** en [securityheaders.com](https://securityheaders.com).

---

## 🛠️ Tecnologías Utilizadas

| Tecnología | Propósito |
|---|---|
| Angular | Framework frontend |
| TypeScript | Lenguaje de programación |
| SCSS | Estilos avanzados |
| PokeAPI GraphQL | Fuente de datos de Pokémon |
| Azure Static Web Apps | Hosting en la nube |
| GitHub Actions | CI/CD automatizado |
| securityheaders.com | Auditoría de seguridad HTTP |

---

## 📁 Estructura del Repositorio

```
pokedex/
├── .github/
│   └── workflows/
│       └── azure-static-web-apps-mango-stone-0306f7110.yml  # CI/CD pipeline
├── source/
│   └── pokedex-angular/          # Proyecto Angular principal
│       ├── src/
│       │   ├── app/              # Lógica de la aplicación
│       │   ├── assets/           # Imágenes, fuentes y estilos
│       │   ├── environments/     # Variables de entorno (dev/prod)
│       │   ├── index.html        # Plantilla HTML principal
│       │   ├── styles.scss       # Estilos globales
│       │   └── staticwebapp.config.json  # Headers de seguridad
│       ├── angular.json          # Configuración de Angular CLI
│       ├── package.json          # Dependencias y scripts
│       └── tsconfig.json         # Configuración de TypeScript
├── staticwebapp.config.json      # Configuración de seguridad Azure
├── README.md                     # Este archivo
└── Despliegue.md                 # Guía detallada de despliegue
```

---

## 🔒 Seguridad Implementada

La aplicación implementa los siguientes encabezados HTTP configurados en `staticwebapp.config.json`:

| Encabezado | Valor | Propósito |
|---|---|---|
| `Content-Security-Policy` | `default-src 'self'` + reglas específicas | Previene ataques XSS |
| `Strict-Transport-Security` | `max-age=31536000; preload` | Obliga uso de HTTPS por 1 año |
| `X-Content-Type-Options` | `nosniff` | Evita MIME sniffing |
| `X-Frame-Options` | `DENY` | Bloquea clickjacking |
| `Referrer-Policy` | `no-referrer` | Protege información de navegación |
| `Permissions-Policy` | Sin acceso a cámara/mic/geo | Reduce superficie de ataque |
| `X-XSS-Protection` | `1; mode=block` | Activa filtro XSS en navegadores legacy |

**Resultado en securityheaders.com:** `A+` ✅

---

## 🌐 URL de la Aplicación

> 🚀 [https://mango-stone-0306f7110.6.azurestaticapps.net](https://mango-stone-0306f7110.6.azurestaticapps.net)

**Repositorio:** [https://github.com/ledesmamesac-prog/pokedex](https://github.com/ledesmamesac-prog/pokedex)

---

## 🧠 Reflexión Técnica

### 1. ¿Qué vulnerabilidades previenen los encabezados implementados?

- **Content-Security-Policy (CSP):** Previene inyección de scripts maliciosos (XSS). Define exactamente qué recursos puede cargar la app: scripts solo desde el propio dominio, estilos desde Google Fonts, imágenes y conexiones solo hacia la PokeAPI. Sin este encabezado, un atacante podría ejecutar código JavaScript arbitrario en el navegador del usuario robando sesiones o datos.

- **Strict-Transport-Security (HSTS):** Garantiza que todas las comunicaciones vayan cifradas por HTTPS durante un año completo, incluyendo subdominios. Previene ataques de tipo *man-in-the-middle* donde un atacante intercepta el tráfico entre el usuario y el servidor.

- **X-Frame-Options: DENY:** Bloquea el *clickjacking*, técnica donde un atacante embebe la aplicación en un iframe invisible para engañar al usuario y que haga clic en elementos sin saberlo.

- **X-Content-Type-Options: nosniff:** Evita que el navegador interprete archivos con tipos MIME distintos al declarado, previniendo que un archivo disfrazado de imagen ejecute código malicioso.

- **Referrer-Policy: no-referrer:** Impide que las URLs internas o parámetros sensibles sean enviados a servidores externos al hacer clic en enlaces, protegiendo información de navegación del usuario.

- **Permissions-Policy:** Bloquea explícitamente el acceso a cámara, micrófono y geolocalización, reduciendo la superficie de ataque del navegador ante posibles vulnerabilidades futuras.

### 2. ¿Qué aprendiste sobre la relación entre despliegue y seguridad web?

El despliegue no termina cuando la aplicación es accesible públicamente. La seguridad debe ser parte integral del proceso desde el inicio. Una aplicación funcional pero insegura representa un riesgo real para los usuarios y la organización.

Durante este proceso aprendí que configurar correctamente el CSP requiere un balance entre seguridad y funcionalidad. Políticas demasiado estrictas pueden romper la aplicación — como ocurrió al bloquear las fuentes de Google Fonts y la PokeAPI GraphQL — y es necesario iterar para encontrar el punto óptimo. Herramientas como `securityheaders.com` son fundamentales para verificar el resultado de forma objetiva.

### 3. ¿Qué desafíos encontraste en el proceso?

- **Rutas de assets incorrectas:** La variable `imagesPath` en `environment.prod.ts` apuntaba a `/pokedex-angular/assets/images/` en lugar de `/assets/images/`, lo que impedía que las imágenes de los Pokémon cargaran en producción. Se resolvió corrigiendo la ruta en el archivo de entorno de producción.

- **Configuración del workflow de GitHub Actions:** El archivo CI/CD generado automáticamente por Azure tenía rutas incorrectas (`app_location` y `output_location`), lo que causó varios fallos en el pipeline antes de encontrar la configuración correcta.

- **CSP y APIs externas:** El Content-Security-Policy inicial bloqueaba las peticiones a `beta.pokeapi.co/graphql/v1beta` y las fuentes de Google Fonts. Fue necesario inspeccionar los errores en la consola del navegador e iterar sobre la configuración para permitir solo los recursos legítimos que la app necesita.

- **Calificación A+:** Para pasar de A a A+ fue necesario eliminar `unsafe-inline` y `unsafe-eval` del `script-src`, lo cual requirió verificar que Angular compilado no dependiera de estas directivas en producción.

---

*Desarrollado como parte del caso de estudio de Pueblo Paleta Inc. — Sistemas Distribuidos 2026*
