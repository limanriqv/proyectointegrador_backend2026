## PROYECTO FINAL INTEGRADOR

API REST para consultar y administrar productos almacenados en Cloud Firestore. Incluye inicio de sesión y protege las operaciones de creación y eliminación con tokens JWT.

## Requisitos

- Node.js y npm.
- Un proyecto de Firebase con Firestore habilitado.
- Las credenciales de configuración de Firebase y una clave secreta para JWT.

## Instalación y configuración

Desde esta carpeta, instala las dependencias:

```bash
npm install
```

Crea un archivo `.env` en `PFI/` con estas variables:

```env
apiKey=tu_api_key
authDomain=tu_auth_domain
storageBucket=tu_storage_bucket
appId=tu_app_id
JWT_SECRET_KEY=una_clave_secreta
```

La configuración de Firebase también fija `projectId` y `messagingSenderId` en `src/data/data.js`. Si usas otro proyecto, actualiza esos valores allí. La colección de Firestore utilizada por la API se llama `products`; cada documento puede contener los campos del producto y debe usar `categoria` para el filtro por categoría.

## Iniciar el servidor

```bash
npm start
```

El servidor escucha en `http://localhost:3000`. El script `npm run dev` inicia Nodemon si está instalado en el entorno.

## API

Todas las respuestas se envían en JSON. Para los endpoints protegidos, envía el token como `Authorization: Bearer <token>`.

| Método | Ruta | Descripción | Autenticación |
| --- | --- | --- | --- |
| `POST` | `/auth/login` | Inicia sesión y devuelve un JWT. | No |
| `GET` | `/api/products` | Lista todos los productos. | No |
| `GET` | `/api/products?category=valor` | Lista productos cuya `categoria` coincide exactamente con `valor`. | No |
| `GET` | `/api/products/:id` | Obtiene un producto por su ID de Firestore. | No |
| `POST` | `/api/products/create` | Crea un producto con el JSON del cuerpo de la solicitud. | Sí |
| `DELETE` | `/api/products/:id` | Elimina el producto indicado. | Sí |

### Iniciar sesión

```bash
curl -X POST http://localhost:3000/auth/login \
  -H 'Content-Type: application/json' \
  -d '{"email":"admin@gmail.com","password":"123456"}'
```

Si las credenciales son válidas, la respuesta contiene un token que expira en una hora:

```json
{
  "token": "<jwt>"
}
```

Las credenciales de ejemplo están definidas directamente en `src/controllers/auth.controller.js`. Cámbialas antes de exponer el servicio a otros usuarios.

### Listar productos

```bash
curl 'http://localhost:3000/api/products'
curl 'http://localhost:3000/api/products?category=indumentaria'
```

### Consultar un producto

```bash
curl http://localhost:3000/api/products/ID_DEL_DOCUMENTO
```

### Crear un producto

```bash
curl -X POST http://localhost:3000/api/products/create \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer <jwt>' \
  -d '{"nombre":"Remera","categoria":"indumentaria","precio":12000}'
```

El cuerpo se guarda como documento en Firestore; los campos del ejemplo son ilustrativos.

### Eliminar un producto

```bash
curl -X DELETE http://localhost:3000/api/products/ID_DEL_DOCUMENTO \
  -H 'Authorization: Bearer <jwt>'
```

## Estructura

```text
PFI/
├── index.js                  # Configuración e inicio de Express
└── src/
    ├── controllers/          # Controladores de autenticación y productos
    ├── data/                 # Inicialización de Firebase y Firestore
    ├── middlewares/          # Validación del JWT
    ├── models/               # Consultas y escrituras en Firestore
    ├── routes/               # Rutas HTTP
    ├── services/             # Lógica de productos
    └── utils/                # Generación de tokens