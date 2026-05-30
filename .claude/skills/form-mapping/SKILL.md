---
name: "form-mapping"
description: "Directivas para registrar y documentar la estructura de formularios web interactivos en FORM.md."
---

## Objetivo
Centralizar todos los puntos de entrada de datos (formularios) identificados durante la navegación para su posterior análisis de vulnerabilidades (SQLi, XSS, CSRF, Auth Bypass, etc.).

## Directivas de Archivo y Escritura
1. **Archivo Objetivo:** Escribir obligatoriamente en `FORM.md` en la raíz del proyecto.
2. **Modo Append:** Si el archivo ya existe, se debe adjuntar (`append`) la información al final del documento. Si no existe, se crea inicializando con el título principal.

## Estructura del Registro en FORM.md

Cada formulario descubierto debe apendizarse usando el siguiente formato:

```markdown
### 📋 Formulario Detectado en: `[Ruta_o_Nombre_de_la_Sección]`
- **URL Origen:** `https://www.spanishdict.com/translate/completa`
- **Método HTTP:** `[POST / GET / PUT]`
- **Action URL:** `[Atributo action del form]`

#### Campos Identificados:
| Atributo `name` | Tipo (`type`) | Marcador (`placeholder`) | Requerido | Notas / Propósito Estimado |
|-----------------|---------------|--------------------------|-----------|----------------------------|
| `username`      | `text`        | "Ingrese su usuario"     | SÍ        | Input de autenticación     |
| `password`      | `password`    | "********"               | SÍ        | Contraseña                 |

---