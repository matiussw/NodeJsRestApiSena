# 📝 Ejercicios Prácticos Progresivos

## 🎯 Objetivo
Esta guía contiene ejercicios progresivos para dominar Node.js y el desarrollo de APIs REST, desde conceptos básicos hasta integración con bases de datos.

---

## 📚 Nivel 1: Fundamentos de Express

### Ejercicio 1.1: Rutas Básicas
**Dificultad:** ⭐

Modifica `server.js` para agregar:

1. Una ruta `GET /` que devuelva un mensaje de bienvenida
2. Una ruta `GET /api/info` que devuelva información sobre la API (nombre, versión, autor)

**Resultado esperado:**
```json
// GET /api/info
{
  "nombre": "API de Tareas",
  "version": "1.0.0",
  "autor": "Tu Nombre"
}
```

### Ejercicio 1.2: Parámetros de Consulta (Query Params)
**Dificultad:** ⭐⭐

Modifica la ruta `GET /api/tareas` para que acepte parámetros de consulta:
- `?completada=true` - Filtra solo tareas completadas
- `?completada=false` - Filtra solo tareas pendientes

**Ejemplo:**
```
GET /api/tareas?completada=true
```

**Pista:**
```javascript
const { completada } = req.query;
// Usa Array.filter() para filtrar las tareas
```

### Ejercicio 1.3: Validación de Datos
**Dificultad:** ⭐⭐

En la ruta `POST /api/tareas`, agrega validaciones:
- El campo `titulo` es obligatorio
- El `titulo` debe tener al menos 3 caracteres
- El `titulo` no debe exceder 100 caracteres

Si la validación falla, devuelve un error 400 con un mensaje descriptivo.

**Resultado esperado en error:**
```json
{
  "error": "El título debe tener entre 3 y 100 caracteres"
}
```

---

## 📚 Nivel 2: Mejoras en la API

### Ejercicio 2.1: Búsqueda de Tareas
**Dificultad:** ⭐⭐

Crea una nueva ruta `GET /api/tareas/buscar?q=texto` que busque tareas cuyo título contenga el texto especificado.

**Ejemplo:**
```
GET /api/tareas/buscar?q=Node
```

**Pista:**
```javascript
const busqueda = req.query.q.toLowerCase();
const resultados = tareas.filter(t => 
  t.titulo.toLowerCase().includes(busqueda)
);
```

### Ejercicio 2.2: Manejo de Errores Centralizado
**Dificultad:** ⭐⭐⭐

Crea un middleware de manejo de errores:

```javascript
// Middleware de manejo de errores (al final del archivo)
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(err.status || 500).json({
    error: {
      mensaje: err.message,
      estado: err.status || 500
    }
  });
});
```

Modifica tus rutas para usar `next(error)` cuando haya errores.

### Ejercicio 2.3: Paginación
**Dificultad:** ⭐⭐⭐

Implementa paginación en `GET /api/tareas`:
- Parámetro `page` (número de página, default: 1)
- Parámetro `limit` (elementos por página, default: 5)

**Ejemplo:**
```
GET /api/tareas?page=2&limit=3
```

**Resultado esperado:**
```json
{
  "data": [...],
  "pagination": {
    "pagina": 2,
    "limite": 3,
    "total": 10,
    "totalPaginas": 4
  }
}
```

### Ejercicio 2.4: Timestamps
**Dificultad:** ⭐⭐

Agrega campos de fecha a las tareas:
- `fechaCreacion`: fecha y hora de creación
- `fechaActualizacion`: fecha y hora de última actualización

Actualiza todas las rutas correspondientes.

---

## 📚 Nivel 3: Modularización

### Ejercicio 3.1: Separar Rutas
**Dificultad:** ⭐⭐⭐

Crea una estructura modular:

```
mi-primera-api/
├── server.js
├── routes/
│   └── tareas.js
├── controllers/
│   └── tareasController.js
└── models/
    └── tarea.js
```

**Archivo routes/tareas.js:**
```javascript
const express = require('express');
const router = express.Router();
const tareasController = require('../controllers/tareasController');

router.get('/', tareasController.obtenerTodas);
router.get('/:id', tareasController.obtenerPorId);
router.post('/', tareasController.crear);
router.put('/:id', tareasController.actualizar);
router.delete('/:id', tareasController.eliminar);

module.exports = router;
```

### Ejercicio 3.2: Variables de Entorno
**Dificultad:** ⭐⭐

1. Instala dotenv: `npm install dotenv`
2. Crea un archivo `.env`:

```env
PORT=3000
NODE_ENV=development
API_VERSION=1.0.0
```

3. Carga las variables en `server.js`:

```javascript
require('dotenv').config();

const PUERTO = process.env.PORT || 3000;
```

### Ejercicio 3.3: Configuración por Entorno
**Dificultad:** ⭐⭐⭐

Crea archivos de configuración:

**config/config.js:**
```javascript
module.exports = {
  development: {
    port: 3000,
    db: './database/tareas-dev.db'
  },
  production: {
    port: process.env.PORT,
    db: './database/tareas.db'
  }
};
```

---

## 📚 Nivel 4: Persistencia con SQLite3

### Ejercicio 4.1: Configurar SQLite3
**Dificultad:** ⭐⭐

1. Instala SQLite3:
```bash
npm install sqlite3
```

2. Crea la carpeta `database/`

3. Crea `database/setup.js`:

```javascript
const sqlite3 = require('sqlite3').verbose();
const path = require('path');

const dbPath = path.join(__dirname, 'tareas.db');
const db = new sqlite3.Database(dbPath);

db.serialize(() => {
  db.run(`
    CREATE TABLE IF NOT EXISTS tareas (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      titulo TEXT NOT NULL,
      completada BOOLEAN DEFAULT 0,
      fecha_creacion DATETIME DEFAULT CURRENT_TIMESTAMP,
      fecha_actualizacion DATETIME DEFAULT CURRENT_TIMESTAMP
    )
  `);

  console.log('Base de datos inicializada');
});

db.close();
```

4. Ejecuta: `node database/setup.js`

### Ejercicio 4.2: Migrar a SQLite - Lectura
**Dificultad:** ⭐⭐⭐

Modifica la ruta `GET /api/tareas` para leer desde SQLite:

```javascript
const sqlite3 = require('sqlite3').verbose();
const db = new sqlite3.Database('./database/tareas.db');

app.get('/api/tareas', (req, res) => {
  db.all('SELECT * FROM tareas', [], (err, rows) => {
    if (err) {
      return res.status(500).json({ error: err.message });
    }
    res.json(rows);
  });
});
```

### Ejercicio 4.3: Migrar a SQLite - Escritura
**Dificultad:** ⭐⭐⭐

Migra las operaciones de escritura (POST, PUT, DELETE) a SQLite.

**POST:**
```javascript
app.post('/api/tareas', (req, res) => {
  const { titulo } = req.body;
  
  if (!titulo || titulo.length < 3) {
    return res.status(400).json({ 
      error: 'El título debe tener al menos 3 caracteres' 
    });
  }

  db.run(
    'INSERT INTO tareas (titulo) VALUES (?)',
    [titulo],
    function(err) {
      if (err) {
        return res.status(500).json({ error: err.message });
      }
      
      // this.lastID contiene el ID del registro insertado
      db.get(
        'SELECT * FROM tareas WHERE id = ?',
        [this.lastID],
        (err, row) => {
          if (err) {
            return res.status(500).json({ error: err.message });
          }
          res.status(201).json(row);
        }
      );
    }
  );
});
```

### Ejercicio 4.4: Uso de Promesas con SQLite
**Dificultad:** ⭐⭐⭐⭐

Convierte las operaciones con callbacks a Promesas:

1. Instala: `npm install sqlite sqlite3`

2. Crea `database/db.js`:

```javascript
const sqlite3 = require('sqlite3');
const { open } = require('sqlite');
const path = require('path');

async function conectarDB() {
  return open({
    filename: path.join(__dirname, 'tareas.db'),
    driver: sqlite3.Database
  });
}

module.exports = { conectarDB };
```

3. Usa async/await en tus rutas:

```javascript
const { conectarDB } = require('./database/db');

app.get('/api/tareas', async (req, res) => {
  try {
    const db = await conectarDB();
    const tareas = await db.all('SELECT * FROM tareas');
    res.json(tareas);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});
```

### Ejercicio 4.5: Transacciones
**Dificultad:** ⭐⭐⭐⭐

Implementa una ruta que marque múltiples tareas como completadas usando una transacción:

**POST /api/tareas/completar-multiples**
```javascript
app.post('/api/tareas/completar-multiples', async (req, res) => {
  const { ids } = req.body; // Array de IDs
  
  try {
    const db = await conectarDB();
    
    await db.run('BEGIN TRANSACTION');
    
    for (const id of ids) {
      await db.run(
        'UPDATE tareas SET completada = 1 WHERE id = ?',
        [id]
      );
    }
    
    await db.run('COMMIT');
    
    res.json({ mensaje: 'Tareas actualizadas exitosamente' });
  } catch (error) {
    await db.run('ROLLBACK');
    res.status(500).json({ error: error.message });
  }
});
```

---

## 📚 Nivel 5: Proyecto Completo - API de Notas

### Ejercicio 5.1: API de Notas Completa
**Dificultad:** ⭐⭐⭐⭐⭐

Crea una API completamente nueva para gestionar notas con las siguientes características:

**Estructura de Nota:**
```javascript
{
  id: number,
  titulo: string,
  contenido: string,
  categoria: string, // 'personal', 'trabajo', 'estudio'
  etiquetas: array,  // ['importante', 'urgente']
  fechaCreacion: datetime,
  fechaActualizacion: datetime,
  color: string      // '#ffffff'
}
```

**Endpoints requeridos:**

1. `GET /api/notas` - Listar todas (con paginación y filtros)
2. `GET /api/notas/:id` - Obtener una nota
3. `POST /api/notas` - Crear nota
4. `PUT /api/notas/:id` - Actualizar nota
5. `DELETE /api/notas/:id` - Eliminar nota
6. `GET /api/notas/categoria/:categoria` - Filtrar por categoría
7. `GET /api/notas/buscar?q=texto` - Búsqueda en título y contenido
8. `GET /api/notas/etiqueta/:etiqueta` - Filtrar por etiqueta
9. `GET /api/estadisticas` - Estadísticas (total, por categoría, etc.)

**Requisitos técnicos:**
- Usar SQLite3
- Estructura modular (routes, controllers, models)
- Validación de datos
- Manejo de errores
- Variables de entorno
- Documentación en código

**Base de datos:**
```sql
CREATE TABLE notas (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  titulo TEXT NOT NULL,
  contenido TEXT,
  categoria TEXT DEFAULT 'personal',
  etiquetas TEXT,  -- JSON array como string
  fecha_creacion DATETIME DEFAULT CURRENT_TIMESTAMP,
  fecha_actualizacion DATETIME DEFAULT CURRENT_TIMESTAMP,
  color TEXT DEFAULT '#ffffff'
);
```

---

## 🎯 Ejercicios Extra Avanzados

### Extra 1: Autenticación Básica
Implementa autenticación con JWT (JSON Web Tokens)

### Extra 2: Rate Limiting
Implementa límite de peticiones por usuario

### Extra 3: Documentación con Swagger
Documenta tu API usando Swagger/OpenAPI

### Extra 4: Tests
Escribe tests con Jest o Mocha

### Extra 5: Deploy
Despliega tu API en Render, Railway o Heroku

---

## 📋 Checklist de Progreso

### Nivel 1: Fundamentos
- [ ] Ejercicio 1.1: Rutas básicas
- [ ] Ejercicio 1.2: Query params
- [ ] Ejercicio 1.3: Validación

### Nivel 2: Mejoras
- [ ] Ejercicio 2.1: Búsqueda
- [ ] Ejercicio 2.2: Manejo de errores
- [ ] Ejercicio 2.3: Paginación
- [ ] Ejercicio 2.4: Timestamps

### Nivel 3: Modularización
- [ ] Ejercicio 3.1: Separar rutas
- [ ] Ejercicio 3.2: Variables de entorno
- [ ] Ejercicio 3.3: Configuración

### Nivel 4: Base de Datos
- [ ] Ejercicio 4.1: Configurar SQLite
- [ ] Ejercicio 4.2: Migrar lectura
- [ ] Ejercicio 4.3: Migrar escritura
- [ ] Ejercicio 4.4: Promesas
- [ ] Ejercicio 4.5: Transacciones

### Nivel 5: Proyecto Completo
- [ ] Ejercicio 5.1: API de Notas

---

## 💡 Consejos para Resolver los Ejercicios

1. **Lee bien los requisitos** antes de empezar a codificar
2. **Prueba cada endpoint** después de implementarlo
3. **Usa console.log()** para debugging
4. **Consulta la documentación** oficial cuando tengas dudas
5. **No copies y pegues** sin entender - escribe el código tú mismo
6. **Comenta tu código** para futuras referencias
7. **Haz commits frecuentes** si usas Git

## 🆘 ¿Atascado?

Si tienes problemas:
1. Revisa los ejemplos en `server.js`
2. Consulta la guía completa en el Google Doc
3. Busca en la documentación oficial de Express
4. Pregunta a tu instructor o compañeros

---

**¡Buena suerte con los ejercicios! 🚀**