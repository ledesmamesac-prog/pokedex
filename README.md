# 🎮 PokeDex — Pueblo Paleta Inc.

> Aplicación web estática para explorar especies de Pokémon, desplegada de forma segura en Azure Static Web Apps.

---

## 📋 Tabla de Contenidos

- [Descripción del Proyecto](#descripción-del-proyecto)
- [Tecnologías Utilizadas](#tecnologías-utilizadas)
- [Creación de Cuenta en Azure](#creación-de-cuenta-en-azure)
- [Estructura del Repositorio](#estructura-del-repositorio)
- [Seguridad Implementada](#seguridad-implementada)
- [Reflexión Técnica](#reflexión-técnica)

---

## 📖 Descripción del Proyecto

**PokeDex** es una aplicación web desarrollada por PumasLab para Pueblo Paleta Inc. Permite a los usuarios explorar diferentes especies de Pokémon con información detallada sobre sus características y habilidades.

La aplicación es de tipo **web estática** (HTML, CSS y JavaScript puro), desplegada en **Azure Static Web Apps** con configuración de seguridad que obtiene calificación **A+** en [securityheaders.com](https://securityheaders.com).

---

## 🛠️ Tecnologías Utilizadas

| Tecnología | Propósito |
|---|---|
| HTML5 / CSS3 / JavaScript | Frontend estático |
| Azure Static Web Apps | Hosting en la nube |
| GitHub | Control de versiones y CI/CD |
| securityheaders.com | Auditoría de seguridad HTTP |

---

## ☁️ Creación de Cuenta en Azure

### Paso 1 — Registro en Azure for Students

1. Ingresa a [https://azure.microsoft.com/es-es/free/students/](https://azure.microsoft.com/es-es/free/students/)
2. Haz clic en **"Activar ahora"**.
3. Inicia sesión con tu **correo institucional** (ej: `estudiante@universidad.edu.co`).
4. Completa el formulario de verificación estudiantil:
   - Nombre completo
   - País
   - Fecha de nacimiento
5. Acepta los términos y condiciones.
6. Azure verificará automáticamente tu estado estudiantil mediante el correo institucional.
7. Una vez aprobado, recibirás **$100 USD de crédito gratuito** sin necesidad de tarjeta de crédito.

### Paso 2 — Acceso al Portal

1. Ingresa a [https://portal.azure.com](https://portal.azure.com)
2. Inicia sesión con las credenciales creadas.
3. Verifica que aparezca la suscripción **"Azure for Students"** en el panel principal.

> ✅ Con esto ya tienes acceso completo al portal para crear recursos.

---

## 📁 Estructura del Repositorio

```
pokedex/
├── index.html                  # Página principal de la aplicación
├── css/
│   └── styles.css              # Estilos de la aplicación
├── js/
│   └── app.js                  # Lógica de la aplicación
├── images/                     # Recursos visuales
├── staticwebapp.config.json    # ⚠️ Configuración de seguridad Azure
├── README.md                   # Este archivo
└── Despliegue.md               # Guía detallada de despliegue
```

---

## 🔒 Seguridad Implementada

La aplicación implementa los siguientes encabezados HTTP de seguridad configurados en `staticwebapp.config.json`:

| Encabezado | Valor | Propósito |
|---|---|---|
| `Content-Security-Policy` | `default-src 'self'` + reglas | Previene ataques XSS |
| `Strict-Transport-Security` | `max-age=31536000; preload` | Obliga uso de HTTPS |
| `X-Content-Type-Options` | `nosniff` | Evita MIME sniffing |
| `X-Frame-Options` | `DENY` | Bloquea clickjacking |
| `Referrer-Policy` | `no-referrer` | Protege información de navegación |
| `Permissions-Policy` | Sin acceso a cámara/mic/geo | Reduce superficie de ataque |

**Resultado en securityheaders.com:** `A+` ✅

---

## 🧠 Reflexión Técnica

### 1. ¿Qué vulnerabilidades previenen los encabezados implementados?

- **Content-Security-Policy**: Previene inyección de scripts maliciosos (XSS). Sin este encabezado, un atacante podría ejecutar código JavaScript arbitrario en el navegador del usuario.
- **Strict-Transport-Security**: Garantiza que todas las comunicaciones vayan cifradas por HTTPS, previniendo ataques de tipo *man-in-the-middle*.
- **X-Frame-Options: DENY**: Bloquea el *clickjacking*, técnica donde un atacante embebe la aplicación en un iframe invisible para engañar al usuario.
- **X-Content-Type-Options: nosniff**: Evita que el navegador interprete archivos con tipos MIME distintos al declarado, previniendo ejecución de código malicioso disfrazado.
- **Referrer-Policy: no-referrer**: Impide que las URLs internas o parámetros sensibles sean enviados a servidores externos al hacer clic en enlaces.

### 2. ¿Qué aprendiste sobre la relación entre despliegue y seguridad web?

El despliegue no termina cuando la aplicación es accesible públicamente. La seguridad debe ser parte integral del proceso desde el inicio. Una aplicación funcional pero insegura representa un riesgo real para los usuarios y la organización. Herramientas como `securityheaders.com` permiten identificar rápidamente las brechas de seguridad y corregirlas con configuraciones relativamente simples como los encabezados HTTP.

### 3. ¿Qué desafíos encontraste en el proceso?

- **Configuración del CSP**: El `Content-Security-Policy` requiere un balance entre seguridad y funcionalidad. Políticas demasiado estrictas pueden bloquear recursos legítimos de la app.
- **HTTPS automático en Azure**: Azure Static Web Apps provee HTTPS automáticamente, pero fue necesario asegurarse de que el encabezado HSTS estuviera correctamente configurado para evitar redirecciones inseguras.
- **Prueba iterativa**: Cada cambio en los headers requería verificación en el escáner, lo que implicó varios ciclos de ajuste y validación.

---

## 🔗 URL de la Aplicación

> 🌐 [https://TU-APP.azurestaticapps.net](https://TU-APP.azurestaticapps.net)

*(Reemplaza con la URL real generada por Azure después del despliegue)*

---

*Desarrollado como parte del caso de estudio de Pueblo Paleta Inc. — Sistemas Distribuidos*
