# FORM.md — Mapa de Formularios Descubiertos
**Target:** https://juice-shop.herokuapp.com/
**Agente:** web-crawler
**Sesión:** 2026-05-30

---

### Formulario Detectado en: `Login`
- **URL Origen:** `https://juice-shop.herokuapp.com/#/login`
- **Método HTTP:** `POST`
- **Action URL:** `/rest/user/login`

#### Campos Identificados:
| Atributo `name` | Tipo (`type`) | Marcador (`placeholder`) | Requerido | Notas / Propósito Estimado |
|-----------------|---------------|--------------------------|-----------|----------------------------|
| `email`         | `email`       | —                        | SÍ        | Input de autenticación / vector SQLi |
| `password`      | `password`    | —                        | SÍ        | Contraseña |
| `rememberMe`    | `checkbox`    | —                        | NO        | Persistencia de sesión |

**Notas:** Angular SPA — sin tag `<form>` estándar. También presenta botón Google OAuth (`/auth/google`). Enlaza a `#/forgot-password` y `#/register`.

---

### Formulario Detectado en: `User Registration`
- **URL Origen:** `https://juice-shop.herokuapp.com/#/register`
- **Método HTTP:** `POST`
- **Action URL:** `/api/Users/`

#### Campos Identificados:
| Atributo `name`    | Tipo (`type`) | Marcador (`placeholder`)               | Requerido | Notas / Propósito Estimado |
|--------------------|---------------|----------------------------------------|-----------|----------------------------|
| `email`            | `email`       | —                                      | SÍ        | Dirección email del nuevo usuario |
| `password`         | `password`    | —                                      | SÍ        | Contraseña (5-40 chars) |
| `repeatPassword`   | `password`    | —                                      | SÍ        | Confirmación de contraseña |
| `showAdvice`       | `switch`      | —                                      | NO        | Toggle de consejo de contraseña |
| `securityQuestion` | `combobox`    | —                                      | SÍ        | Pregunta de seguridad (inmutable post-registro) |
| `securityAnswer`   | `text`        | "Answer to your security question"     | SÍ        | Respuesta a pregunta de seguridad |

**Notas:** El campo `securityQuestion` es un combobox Angular con lista predefinida. Nota de UI: "This cannot be changed later!" — posible vector de enumeración de preguntas. Enlaza a `#/login`.

---

### Formulario Detectado en: `Forgot Password`
- **URL Origen:** `https://juice-shop.herokuapp.com/#/forgot-password`
- **Método HTTP:** `POST`
- **Action URL:** `/rest/user/reset-password`

#### Campos Identificados:
| Atributo `name`     | Tipo (`type`) | Marcador (`placeholder`) | Requerido | Notas / Propósito Estimado |
|---------------------|---------------|--------------------------|-----------|----------------------------|
| `email`             | `email`       | "Enter your email"       | SÍ        | Lookup de cuenta — vector de enumeración de usuarios |
| `securityAnswer`    | `text`        | —                        | SÍ        | Respuesta a pregunta de seguridad (habilitado tras lookup de email) |
| `newPassword`       | `password`    | —                        | SÍ        | Nueva contraseña (5-40 chars, deshabilitado hasta lookup) |
| `repeatNewPassword` | `password`    | —                        | SÍ        | Confirmación de nueva contraseña |
| `showAdvice`        | `switch`      | —                        | NO        | Toggle de consejo de contraseña |

**Notas:** Flujo de dos pasos — campos de contraseña deshabilitados hasta que se ingresa email y se resuelve pregunta de seguridad. Alto riesgo: enumeración de usuarios + bypass de autenticación.

---

### Formulario Detectado en: `Customer Feedback`
- **URL Origen:** `https://juice-shop.herokuapp.com/#/contact`
- **Método HTTP:** `POST`
- **Action URL:** `/api/Feedbacks/`

#### Campos Identificados:
| Atributo `name` | Tipo (`type`) | Marcador (`placeholder`)                        | Requerido | Notas / Propósito Estimado |
|-----------------|---------------|-------------------------------------------------|-----------|----------------------------|
| `author`        | `text`        | "anonymous"                                     | NO        | Nombre del autor (deshabilitado/pre-rellenado como "anonymous") |
| `comment`       | `textarea`    | "What did you like or dislike?"                 | SÍ        | Texto del feedback (max 160 chars) — vector XSS almacenado |
| `rating`        | `slider`      | —                                               | SÍ        | Rating 1-5 estrellas |
| `captchaAnswer` | `text`        | "Please enter the result of the CAPTCHA code."  | SÍ        | Respuesta a CAPTCHA matemático (ej: `4+6-4`) |

**Notas:** CAPTCHA matemático — potencialmente bypasseable. Campo `author` deshabilitado para usuarios no autenticados (muestra "anonymous"). Campo `comment` es vector principal de XSS almacenado.

---

### Formulario Detectado en: `AI Chatbot`
- **URL Origen:** `https://juice-shop.herokuapp.com/#/chatbot`
- **Método HTTP:** `POST`
- **Action URL:** `/rest/chatbot/respond`

#### Campos Identificados:
| Atributo `name` | Tipo (`type`) | Marcador (`placeholder`) | Requerido | Notas / Propósito Estimado |
|-----------------|---------------|--------------------------|-----------|----------------------------|
| `query`         | `text`        | "Ask me anything"        | SÍ        | Entrada libre al chatbot — vector de prompt injection / XSS |

**Notas:** Chatbot LLM. Tiene botones de sugerencia rápida: "Get a suggestion" y "Get help". Vector potencial de prompt injection. Botón "Send message" deshabilitado hasta que se escribe texto. Requiere autenticación para funcionar completamente.

---
