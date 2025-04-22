# Informe Técnico del Proyecto de Biblioteca Digital

## Introducción

En la era digital actual, las bibliotecas enfrentan el desafío de modernizar sus servicios para satisfacer las necesidades de una generación cada vez más conectada. Este proyecto surge como respuesta a la necesidad de transformar la gestión tradicional de bibliotecas en un sistema digital integral, aprovechando las tecnologías modernas para mejorar la accesibilidad y eficiencia de los servicios bibliotecarios.

## Justificación

La implementación de una biblioteca digital responde a múltiples necesidades:

1. **Accesibilidad**: Permitir el acceso a recursos bibliográficos desde cualquier ubicación y dispositivo.
2. **Eficiencia Operativa**: Automatizar procesos de gestión bibliotecaria para reducir tareas manuales.
3. **Preservación Digital**: Garantizar la conservación y disponibilidad a largo plazo de los recursos.
4. **Sostenibilidad**: Reducir costos operativos y huella ambiental asociada con la gestión física.

## Planteamiento del Problema

### Problemática

Las bibliotecas tradicionales enfrentan limitaciones significativas:

- Acceso restringido a horarios físicos
- Procesos manuales propensos a errores
- Dificultad en el seguimiento y control de préstamos
- Limitaciones en la búsqueda y catalogación de recursos

### Pregunta de Investigación

¿Cómo implementar un sistema de biblioteca digital que optimice la gestión de recursos bibliográficos y mejore la experiencia de usuarios, garantizando la seguridad y eficiencia en el acceso a la información?

## Requisitos del Sistema

### Requisitos Funcionales

1. **Gestión de Usuarios**
   - Registro y autenticación de usuarios
   - Gestión de roles y permisos
   - Perfil de usuario personalizable

2. **Gestión de Recursos**
   - Catalogación de materiales bibliográficos
   - Sistema de búsqueda avanzada
   - Control de préstamos y devoluciones

3. **Administración**
   - Panel de control administrativo
   - Generación de reportes
   - Gestión de inventario

### Requisitos No Funcionales

1. **Seguridad**
   - Implementación de HTTPS
   - Autenticación segura mediante JWT
   - Protección contra ataques comunes

2. **Rendimiento**
   - Tiempo de respuesta < 2 segundos
   - Soporte para múltiples usuarios concurrentes
   - Optimización de consultas a base de datos

3. **Disponibilidad**
   - Servicio disponible 24/7
   - Plan de respaldo y recuperación
   - Monitoreo continuo

## Estado Actual del Proyecto

### Resumen Ejecutivo

Este informe técnico detalla el estado actual y los avances del proyecto de Biblioteca Digital, un sistema integral que consta de un frontend moderno y un backend robusto. El proyecto implementa una arquitectura moderna basada en microservicios, con énfasis en la seguridad, escalabilidad y mantenibilidad.

## 1. Arquitectura del Sistema

### 1.1 Frontend (Cliente)

- **Tecnología Principal**: React.js
- **Características Implementadas**:
  - Interfaz de usuario moderna y responsiva
  - Implementación de HTTPS para desarrollo local
  - Sistema de autenticación seguro
  - Gestión de estado de la aplicación
  - Componentes reutilizables

### 1.2 Backend (Servidor)

- **Tecnología Principal**: Node.js
- **Base de Datos**: MySQL
- **Contenedorización**: Docker

#### Configuración de Docker

Utilizamos Docker para containerizar nuestra aplicación, lo que nos permite tener un entorno de desarrollo consistente y facilitar el despliegue:

```dockerfile
# Dockerfile para el backend
# Este archivo define la configuración para construir el contenedor de la aplicación
# Usamos la imagen alpine por su tamaño reducido y seguridad
FROM node:16-alpine

# Establecemos el directorio de trabajo
WORKDIR /app

# Copiamos los archivos de dependencias
COPY package*.json ./
RUN npm install

# Copiamos el resto del código fuente
COPY . .

# Exponemos el puerto que usará la aplicación
EXPOSE 3000
CMD ["npm", "start"]
```

#### Orquestación con Docker Compose

Utilizamos Docker Compose para gestionar múltiples contenedores y sus dependencias:

```yaml
# docker-compose.yml
# Este archivo define la configuración para orquestar múltiples servicios
version: '3.8'
services:
  # Servicio del backend de la aplicación
  backend:
    build: .
    ports:
      - "3000:3000"  # Mapeo de puertos host:contenedor
    environment:
      - NODE_ENV=production
      - DB_HOST=db
    depends_on:
      - db  # Asegura que la base de datos esté disponible antes de iniciar el backend
    
  # Servicio de base de datos MySQL
  db:
    image: mysql:8
    environment:
      - MYSQL_ROOT_PASSWORD=secreto  # Contraseña del root (usar variables de entorno en producción)
      - MYSQL_DATABASE=biblioteca    # Nombre de la base de datos
    volumes:
      - db_data:/var/lib/mysql      # Persistencia de datos

# Definición de volúmenes para persistencia
volumes:
  db_data:
```

## 2. Capa de Persistencia Avanzada

### 2.1 Optimizaciones Implementadas

- Consultas SQL optimizadas para rendimiento
- Implementación de caché para consultas frecuentes
- Índices estratégicos en tablas críticas
- Paginación eficiente de resultados

### 2.2 Gestión de Datos

- Sistema de respaldo automatizado
- Validación de integridad de datos
- Manejo de transacciones ACID
- Normalización de esquemas de base de datos

## 3. Controladores Avanzados

### 3.1 Características Implementadas

- Manejo avanzado de ficheros
- Procesamiento de objetos Blob para documentos
- Validación robusta de datos de entrada
- Sistema de logging detallado

### 3.2 Patrones de Diseño Utilizados

#### Patrón MVC (Modelo-Vista-Controlador)

Implementamos el patrón MVC para separar la lógica de negocio, la presentación y el control de datos:

```javascript
// Controlador de Libros (Controller)
// Maneja las peticiones HTTP y coordina la lógica de negocio
class LibroController {
  constructor(libroService) {
    // Inyección de dependencias para mejor testabilidad
    this.libroService = libroService;
  }

  // Método para obtener todos los libros
  async obtenerLibros(req, res) {
    try {
      // Delega la lógica de negocio al servicio
      const libros = await this.libroService.obtenerTodos();
      // Responde con los datos en formato JSON
      res.json(libros);
    } catch (error) {
      // Manejo centralizado de errores
      res.status(500).json({ 
        error: error.message,
        timestamp: new Date().toISOString()
      });
    }
  }

  // Método para buscar libros por criterios
  async buscarLibros(req, res) {
    try {
      const { titulo, autor, categoria } = req.query;
      const libros = await this.libroService.buscar({ titulo, autor, categoria });
      res.json(libros);
    } catch (error) {
      res.status(500).json({ error: error.message });
    }
  }
}
```

#### Repository Pattern para Acceso a Datos

Utilizamos el Repository Pattern para abstraer y encapsular la lógica de acceso a datos:

```javascript
// Repositorio de Libros
// Encapsula la lógica de acceso a datos y proporciona una API clara
class LibroRepository {
  constructor(database) {
    this.db = database;
  }

  // Buscar libro por ID
  async findById(id) {
    // Utilizamos parámetros preparados para prevenir SQL injection
    return await this.db.query(
      'SELECT * FROM libros WHERE id = ?',
      [id]
    );
  }

  // Guardar nuevo libro
  async save(libro) {
    // Validación básica de datos
    if (!libro.titulo || !libro.autor) {
      throw new Error('Título y autor son requeridos');
    }

    // Inserción con parámetros preparados
    return await this.db.query(
      'INSERT INTO libros (titulo, autor, isbn, categoria) VALUES (?, ?, ?, ?)',
      [libro.titulo, libro.autor, libro.isbn, libro.categoria]
    );
  }

  // Buscar libros por criterios
  async buscarPor(criterios) {
    let query = 'SELECT * FROM libros WHERE 1=1';
    const params = [];

    // Construcción dinámica de consulta
    if (criterios.titulo) {
      query += ' AND titulo LIKE ?';
      params.push(`%${criterios.titulo}%`);
    }
    if (criterios.autor) {
      query += ' AND autor LIKE ?';
      params.push(`%${criterios.autor}%`);
    }

    return await this.db.query(query, params);
  }
}
```

## 4. Gestión de Usuarios

### 4.1 Sistema de Roles

- Administradores
- Bibliotecarios
- Usuarios estándar
- Invitados

### 4.2 Características de Seguridad

#### Sistema de Autenticación JWT

Implementamos JSON Web Tokens (JWT) para gestionar la autenticación de manera segura y escalable:

```javascript
// Configuración y utilidades de JWT
// Este módulo maneja la generación y verificación de tokens de acceso
const jwt = require('jsonwebtoken');

// La clave secreta debe estar en variables de entorno
const SECRET_KEY = process.env.JWT_SECRET;

// Configuración de opciones del token
const TOKEN_CONFIG = {
  expiresIn: '24h',     // Tiempo de expiración
  algorithm: 'HS256'    // Algoritmo de firma
};

/**
 * Genera un token JWT para un usuario autenticado
 * @param {Object} user - Datos del usuario
 * @returns {string} Token JWT firmado
 */
const generateToken = (user) => {
  // Payload del token con información esencial
  const payload = { 
    id: user.id,
    role: user.role,
    email: user.email,
    iat: Date.now()    // Timestamp de emisión
  };

  // Generamos y firmamos el token
  return jwt.sign(
    payload,
    SECRET_KEY,
    TOKEN_CONFIG
  );
};

/**
 * Verifica y decodifica un token JWT
 * @param {string} token - Token JWT a verificar
 * @returns {Object} Payload decodificado
 * @throws {Error} Si el token es inválido o está expirado
 */
const verifyToken = (token) => {
  try {
    // Verificamos y decodificamos el token
    const decoded = jwt.verify(token, SECRET_KEY);
    
    // Verificación adicional de expiración
    if (Date.now() >= decoded.exp * 1000) {
      throw new Error('Token expirado');
    }
    
    return decoded;
  } catch (error) {
    // Manejamos diferentes tipos de errores
    if (error.name === 'TokenExpiredError') {
      throw new Error('El token ha expirado');
    } else if (error.name === 'JsonWebTokenError') {
      throw new Error('Token inválido');
    }
    throw error;
  }
};

/**
 * Middleware para proteger rutas
 * @param {Object} req - Objeto de solicitud
 * @param {Object} res - Objeto de respuesta
 * @param {Function} next - Función next
 */
const authMiddleware = (req, res, next) => {
  try {
    // Extraemos el token del header
    const token = req.headers.authorization?.split(' ')[1];
    if (!token) {
      return res.status(401).json({ error: 'Token no proporcionado' });
    }

    // Verificamos el token
    const decoded = verifyToken(token);
    req.user = decoded;  // Agregamos el usuario al objeto request
    next();
  } catch (error) {
    res.status(401).json({ error: error.message });
  }
};
```

## 5. Implementación de Seguridad

### 5.1 Medidas de Seguridad Implementadas

- Encriptación de datos sensibles
- Protección contra ataques XSS y CSRF
- Rate limiting para prevenir ataques DoS
- Validación de entrada en todos los endpoints

### 5.2 Auditoría y Monitoreo

- Registro de actividades de usuarios
- Monitoreo de intentos de acceso
- Alertas de seguridad automatizadas
- Sistema de respuesta a incidentes

## 6. Pruebas de Software

### 6.1 Tipos de Pruebas Implementadas

#### Pruebas Unitarias con Jest

Implementamos pruebas unitarias exhaustivas para verificar el comportamiento de componentes individuales:

```javascript
// Pruebas unitarias para el sistema de autenticación JWT
// Verificamos la generación y validación de tokens
const { generateToken, verifyToken } = require('../auth/jwt');

describe('Sistema de Autenticación JWT', () => {
  // Datos de prueba
  const mockUser = {
    id: 1,
    email: 'usuario@ejemplo.com',
    role: 'usuario',
    nombre: 'Usuario Test'
  };

  // Configuración inicial antes de cada prueba
  beforeEach(() => {
    jest.clearAllMocks();  // Limpiamos los mocks
    process.env.JWT_SECRET = 'test_secret_key';  // Configuramos clave de prueba
  });

  describe('Generación de Token', () => {
    test('debe generar un token válido con la información correcta', () => {
      // Generamos el token
      const token = generateToken(mockUser);

      // Verificaciones básicas del token
      expect(token).toBeDefined();
      expect(typeof token).toBe('string');
      expect(token.split('.')).toHaveLength(3);  // Formato JWT válido
    });

    test('debe incluir toda la información del usuario en el payload', () => {
      const token = generateToken(mockUser);
      const decoded = verifyToken(token);

      // Verificamos que el payload contiene la información correcta
      expect(decoded.id).toBe(mockUser.id);
      expect(decoded.email).toBe(mockUser.email);
      expect(decoded.role).toBe(mockUser.role);
      expect(decoded.iat).toBeDefined();  // Timestamp de emisión
    });
  });

  describe('Verificación de Token', () => {
    test('debe verificar correctamente un token válido', () => {
      const token = generateToken(mockUser);
      const decoded = verifyToken(token);

      // Verificamos la decodificación correcta
      expect(decoded).toMatchObject({
        id: mockUser.id,
        email: mockUser.email,
        role: mockUser.role
      });
    });

    test('debe rechazar un token inválido', () => {
      // Verificamos que se lance el error apropiado
      expect(() => {
        verifyToken('token_invalido');
      }).toThrow('Token inválido');
    });

    test('debe rechazar un token expirado', () => {
      // Simulamos un token expirado
      jest.spyOn(Date, 'now').mockImplementation(() => Date.now() + 86400000);

      const token = generateToken(mockUser);
      expect(() => {
        verifyToken(token);
      }).toThrow('Token expirado');
    });
  });
});
```

#### Pruebas de Integración

Realizamos pruebas de integración para verificar la interacción entre diferentes componentes del sistema:

```javascript
// Pruebas de integración para el API de Libros
// Verificamos el funcionamiento completo de los endpoints
const request = require('supertest');
const app = require('../app');
const { conectarDB, limpiarDB, cerrarDB } = require('../config/database');

describe('API de Gestión de Libros', () => {
  // Configuración inicial para las pruebas
  beforeAll(async () => {
    await conectarDB();  // Conectamos a la base de datos de prueba
  });

  // Limpiamos la base de datos antes de cada prueba
  beforeEach(async () => {
    await limpiarDB();
  });

  // Cerramos la conexión después de todas las pruebas
  afterAll(async () => {
    await cerrarDB();
  });

  describe('GET /api/libros', () => {
    test('debe devolver lista de libros paginada', async () => {
      // Preparamos datos de prueba
      await request(app)
        .post('/api/libros')
        .send([
          { titulo: 'Libro 1', autor: 'Autor 1' },
          { titulo: 'Libro 2', autor: 'Autor 2' }
        ]);

      // Realizamos la petición de prueba
      const response = await request(app)
        .get('/api/libros?page=1&limit=10')
        .expect('Content-Type', /json/)
        .expect(200);

      // Verificamos la estructura y contenido de la respuesta
      expect(response.body).toMatchObject({
        data: expect.any(Array),
        pagination: {
          total: 2,
          page: 1,
          limit: 10
        }
      });
      expect(response.body.data).toHaveLength(2);
    });

    test('debe filtrar libros por criterios de búsqueda', async () => {
      // Preparamos datos de prueba
      await request(app)
        .post('/api/libros')
        .send([
          { titulo: 'JavaScript Avanzado', autor: 'Autor 1' },
          { titulo: 'Python Básico', autor: 'Autor 2' }
        ]);

      // Realizamos búsqueda por título
      const response = await request(app)
        .get('/api/libros?search=JavaScript')
        .expect(200);

      expect(response.body.data).toHaveLength(1);
      expect(response.body.data[0].titulo).toContain('JavaScript');
    });
  });

  describe('POST /api/libros', () => {
    test('debe crear un nuevo libro con datos válidos', async () => {
      const nuevoLibro = {
        titulo: 'Nuevo Libro',
        autor: 'Nuevo Autor',
        isbn: '1234567890',
        categoria: 'Programación'
      };

      const response = await request(app)
        .post('/api/libros')
        .send(nuevoLibro)
        .expect(201);

      expect(response.body).toMatchObject({
        id: expect.any(Number),
        ...nuevoLibro
      });
    });

    test('debe validar datos requeridos', async () => {
      const libroIncompleto = {
        titulo: 'Libro Sin Autor'
      };

      await request(app)
        .post('/api/libros')
        .send(libroIncompleto)
        .expect(400);
    });
  });
});
```

### 6.2 Herramientas y Métricas

- Jest para pruebas unitarias
- Supertest para pruebas de API
- JMeter para pruebas de carga
- SonarQube para análisis de código

## Conclusión

El proyecto de Biblioteca Digital muestra un progreso significativo en la implementación de características fundamentales, seguridad y arquitectura. Las pruebas realizadas demuestran la robustez del sistema y su capacidad para manejar las necesidades de una biblioteca moderna. Los próximos pasos se centrarán en la optimización continua y la mejora de la experiencia del usuario.