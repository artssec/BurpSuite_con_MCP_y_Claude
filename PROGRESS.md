## [2026-05-30 04:32] burp-analyst — Reconocimiento Inicial

**Agente:** burp-analyst
**Objetivo:** https://juice-shop.herokuapp.com/
**Scope verificado en:** SCOPE.md

### Resumen de actividad

- Leído el historial completo de proxy HTTP de Burp Suite (100 entradas, 2 sesiones de navegación).
- Verificada conectividad puerto 80: HTTP/1.1 503 Service Unavailable (Heroku dyno dormido, sin redirect HTTPS explícito).
- Identificadas tecnologías, endpoints de API, parámetros y hallazgos de información sensible.

### Tecnologías identificadas

- OWASP Juice Shop v20.0.0 (Node.js / Express)
- Angular (SPA) con Angular Material UI
- Socket.IO (WebSocket real-time)
- Font Awesome, RxJS, Zone.js (Angular runtime)
- Plataforma: Heroku (dyno)
- LLM chatbot: gemma4:e4b (expuesto en configuración pública)
- Google OAuth 2.0 (client ID expuesto)

### Endpoints de API documentados

REST:
- GET /rest/admin/application-version → {"version":"20.0.0"} (sin auth)
- GET /rest/admin/application-configuration → config completa (sin auth, incluye client_id OAuth, baseUrl interno, modelo LLM)
- GET /rest/products/search?q= → búsqueda de productos (parámetro q, vector SQLi conocido)
- GET /rest/user/login (POST) → autenticación JWT
- GET /rest/user/change-password?current=&new=&repeat= → cambio password vía GET (inseguro)
- GET /rest/user/whoami → identidad usuario autenticado
- GET /rest/user/authentication-details/ → detalles de autenticación
- GET /rest/saveLoginIp → guarda IP de login
- GET /rest/deluxe-membership → estado membresía deluxe
- GET /rest/languages → lista de idiomas
- GET /rest/admin/application-configuration → configuración completa
- GET /rest/web3/nftUnlocked, nftMintListen, submitKey → endpoints Web3/NFT
- POST /rest/web3/walletNFTVerify, walletExploitAddress → wallets

RESTful CRUD (Sequelize/SQLite):
- GET /api/Challenges/ (con filtros name, key)
- GET /api/Users/
- GET /api/Quantitys/
- GET /socket.io/ → WebSocket real-time

### Hallazgos de seguridad

1. **[HIGH] Versión exacta expuesta sin autenticación** — /rest/admin/application-version → {"version":"20.0.0"}
2. **[HIGH] Configuración completa de la aplicación pública** — /rest/admin/application-configuration expone: baseUrl interno (http://localhost:3000), puerto (3000), Google OAuth client_id, modelo LLM (gemma4:e4b), chatbot config
3. **[HIGH] CORS incorrecto en Socket.IO** — Access-Control-Allow-Origin: http://localhost:4200 (internal dev origin leakeado en producción)
4. **[MEDIUM] Endpoint de cambio de contraseña por GET** — /rest/user/change-password?current=&new=&repeat= expone credenciales en URL/logs
5. **[MEDIUM] Puerto 80 activo sin redirect a HTTPS** — HTTP 503 (dyno dormido), no hay redirect 301 explícito a HTTPS
6. **[MEDIUM] ACAO: * (wildcard) en endpoints de API** — Access-Control-Allow-Origin: * en todos los endpoints REST y estáticos
7. **[INFO] X-Recruiting header** — X-Recruiting: /#/jobs en todas las respuestas
8. **[INFO] Feature-Policy header legacy** — Feature-Policy: payment 'self' (deprecado, debería ser Permissions-Policy)

### Próximos pasos sugeridos

- Pruebas de SQLi en /rest/products/search?q=
- Autenticación: pruebas de bypass en /rest/user/login
- Enumeración de usuarios via /api/Users/
- Exploración de endpoints Web3 (/rest/web3/)
- Verificar acceso no autenticado a /api/Challenges/ completo
