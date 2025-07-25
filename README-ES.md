# Mi Proyecto FastAPI

[🇺🇸 English](README.md) | 🇪🇸 Español

Una API REST robusta construida con FastAPI y SQLAlchemy que implementa un sistema de gestión de cuentas de usuario, jugadores, gremios y descargas con autenticación JWT, autorización de administrador y una arquitectura sofisticada de cuádruple base de datos.

## 🚀 Características

- **Autenticación JWT**: Sistema de autenticación seguro basado en tokens
- **Arquitectura de Cuádruple Base de Datos**: Separa datos de aplicación principal, cuenta legacy, jugador/gremio legacy y datos administrativos en bases de datos independientes
- **Autorización de Administrador**: Control de acceso basado en roles con niveles de autoridad
- **Gestión de Descargas**: Sistema completo de gestión de archivos/contenido con funcionalidad de publicar/despublicar
- **API RESTful**: Endpoints bien estructurados siguiendo las mejores prácticas
- **Modelos Base Personalizados**: Operaciones CRUD específicas por base de datos con métodos integrados
- **Validación de Datos**: Esquemas Pydantic para validación robusta de entrada y salida
- **CORS Configurado**: Listo para integración con aplicaciones frontend
- **Documentación Automática**: Swagger UI y ReDoc incluidos
- **Soporte Docker**: Contenedorización completa con verificaciones de salud

## 🛠️ Tecnologías

- **FastAPI**: Framework web moderno y rápido
- **SQLAlchemy**: ORM de Python con soporte multi-base de datos
- **MySQL**: Base de datos relacional
- **PyMySQL**: Conector MySQL para Python
- **JWT**: Autenticación basada en tokens
- **Pydantic**: Validación y serialización de datos
- **Uvicorn**: Servidor ASGI de alto rendimiento
- **Docker**: Plataforma de contenedorización
- **Docker Compose**: Orquestación multi-contenedor

## 📁 Estructura del Proyecto

```
my_fastapi_project/
├── app/
│   ├── api/
│   │   ├── deps.py              # Inyección de dependencias
│   │   └── routes/
│   │       ├── account.py       # Endpoints de cuentas
│   │       └── game.py          # Endpoints de juego (jugadores, gremios)
│   ├── core/
│   │   ├── hashers.py          # Utilidades de hash de contraseñas
│   │   └── security.py         # JWT y seguridad
│   ├── crud/
│   │   ├── account.py          # Operaciones CRUD para cuentas
│   │   ├── download.py         # Operaciones CRUD para descargas
│   │   └── common.py           # Operaciones CRUD para funciones admin
│   ├── models/
│   │   ├── account.py          # Modelo de cuenta de usuario
│   │   ├── player.py           # Modelos de jugador y gremio
│   │   ├── application.py      # Modelos de descarga y aplicación
│   │   └── common.py           # Modelos de admin/GM
│   ├── schemas/
│   │   ├── account.py          # Esquemas Pydantic para cuentas
│   │   ├── player.py           # Esquemas de jugadores
│   │   ├── download.py         # Esquemas de descargas
│   │   └── common.py           # Esquemas de admin
│   ├── config.py               # Configuración de la aplicación
│   ├── database.py             # Configuración multi-base de datos
│   └── main.py                 # Punto de entrada de la aplicación
├── compose/
│   └── api/
│       ├── Dockerfile          # Definición de imagen Docker
│       ├── entrypoint.sh       # Script de inicio del contenedor
│       └── init.sql            # Inicialización de base de datos
├── docker-compose.yml          # Orquestación de servicios
├── requirements.txt            # Dependencias de Python
├── .env                        # Variables de entorno
├── .gitignore                  # Reglas de ignorar para Git
├── .dockerignore              # Reglas de ignorar para Docker
├── CLAUDE.md                   # Guía de desarrollo
├── README.md                   # Documentación del proyecto (Inglés)
└── README-ES.md               # Documentación del proyecto (Español)
```

## 🚀 Instalación y Configuración

### Requisitos Previos

- Python 3.8+
- Servidor MySQL (para desarrollo local)
- Docker & Docker Compose (para despliegue en contenedores)
- pip (gestor de paquetes de Python)

### Inicio Rápido con Docker (Recomendado)

1. **Clonar el repositorio**
   ```bash
   git clone <repository-url>
   cd my_fastapi_project
   ```

2. **Crear archivo de entorno**
   ```bash
   cp .env.example .env
   # Editar .env con tu configuración
   ```

3. **Ejecutar con Docker Compose**
   ```bash
   docker-compose up --build
   ```

4. **Acceder a la aplicación**
   - API: http://localhost:8000
   - Documentación API: http://localhost:8000/docs
   - MySQL: localhost:3307

### Instalación para Desarrollo Local

1. **Clonar el repositorio**
   ```bash
   git clone <repository-url>
   cd my_fastapi_project
   ```

2. **Crear entorno virtual**
   ```bash
   python -m venv venv
   source venv/bin/activate  # En Windows: venv\\Scripts\\activate
   ```

3. **Instalar dependencias**
   ```bash
   pip install -r requirements.txt
   ```

4. **Configurar variables de entorno**
   Crear un archivo `.env` en la raíz del proyecto:
   ```env
   DATABASE_URL_APP=mysql+pymysql://usuario:contraseña@host:puerto/application
   DATABASE_URL_ACCOUNT=mysql+pymysql://usuario:contraseña@host:puerto/srv1_account
   DATABASE_URL_PLAYER=mysql+pymysql://usuario:contraseña@host:puerto/srv1_player
   DATABASE_URL_COMMON=mysql+pymysql://usuario:contraseña@host:puerto/srv1_common
   SECRET_KEY=tu-clave-secreta-muy-segura
   ALGORITHM=HS256
   ACCESS_TOKEN_EXPIRE_MINUTES=30
   ```

5. **Crear bases de datos**
   ```sql
   CREATE DATABASE application;
   ```

6. **Ejecutar la aplicación**
   ```bash
   python -m app.main
   # O usar uvicorn directamente
   uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
   ```

## 📚 Endpoints de la API

### Autenticación

#### Registro de Cuenta
```http
POST /api/account/register
Content-Type: application/json

{
  "login": "usuario123",
  "password": "contraseña_segura",
  "email": "usuario@ejemplo.com",
  "social_id": "1234567"
}
```

#### Inicio de Sesión
```http
POST /api/account/token
Content-Type: application/x-www-form-urlencoded

username=usuario123&password=contraseña_segura
```

**Respuesta:**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "bearer"
}
```

### Gestión de Cuentas

#### Obtener Información Personal
```http
GET /api/account/me
Authorization: Bearer <token>
```

#### Actualizar Información
```http
PUT /api/account/me
Authorization: Bearer <token>
Content-Type: application/json

{
  "social_id": "7654321",
  "email": "nuevoemail@ejemplo.com"
}
```

#### Cambiar Contraseña
```http
PUT /api/account/me/password
Authorization: Bearer <token>
Content-Type: application/json

{
  "old_password": "contraseña_actual",
  "new_password": "contraseña_nueva"
}
```

### Características del Juego

#### Listar Jugadores
```http
GET /api/game/players
```

**Respuesta:**
```json
{
  "players": [
    {
      "account_id": 12345,
      "name": "MataDragones",
      "job": 1,
      "level": 85,
      "exp": 450000
    }
  ]
}
```

#### Listar Gremios
```http
GET /api/game/guilds
```

**Respuesta:**
```json
{
  "guilds": [
    {
      "id": 1,
      "name": "MataDragones",
      "master": 12345,
      "level": 50,
      "exp": 25000,
      "win": 15,
      "draw": 3,
      "loss": 2
    }
  ]
}
```

### Gestión de Descargas

#### Listar Descargas
```http
GET /api/game/downloads?page=1&per_page=10&category=cliente&published=true
```

**Parámetros de Consulta:**
- `page`: Número de página (predeterminado: 1)
- `per_page`: Elementos por página (predeterminado: 10, máximo: 100)
- `category`: Filtrar por categoría (opcional)
- `published`: Filtrar por estado de publicación (opcional)

**Respuesta:**
```json
{
  "response": [
    {
      "id": 1,
      "provider": "Google Drive",
      "size": 512.5,
      "link": "https://drive.google.com/file/d/123456789/view",
      "category": "cliente",
      "published": true
    }
  ],
  "total": 1,
  "page": 1,
  "per_page": 10,
  "total_pages": 1,
  "has_next": false,
  "has_prev": false
}
```

#### Obtener Descarga por ID
```http
GET /api/game/downloads/{download_id}
```

#### Crear Descarga (Solo Admin)
```http
POST /api/game/downloads
Authorization: Bearer <admin_token>
Content-Type: application/json

{
  "provider": "Google Drive",
  "size": 512.5,
  "link": "https://drive.google.com/file/d/123456789/view",
  "category": "cliente",
  "published": false
}
```

#### Actualizar Descarga (Solo Admin)
```http
PUT /api/game/downloads/{download_id}
Authorization: Bearer <admin_token>
Content-Type: application/json

{
  "provider": "Mega",
  "size": 600.0,
  "link": "https://mega.nz/file/abcdef123456789",
  "category": "parches",
  "published": true
}
```

#### Publicar Descarga (Solo Admin)
```http
PATCH /api/game/downloads/{download_id}/publish
Authorization: Bearer <admin_token>
```

#### Despublicar Descarga (Solo Admin)
```http
PATCH /api/game/downloads/{download_id}/unpublish
Authorization: Bearer <admin_token>
```

#### Eliminar Descarga (Solo Admin)
```http
DELETE /api/game/downloads/{download_id}
Authorization: Bearer <admin_token>
```

#### Obtener Jugadores del Usuario Actual
```http
GET /api/account/me/players
Authorization: Bearer <token>
```

**Respuesta:**
```json
{
  "players": [
    {
      "account_id": 12345,
      "name": "NombreJugador",
      "job": 1,
      "level": 85,
      "exp": 450000
    }
  ]
}
```

## 🔧 Configuración

### Variables de Entorno

| Variable | Descripción | Valor por Defecto |
|----------|-------------|-------------------|
| `DATABASE_URL_APP` | URL de conexión de la base de datos principal | `mysql+pymysql://usuario:contraseña@HOSTNAME:PUERTO/application` |
| `DATABASE_URL_ACCOUNT` | URL de conexión de la base de datos de cuentas legacy | `mysql+pymysql://usuario:contraseña@HOSTNAME:PUERTO/srv1_account` |
| `DATABASE_URL_PLAYER` | URL de conexión de la base de datos de jugadores legacy | `mysql+pymysql://usuario:contraseña@HOSTNAME:PUERTO/srv1_player` |
| `DATABASE_URL_COMMON` | URL de conexión de la base de datos administrativa | `mysql+pymysql://usuario:contraseña@HOSTNAME:PUERTO/common` |
| `SECRET_KEY` | Clave secreta JWT | `tu-clave-secreta` |
| `ALGORITHM` | Algoritmo de encriptación JWT | `HS256` |
| `ACCESS_TOKEN_EXPIRE_MINUTES` | Tiempo de expiración del token (minutos) | `30` |

### Configuración CORS

La aplicación está configurada para aceptar solicitudes desde:
- `http://localhost:3000`
- `http://localhost:8080`

## 📖 Documentación de la API

Una vez que la aplicación esté ejecutándose, puedes acceder a:

- **Swagger UI**: http://localhost:8000/docs
- **ReDoc**: http://localhost:8000/redoc

## 🔍 Endpoints de Salud

- **Verificación de Salud**: `GET /health` - Verificar estado de la aplicación
- **Raíz**: `GET /` - Mensaje de bienvenida

## 🏗️ Arquitectura

### Configuración de Cuádruple Base de Datos

La aplicación usa cuatro bases de datos MySQL separadas:

1. **application**: Base de datos principal de la aplicación para nuevas características y datos (descargas, etc.)
2. **srv1_account**: Base de datos legacy que almacena información de autenticación y cuentas de usuario
3. **srv1_player**: Base de datos legacy que maneja datos de jugadores y gremios
4. **common**: Base de datos administrativa para gestión de GM/admin y autorización

### Modelos Personalizados

Los modelos heredan de clases base personalizadas que corresponden a su base de datos objetivo:

- **BaseSaveModel**: Para tablas de la base de datos principal de la aplicación (descargas, etc.)
- **BaseSaveAccountModel**: Para tablas de la base de datos de cuentas legacy
- **BaseSavePlayerModel**: Para tablas de la base de datos de jugadores/gremios legacy
- **BaseSaveCommonModel**: Para tablas de la base de datos administrativa (gestión GM/admin)

Cada clase base incluye métodos convenientes:
- `.save()`: Guardar en la base de datos apropiada
- `.delete()`: Eliminar registro de la base de datos apropiada
- `.filter()`: Filtrar registros en la base de datos apropiada
- `.query()`: Realizar consultas en la base de datos apropiada

### Sistema de Dependencias

Usa el sistema de inyección de dependencias de FastAPI para:
- Gestión de sesiones multi-base de datos (bases de datos de aplicación, cuenta, jugador, común)
- Autenticación y autorización de usuarios
- Control de acceso basado en roles de admin
- Operaciones CRUD específicas de base de datos

## 🐳 Configuración Docker

El proyecto incluye soporte Docker completo con:

- **Configuración multi-servicio**: API y base de datos MySQL
- **Verificaciones de salud**: Verificación de disponibilidad de base de datos
- **Persistencia de volúmenes**: Los datos sobreviven a reinicios de contenedores
- **Configuración de entorno**: Opciones de despliegue flexibles

### Servicios Docker
- **API**: Aplicación FastAPI en puerto 8000
- **Base de datos**: MySQL 5.7 en puerto 3307

### Comandos Docker

```bash
# Construir y ejecutar
docker-compose up --build

# Ejecutar en segundo plano
docker-compose up -d

# Ver logs
docker-compose logs -f api

# Detener servicios
docker-compose down

# Eliminar volúmenes (precaución: elimina datos)
docker-compose down -v
```

## 🚀 Desarrollo

### Ejecutar en Modo Desarrollo
```bash
uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
```

### Características de Desarrollo

- **Creación Automática de Tablas**: Las tablas se crean al iniciar la aplicación
- **Registro de Consultas SQL**: Modo echo habilitado para depuración
- **Recarga en Caliente**: Reinicio automático en cambios de código (con --reload)

## 📝 Licencia

Este proyecto está licenciado bajo la Licencia MIT.

## 🤝 Contribuir

¡Las contribuciones son bienvenidas! Por favor:

1. Haz fork del proyecto
2. Crea una rama de característica (`git checkout -b feature/CaracteristicaIncreible`)
3. Confirma tus cambios (`git commit -m 'Agregar alguna CaracteristicaIncreible'`)
4. Empuja a la rama (`git push origin feature/CaracteristicaIncreible`)
5. Abre un Pull Request