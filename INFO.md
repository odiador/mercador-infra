📘 Proyecto Auth con Supabase + Hono + Zod OpenAPI
🚀 Stack

Runtime: Bun / Node.js

Framework API: Hono

Validación & Docs: Zod
 + @hono/zod-openapi

Auth & DB: Supabase

Linter/Formatter: ESLint + Prettier

📂 Estructura del Proyecto
├── config/
│   └── supabase.ts         # Cliente de Supabase
├── middlewares/
│   └── authMiddleware.ts   # Middleware para validar JWT de Supabase
├── routes/
│   └── auth.ts             # Rutas de autenticación (signup, login, google, me, logout)
├── services/
│   └── user.service.ts     # Lógica de usuarios (signup, login, getUserById, etc.)
├── types/                  # Tipos globales si hacen falta
├── utils/                  # Utilidades
├── index.ts                # Entry point principal
└── README.md               # Documentación del proyecto

⚙️ Configuración
1. Variables de entorno

Crea un archivo .env:

SUPABASE_URL=https://<your-project>.supabase.co
SUPABASE_ANON_KEY=<public-anon-key>
SUPABASE_SERVICE_ROLE_KEY=<service-role-key>

2. Cliente de Supabase (config/supabase.ts)
import { createClient } from '@supabase/supabase-js'

const supabaseUrl = process.env.SUPABASE_URL!
const supabaseAnonKey = process.env.SUPABASE_ANON_KEY!

export const supabase = createClient(supabaseUrl, supabaseAnonKey)

🔑 Autenticación

Se soportan dos métodos:

Email + Password

Registro (/signup)

Login (/login)

OAuth con Google

Redirección a Google (/login/google)

Además:

POST /logout → Invalida sesión (normalmente se maneja en el cliente).

GET /me → Devuelve el perfil del usuario autenticado (requiere token válido).

🛡️ Middleware de Auth (middlewares/authMiddleware.ts)

Este middleware protege rutas privadas validando el Bearer Token de Supabase:

const authHeader = c.req.header('authorization')
if (!authHeader?.startsWith('Bearer ')) return 401
const token = authHeader.replace('Bearer ', '')
const { data, error } = await supabase.auth.getUser(token)


Si el token es válido, guarda en el contexto:

userId

userEmail

userRole (por defecto "cliente")

🧩 Endpoints Principales (routes/auth.ts)

POST /signup → Crea usuario con email y password.

POST /login → Login con email y password.

GET /login/google → Retorna la URL para OAuth con Google.

POST /logout → Cierra sesión (cliente).

GET /me → Devuelve el usuario autenticado (requiere Bearer Token).

La documentación OpenAPI se genera automáticamente gracias a zod-openapi.

📖 OpenAPI Docs

En tu index.ts debes montar algo así:

import { OpenAPIHono } from '@hono/zod-openapi'
import authRoutes from './routes/auth'

const app = new OpenAPIHono()

app.route('/auth', authRoutes)

// Swagger UI
app.doc('/doc', {
  openapi: '3.0.0',
  info: { title: 'API Docs', version: '1.0.0' }
})

export default app


Luego navega a /doc para ver la documentación.