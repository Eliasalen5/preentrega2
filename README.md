# Turnos y Reservas — Pre-entrega 2 (API REST de servicios)

API REST construida con **Express** y **ESM** que expone endpoints para gestionar el
recurso `services` de un sistema de turnos y reservas. Las rutas se conectan con el
`ServiceManager` de la pre-entrega anterior, donde permanece toda la lógica de negocio.

## Requisitos

- Node.js 18 o superior (usa módulos ESM nativos).

## Instalación

```bash
npm install
```

## Variables de entorno

Creá un archivo `.env` en la raíz del proyecto, tomando como base `.env.example`:

| Variable   | Descripción            | Ejemplo       |
| ---------- | ---------------------- | ------------- |
| `PORT`     | Puerto del servidor    | `8080`        |
| `NODE_ENV` | Entorno de ejecución   | `development` |

```
PORT=8080
NODE_ENV=development
```

Si falta alguna de estas variables, la aplicación **falla al iniciar** con un mensaje
claro (validación en `src/config/env.config.js`).

## Ejecución

```bash
npm start
```

o en modo desarrollo (reinicio automático):

```bash
npm run dev
```

Por defecto el servidor queda disponible en `http://localhost:8080`.

## Estructura del proyecto

```
src/
  config/env.config.js         # Carga y valida las variables de entorno
  managers/ServiceManager.js   # Lógica de negocio de los servicios
  routes/services.router.js    # Router de Express con los endpoints
  data/services.json           # Persistencia de los servicios
  app.js                       # Configuración de la app Express (monta el router)
  server.js                    # Punto de entrada: levanta el servidor
package.json
.env.example
.gitignore
README.md
```

> `app.js` solo configura Express y monta el router. La lógica de servicios vive
> exclusivamente en `ServiceManager`; `server.js` únicamente inicia el servidor.

## Recurso `services`

Cada servicio tiene la siguiente forma:

```json
{
  "id": 1,
  "name": "Consulta general",
  "description": "Consulta médica general de 30 minutos",
  "duration": 30,
  "price": 5000,
  "category": "salud",
  "available": true
}
```

| Campo         | Tipo      | Descripción                                 |
| ------------- | --------- | ------------------------------------------- |
| `id`          | `number`  | Identificador único (generado internamente) |
| `name`        | `string`  | Nombre del servicio                         |
| `description` | `string`  | Descripción del servicio                    |
| `duration`    | `number`  | Duración en minutos                         |
| `price`       | `number`  | Precio del servicio                         |
| `category`    | `string`  | Categoría del servicio                      |
| `available`   | `boolean` | Si el servicio está disponible              |

## Endpoints

| Método   | Ruta                 | Descripción                                            | Códigos        |
| -------- | -------------------- | ------------------------------------------------------ | -------------- |
| `GET`    | `/api/services`      | Lista todos los servicios (acepta `?category=` y `?available=`) | 200            |
| `GET`    | `/api/services/:sid` | Obtiene un servicio por id                             | 200 / 404      |
| `POST`   | `/api/services`      | Crea un servicio (id generado automáticamente)         | 201 / 400      |
| `PUT`    | `/api/services/:sid` | Actualiza un servicio (no permite cambiar el id)       | 200 / 404      |
| `DELETE` | `/api/services/:sid` | Elimina un servicio                                    | 200 / 404      |

### Ejemplos

Listar todos los servicios:

```bash
curl http://localhost:8080/api/services
```

Filtrar por categoría y disponibilidad (query params):

```bash
curl "http://localhost:8080/api/services?category=salud"
curl "http://localhost:8080/api/services?available=true"
```

Obtener un servicio por id:

```bash
curl http://localhost:8080/api/services/1
```

Crear un servicio (el `id` se genera internamente, no se envía en el body):

```bash
curl -X POST http://localhost:8080/api/services \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Sesión de kinesiología",
    "description": "Sesión de kinesiología de 40 minutos",
    "duration": 40,
    "price": 7000,
    "category": "salud",
    "available": true
  }'
```

Actualizar un servicio:

```bash
curl -X PUT http://localhost:8080/api/services/1 \
  -H "Content-Type: application/json" \
  -d '{ "price": 5500 }'
```

Eliminar un servicio:

```bash
curl -X DELETE http://localhost:8080/api/services/1
```
