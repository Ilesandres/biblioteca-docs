# Guía de Instalación

## Requisitos Previos

Antes de comenzar la instalación, asegúrese de tener instalado:

- Node.js (v14 o superior)
- Docker y Docker Compose
- Git
- MySQL (si no usa Docker)

## Pasos de Instalación

### 1. Clonar el Repositorio

```bash
git clone [URL_DEL_REPOSITORIO]
cd biblioteca-digital
```

### 2. Configuración del Frontend

```bash
# Navegar al directorio del frontend
cd frontend

# Instalar dependencias
npm install

# Crear archivo de variables de entorno
cp .env.example .env
```

Edite el archivo `.env` con sus configuraciones locales.

### 3. Configuración del Backend

```bash
# Navegar al directorio del backend
cd ../backend

# Instalar dependencias
npm install

# Crear archivo de variables de entorno
cp .env.example .env
```

Edite el archivo `.env` con sus configuraciones de base de datos y otras variables de entorno.

### 4. Configuración de la Base de Datos

#### Usando Docker (Recomendado)

```bash
# Iniciar servicios con Docker Compose
docker-compose up -d
```

#### Configuración Manual

Si no usa Docker, configure su base de datos MySQL local según las especificaciones en `.env`.

### 5. Iniciar el Proyecto

#### Desarrollo

```bash
# Iniciar backend
cd backend
npm run dev

# En otra terminal, iniciar frontend
cd frontend
npm run dev
```

#### Producción

```bash
# Construir frontend
cd frontend
npm run build

# Iniciar backend en modo producción
cd ../backend
npm start
```

## Verificación de la Instalación

1. Frontend: Acceda a `http://localhost:5173`
2. Backend: Verifique `http://localhost:3000/api/health`
3. Base de datos: Verifique la conexión a través del backend

## Configuración SSL para Desarrollo Local

### Generar Certificados

```bash
mkcert localhost
```

Mueva los certificados generados a la carpeta correspondiente y actualice la configuración en el archivo `.env`.

## Solución de Problemas Comunes

### Error de Conexión a la Base de Datos
- Verifique las credenciales en `.env`
- Confirme que el servicio MySQL esté activo
- Revise la conectividad al puerto de la base de datos

### Errores de Dependencias
- Limpie la caché de npm: `npm cache clean --force`
- Elimine node_modules y reinstale: `rm -rf node_modules && npm install`

### Problemas con Docker
- Verifique que Docker esté corriendo
- Revise los logs: `docker-compose logs`
- Reconstruya los contenedores: `docker-compose up --build`

## Siguientes Pasos

Una vez completada la instalación, consulte el [Manual de Usuario](uso.md) para comenzar a utilizar el sistema.