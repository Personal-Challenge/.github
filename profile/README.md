# Arquitectura

Este repositorio orquesta una aplicacion compuesta por frontend, backend y MongoDB usando Docker Compose.

```mermaid
flowchart LR
    user[Usuario / Navegador]

    subgraph host[Maquina host]
        subgraph compose[Docker Compose]
            subgraph network[personal-todo-network]
                frontend[frontend\nReact + Vite + Nginx\npuerto 3000]
                backend[backend\nNode.js + Express + Mongoose\npuerto 8080]
                mongo[(mongo\nMongoDB\npuerto 27017)]
            end

            volume[(mongo_data\n/data/db)]
        end
    end

    user -->|HTTP puerto 3000| frontend
    frontend -->|HTTP API| backend
    backend -->|MONGO_URI via hostname mongo| mongo
    mongo -->|persistencia| volume
```

## Servicios

| Servicio | Contenedor | Tecnologia | Puerto host | Uso |
| --- | --- | --- | --- | --- |
| `frontend` | `personal-todo-frontend` | React, Vite, Nginx | `3000` | Interfaz web |
| `backend` | `personal-todo-backend` | Node.js, Express, Mongoose | `8080` | API REST |
| `mongo` | `personal-todo-mongo` | MongoDB | `27017` | Base de datos local |

## Variables clave

El frontend usa:

```env
VITE_API_URL=http://localhost:8080
```

El navegador accede al frontend en:

```text
http://localhost:3000
```

El backend usa:

```env
MONGO_URI=mongodb://admin:admin123@mongo:27017/todoapp?authSource=admin
```

## Flujo de ejecucion

1. `setup.sh` clona o actualiza los repositorios `backend/` y `frontend/` desde la rama `main`.
2. Docker Compose construye las imagenes usando `./backend` y `./frontend` como contextos.
3. `mongo` levanta dentro de la red `personal-todo-network` y persiste datos en `mongo_data`.
4. `backend` se conecta a MongoDB usando el hostname Docker `mongo`.
5. `frontend` se sirve con Nginx y consume la API expuesta en `localhost:8080`.
6. `make seed` importa `seeds/tasks.json` en la coleccion `tasks`.
