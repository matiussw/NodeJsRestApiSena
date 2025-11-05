# 📘 Guía Avanzada: Node.js con Bases de Datos

## Continuación de la Guía Completa de Node.js y APIs REST

---

## 📚 Tabla de Contenidos

1. Introducción a las Bases de Datos
2. SQLite3 - Base de Datos Ligera
3. Configuración e Instalación
4. Operaciones CRUD con SQLite3
5. Promesas y Async/Await
6. Modularización Avanzada
7. Mejores Prácticas
8. Proyecto Final Integrado

---

## 1. Introducción a las Bases de Datos

### ¿Por qué usar una Base de Datos?

Hasta ahora hemos usado un **array en memoria** para almacenar datos. Esto tiene limitaciones:

❌ **Problemas del array en memoria:**
- Los datos se pierden al reiniciar el servidor
- No es escalable para muchos datos
- No permite concurrencia segura
- No hay respaldo automático

✅ **Ventajas de usar una base de datos:**
- **Persistencia**: Los datos se guardan permanentemente
- **Escalabilidad**: Maneja millones de registros
- **Seguridad**: Control de acceso y transacciones
- **Búsquedas eficientes**: Índices y optimizaciones
- **Integridad**: Validaciones y restricciones

### Tipos de Bases de Datos

**SQL (Relacionales):**
- SQLite (lo usaremos en este curso)
- PostgreSQL
- MySQL / MariaDB
- Microsoft SQL Server

**NoSQL:**
- MongoDB
- Redis
- Firebase

### ¿Por qué SQLite3 para aprender?

✅ **Ventajas de SQLite:**
- **Sin configuración**: No requiere servidor
- **Archivo único**: Toda la DB en un solo archivo `.db`
- **Ligero**: Ideal para prototipos y desarrollo
- **SQL estándar**: Lo que aprendas aplica a otras DBs
- **Portátil**: Mueve el archivo `.db` y listo

---

## 2. SQLite3 - Base de Datos Ligera

### ¿Qué es SQLite?

SQLite es una base de datos relacional embebida. Es como tener una base de datos completa en un solo archivo.

### Conceptos Clave

**Tabla**: Estructura que almacena datos (como una hoja de Excel)

**Columna**: Campo de datos (como las columnas en Excel)

**Fila**: Registro individual (como las filas en Excel)

**Clave Primaria (Primary Key)**: Identificador único de cada fila

**Tipo de Datos en SQLite:**
- `INTEGER`: Números enteros
- `TEXT`: Cadenas de texto
- `REAL`: Números decimales
- `BLOB`: Datos binarios (imágenes, archivos)
- `DATETIME`: Fechas y horas

---

## 3. Configuración e Instalación

### Paso 1: Instalar SQLite3

Abre tu terminal en el proyecto y ejecuta:

```bash
npm install sqlite3
```

Para usar con Promesas (recomendado):

```bash
npm install sqlite sqlite3
```

### Paso 2: Crear la Estructura de Carpetas

```bash
mkdir database
```

Tu proyecto debe verse así:

```
mi-primera-api/
├── server.js
├── package.json
└── database/
    ├── setup.js       # Script de inicialización
    └── tareas.db      # Archivo de base de datos (se crea automáticamente)
```

### Paso 3: Script de Inicialización

Crea `database/setup.js`:

```javascript
const sqlite3 = require('sqlite3').verbose();
const path = require('path');

// Ruta de la base de datos
const dbPath = path.join(__dirname, 'tareas.db');

// Crear conexión
const db = new sqlite3.Database(dbPath, (err) => {
  if (err) {
    console.error('Error al conectar a la base de datos:', err.message);
    return;
  }
  console.log('✅ Conectado a la base de datos SQLite');
});

// Crear tabla si no existe
db.serialize(() => {
  db.run(`
    CREATE TABLE IF NOT EXISTS tareas (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      titulo TEXT NOT NULL,
      descripcion TEXT,
      completada BOOLEAN DEFAULT 0,
      prioridad TEXT DEFAULT 'media',
      fecha_creacion DATETIME DEFAULT CURRENT_TIMESTAMP,
      fecha_actualizacion DATETIME DEFAULT CURRENT_TIMESTAMP
    )
  `, (err) => {
    if (err) {
      console.error('Error al crear la tabla:', err.message);
    } else {
      console.log('✅ Tabla "tareas" creada o ya existe');
    }
  });

  // Insertar datos de ejemplo (solo si la tabla está vacía)
  db.get('SELECT COUNT(*) as count FROM tareas', (err, row) => {
    if (row.count === 0) {
      const tareasIniciales = [
        ['Aprender Node.js', 'Estudiar los fundamentos', 0, 'alta'],
        ['Crear una API REST', 'Implementar CRUD completo', 0, 'alta'],
        ['Aprender SQLite', 'Dominar operaciones con bases de datos', 0, 'media']
      ];

      const stmt = db.prepare(`
        INSERT INTO tareas (titulo, descripcion, completada, prioridad)
        VALUES (?, ?, ?, ?)
      `);

      tareasIniciales.forEach(tarea => {
        stmt.run(tarea);
      });

      stmt.finalize();
      console.log('✅ Datos de ejemplo insertados');
    }
  });
});

// Cerrar conexión
db.close((err) => {
  if (err) {
    console.error('Error al cerrar la base de datos:', err.message);
  } else {
    console.log('✅ Conexión cerrada');
  }
});
```

### Paso 4: Ejecutar la Inicialización

```bash
node database/setup.js
```

Deberías ver:
```
✅ Conectado a la base de datos SQLite
✅ Tabla "tareas" creada o ya existe
✅ Datos de ejemplo insertados
✅ Conexión cerrada
```

---

## 4. Operaciones CRUD con SQLite3

### Método 1: Callbacks (Básico)

Crea `server-db-basic.js`:

```javascript
const express = require('express');
const sqlite3 = require('sqlite3').verbose();
const path = require('path');

const app = express();
app.use(express.json());

// Conectar a la base de datos
const dbPath = path.join(__dirname, 'database', 'tareas.db');
const db = new sqlite3.Database(dbPath);

// ============================================
// LEER (READ) - Obtener todas las tareas
// ============================================
app.get('/api/tareas', (req, res) => {
  const sql = 'SELECT * FROM tareas ORDER BY fecha_creacion DESC';
  
  db.all(sql, [], (err, rows) => {
    if (err) {
      return res.status(500).json({ 
        error: 'Error al obtener tareas',
        detalle: err.message 
      });
    }
    res.json(rows);
  });
});

// ============================================
// LEER (READ) - Obtener una tarea por ID
// ============================================
app.get('/api/tareas/:id', (req, res) => {
  const { id } = req.params;
  const sql = 'SELECT * FROM tareas WHERE id = ?';
  
  db.get(sql, [id], (err, row) => {
    if (err) {
      return res.status(500).json({ 
        error: 'Error al obtener la tarea',
        detalle: err.message 
      });
    }
    
    if (!row) {
      return res.status(404).json({ 
        error: 'Tarea no encontrada' 
      });
    }
    
    res.json(row);
  });
});

// ============================================
// CREAR (CREATE) - Nueva tarea
// ============================================
app.post('/api/tareas', (req, res) => {
  const { titulo, descripcion, prioridad } = req.body;
  
  // Validación
  if (!titulo || titulo.trim().length < 3) {
    return res.status(400).json({ 
      error: 'El título debe tener al menos 3 caracteres' 
    });
  }
  
  const sql = `
    INSERT INTO tareas (titulo, descripcion, prioridad)
    VALUES (?, ?, ?)
  `;
  
  db.run(sql, [titulo, descripcion || null, prioridad || 'media'], function(err) {
    if (err) {
      return res.status(500).json({ 
        error: 'Error al crear la tarea',
        detalle: err.message 
      });
    }
    
    // Obtener la tarea recién creada
    db.get('SELECT * FROM tareas WHERE id = ?', [this.lastID], (err, row) => {
      if (err) {
        return res.status(500).json({ error: err.message });
      }
      res.status(201).json(row);
    });
  });
});

// ============================================
// ACTUALIZAR (UPDATE) - Modificar tarea
// ============================================
app.put('/api/tareas/:id', (req, res) => {
  const { id } = req.params;
  const { titulo, descripcion, completada, prioridad } = req.body;
  
  // Construir la query dinámicamente
  const campos = [];
  const valores = [];
  
  if (titulo !== undefined) {
    campos.push('titulo = ?');
    valores.push(titulo);
  }
  if (descripcion !== undefined) {
    campos.push('descripcion = ?');
    valores.push(descripcion);
  }
  if (completada !== undefined) {
    campos.push('completada = ?');
    valores.push(completada ? 1 : 0);
  }
  if (prioridad !== undefined) {
    campos.push('prioridad = ?');
    valores.push(prioridad);
  }
  
  if (campos.length === 0) {
    return res.status(400).json({ 
      error: 'No se proporcionaron campos para actualizar' 
    });
  }
  
  // Agregar fecha de actualización
  campos.push('fecha_actualizacion = CURRENT_TIMESTAMP');
  
  // Agregar el ID al final
  valores.push(id);
  
  const sql = `UPDATE tareas SET ${campos.join(', ')} WHERE id = ?`;
  
  db.run(sql, valores, function(err) {
    if (err) {
      return res.status(500).json({ 
        error: 'Error al actualizar la tarea',
        detalle: err.message 
      });
    }
    
    if (this.changes === 0) {
      return res.status(404).json({ 
        error: 'Tarea no encontrada' 
      });
    }
    
    // Obtener la tarea actualizada
    db.get('SELECT * FROM tareas WHERE id = ?', [id], (err, row) => {
      if (err) {
        return res.status(500).json({ error: err.message });
      }
      res.json(row);
    });
  });
});

// ============================================
// ELIMINAR (DELETE) - Borrar tarea
// ============================================
app.delete('/api/tareas/:id', (req, res) => {
  const { id } = req.params;
  const sql = 'DELETE FROM tareas WHERE id = ?';
  
  db.run(sql, [id], function(err) {
    if (err) {
      return res.status(500).json({ 
        error: 'Error al eliminar la tarea',
        detalle: err.message 
      });
    }
    
    if (this.changes === 0) {
      return res.status(404).json({ 
        error: 'Tarea no encontrada' 
      });
    }
    
    res.json({ 
      mensaje: 'Tarea eliminada exitosamente',
      id: parseInt(id)
    });
  });
});

// Iniciar servidor
const PUERTO = process.env.PORT || 3000;
app.listen(PUERTO, () => {
  console.log(`🚀 Servidor corriendo en http://localhost:${PUERTO}`);
  console.log(`📊 Base de datos: ${dbPath}`);
});

// Cerrar la base de datos al terminar el proceso
process.on('SIGINT', () => {
  db.close((err) => {
    if (err) {
      console.error(err.message);
    }
    console.log('Base de datos cerrada');
    process.exit(0);
  });
});
```

### Probar el Servidor con BD

```bash
node server-db-basic.js
```

---

## 5. Promesas y Async/Await

### ¿Por qué usar Promesas?

Los callbacks pueden volverse complicados (callback hell). Las promesas y async/await hacen el código más limpio.

### Método 2: Con Promesas (Recomendado)

Primero, crea `database/db.js` para centralizar la conexión:

```javascript
const sqlite3 = require('sqlite3');
const { open } = require('sqlite');
const path = require('path');

let db = null;

async function conectarDB() {
  if (db) {
    return db;
  }

  db = await open({
    filename: path.join(__dirname, 'tareas.db'),
    driver: sqlite3.Database
  });

  console.log('✅ Conectado a la base de datos SQLite');
  return db;
}

async function cerrarDB() {
  if (db) {
    await db.close();
    db = null;
    console.log('✅ Conexión a la base de datos cerrada');
  }
}

module.exports = { conectarDB, cerrarDB };
```

Ahora crea `server-db-async.js`:

```javascript
const express = require('express');
const { conectarDB, cerrarDB } = require('./database/db');

const app = express();
app.use(express.json());

// ============================================
// LEER (READ) - Obtener todas las tareas
// ============================================
app.get('/api/tareas', async (req, res) => {
  try {
    const db = await conectarDB();
    const tareas = await db.all('SELECT * FROM tareas ORDER BY fecha_creacion DESC');
    res.json(tareas);
  } catch (error) {
    res.status(500).json({ 
      error: 'Error al obtener tareas',
      detalle: error.message 
    });
  }
});

// ============================================
// LEER (READ) - Obtener una tarea por ID
// ============================================
app.get('/api/tareas/:id', async (req, res) => {
  try {
    const db = await conectarDB();
    const tarea = await db.get('SELECT * FROM tareas WHERE id = ?', req.params.id);
    
    if (!tarea) {
      return res.status(404).json({ error: 'Tarea no encontrada' });
    }
    
    res.json(tarea);
  } catch (error) {
    res.status(500).json({ 
      error: 'Error al obtener la tarea',
      detalle: error.message 
    });
  }
});

// ============================================
// CREAR (CREATE) - Nueva tarea
// ============================================
app.post('/api/tareas', async (req, res) => {
  try {
    const { titulo, descripcion, prioridad } = req.body;
    
    // Validación
    if (!titulo || titulo.trim().length < 3) {
      return res.status(400).json({ 
        error: 'El título debe tener al menos 3 caracteres' 
      });
    }
    
    const db = await conectarDB();
    const resultado = await db.run(
      'INSERT INTO tareas (titulo, descripcion, prioridad) VALUES (?, ?, ?)',
      [titulo, descripcion || null, prioridad || 'media']
    );
    
    // Obtener la tarea recién creada
    const nuevaTarea = await db.get('SELECT * FROM tareas WHERE id = ?', resultado.lastID);
    res.status(201).json(nuevaTarea);
  } catch (error) {
    res.status(500).json({ 
      error: 'Error al crear la tarea',
      detalle: error.message 
    });
  }
});

// ============================================
// ACTUALIZAR (UPDATE) - Modificar tarea
// ============================================
app.put('/api/tareas/:id', async (req, res) => {
  try {
    const { id } = req.params;
    const { titulo, descripcion, completada, prioridad } = req.body;
    
    const db = await conectarDB();
    
    // Verificar que la tarea existe
    const tareaExiste = await db.get('SELECT id FROM tareas WHERE id = ?', id);
    if (!tareaExiste) {
      return res.status(404).json({ error: 'Tarea no encontrada' });
    }
    
    // Construir la query dinámicamente
    const campos = [];
    const valores = [];
    
    if (titulo !== undefined) {
      campos.push('titulo = ?');
      valores.push(titulo);
    }
    if (descripcion !== undefined) {
      campos.push('descripcion = ?');
      valores.push(descripcion);
    }
    if (completada !== undefined) {
      campos.push('completada = ?');
      valores.push(completada ? 1 : 0);
    }
    if (prioridad !== undefined) {
      campos.push('prioridad = ?');
      valores.push(prioridad);
    }
    
    if (campos.length === 0) {
      return res.status(400).json({ 
        error: 'No se proporcionaron campos para actualizar' 
      });
    }
    
    campos.push('fecha_actualizacion = CURRENT_TIMESTAMP');
    valores.push(id);
    
    await db.run(
      `UPDATE tareas SET ${campos.join(', ')} WHERE id = ?`,
      valores
    );
    
    // Obtener la tarea actualizada
    const tareaActualizada = await db.get('SELECT * FROM tareas WHERE id = ?', id);
    res.json(tareaActualizada);
  } catch (error) {
    res.status(500).json({ 
      error: 'Error al actualizar la tarea',
      detalle: error.message 
    });
  }
});

// ============================================
// ELIMINAR (DELETE) - Borrar tarea
// ============================================
app.delete('/api/tareas/:id', async (req, res) => {
  try {
    const { id } = req.params;
    const db = await conectarDB();
    
    const resultado = await db.run('DELETE FROM tareas WHERE id = ?', id);
    
    if (resultado.changes === 0) {
      return res.status(404).json({ error: 'Tarea no encontrada' });
    }
    
    res.json({ 
      mensaje: 'Tarea eliminada exitosamente',
      id: parseInt(id)
    });
  } catch (error) {
    res.status(500).json({ 
      error: 'Error al eliminar la tarea',
      detalle: error.message 
    });
  }
});

// ============================================
// BÚSQUEDA AVANZADA
// ============================================
app.get('/api/tareas/buscar', async (req, res) => {
  try {
    const { q, prioridad, completada } = req.query;
    const db = await conectarDB();
    
    let sql = 'SELECT * FROM tareas WHERE 1=1';
    const params = [];
    
    if (q) {
      sql += ' AND (titulo LIKE ? OR descripcion LIKE ?)';
      params.push(`%${q}%`, `%${q}%`);
    }
    
    if (prioridad) {
      sql += ' AND prioridad = ?';
      params.push(prioridad);
    }
    
    if (completada !== undefined) {
      sql += ' AND completada = ?';
      params.push(completada === 'true' ? 1 : 0);
    }
    
    sql += ' ORDER BY fecha_creacion DESC';
    
    const tareas = await db.all(sql, params);
    res.json(tareas);
  } catch (error) {
    res.status(500).json({ 
      error: 'Error en la búsqueda',
      detalle: error.message 
    });
  }
});

// ============================================
// ESTADÍSTICAS
// ============================================
app.get('/api/estadisticas', async (req, res) => {
  try {
    const db = await conectarDB();
    
    const total = await db.get('SELECT COUNT(*) as total FROM tareas');
    const completadas = await db.get('SELECT COUNT(*) as total FROM tareas WHERE completada = 1');
    const pendientes = await db.get('SELECT COUNT(*) as total FROM tareas WHERE completada = 0');
    const porPrioridad = await db.all(`
      SELECT prioridad, COUNT(*) as cantidad 
      FROM tareas 
      GROUP BY prioridad
    `);
    
    res.json({
      total: total.total,
      completadas: completadas.total,
      pendientes: pendientes.total,
      porPrioridad: porPrioridad
    });
  } catch (error) {
    res.status(500).json({ 
      error: 'Error al obtener estadísticas',
      detalle: error.message 
    });
  }
});

// Iniciar servidor
const PUERTO = process.env.PORT || 3000;
app.listen(PUERTO, async () => {
  await conectarDB();
  console.log(`🚀 Servidor corriendo en http://localhost:${PUERTO}`);
});

// Cerrar la base de datos al terminar
process.on('SIGINT', async () => {
  await cerrarDB();
  process.exit(0);
});
```

---

## 6. Modularización Avanzada

### Estructura Profesional

```
mi-primera-api/
├── server.js               # Punto de entrada
├── package.json
├── .env                    # Variables de entorno
├── .gitignore
│
├── config/                 # Configuraciones
│   └── database.js
│
├── database/              # Base de datos
│   ├── db.js              # Conexión
│   ├── setup.js           # Inicialización
│   └── tareas.db          # Archivo SQLite
│
├── routes/                # Rutas
│   └── tareas.routes.js
│
├── controllers/           # Lógica de negocio
│   └── tareas.controller.js
│
├── models/               # Modelos de datos
│   └── tarea.model.js
│
├── middlewares/          # Middlewares personalizados
│   ├── errorHandler.js
│   └── validator.js
│
└── utils/                # Utilidades
    └── helpers.js
```

### Ejemplo: `models/tarea.model.js`

```javascript
const { conectarDB } = require('../database/db');

class TareaModel {
  // Obtener todas las tareas
  static async obtenerTodas() {
    const db = await conectarDB();
    return await db.all('SELECT * FROM tareas ORDER BY fecha_creacion DESC');
  }

  // Obtener una tarea por ID
  static async obtenerPorId(id) {
    const db = await conectarDB();
    return await db.get('SELECT * FROM tareas WHERE id = ?', id);
  }

  // Crear una nueva tarea
  static async crear(datos) {
    const db = await conectarDB();
    const { titulo, descripcion, prioridad } = datos;
    
    const resultado = await db.run(
      'INSERT INTO tareas (titulo, descripcion, prioridad) VALUES (?, ?, ?)',
      [titulo, descripcion || null, prioridad || 'media']
    );
    
    return await this.obtenerPorId(resultado.lastID);
  }

  // Actualizar una tarea
  static async actualizar(id, datos) {
    const db = await conectarDB();
    const { titulo, descripcion, completada, prioridad } = datos;
    
    const campos = [];
    const valores = [];
    
    if (titulo !== undefined) {
      campos.push('titulo = ?');
      valores.push(titulo);
    }
    if (descripcion !== undefined) {
      campos.push('descripcion = ?');
      valores.push(descripcion);
    }
    if (completada !== undefined) {
      campos.push('completada = ?');
      valores.push(completada ? 1 : 0);
    }
    if (prioridad !== undefined) {
      campos.push('prioridad = ?');
      valores.push(prioridad);
    }
    
    if (campos.length === 0) {
      throw new Error('No hay campos para actualizar');
    }
    
    campos.push('fecha_actualizacion = CURRENT_TIMESTAMP');
    valores.push(id);
    
    await db.run(
      `UPDATE tareas SET ${campos.join(', ')} WHERE id = ?`,
      valores
    );
    
    return await this.obtenerPorId(id);
  }

  // Eliminar una tarea
  static async eliminar(id) {
    const db = await conectarDB();
    const resultado = await db.run('DELETE FROM tareas WHERE id = ?', id);
    return resultado.changes > 0;
  }

  // Buscar tareas
  static async buscar(filtros) {
    const db = await conectarDB();
    const { q, prioridad, completada } = filtros;
    
    let sql = 'SELECT * FROM tareas WHERE 1=1';
    const params = [];
    
    if (q) {
      sql += ' AND (titulo LIKE ? OR descripcion LIKE ?)';
      params.push(`%${q}%`, `%${q}%`);
    }
    
    if (prioridad) {
      sql += ' AND prioridad = ?';
      params.push(prioridad);
    }
    
    if (completada !== undefined) {
      sql += ' AND completada = ?';
      params.push(completada ? 1 : 0);
    }
    
    sql += ' ORDER BY fecha_creacion DESC';
    
    return await db.all(sql, params);
  }

  // Obtener estadísticas
  static async obtenerEstadisticas() {
    const db = await conectarDB();
    
    const total = await db.get('SELECT COUNT(*) as total FROM tareas');
    const completadas = await db.get('SELECT COUNT(*) as total FROM tareas WHERE completada = 1');
    const pendientes = await db.get('SELECT COUNT(*) as total FROM tareas WHERE completada = 0');
    const porPrioridad = await db.all(`
      SELECT prioridad, COUNT(*) as cantidad 
      FROM tareas 
      GROUP BY prioridad
    `);
    
    return {
      total: total.total,
      completadas: completadas.total,
      pendientes: pendientes.total,
      porPrioridad: porPrioridad
    };
  }
}

module.exports = TareaModel;
```

### Ejemplo: `controllers/tareas.controller.js`

```javascript
const TareaModel = require('../models/tarea.model');

class TareasController {
  // GET /api/tareas
  async obtenerTodas(req, res) {
    try {
      const tareas = await TareaModel.obtenerTodas();
      res.json(tareas);
    } catch (error) {
      res.status(500).json({ 
        error: 'Error al obtener tareas',
        detalle: error.message 
      });
    }
  }

  // GET /api/tareas/:id
  async obtenerPorId(req, res) {
    try {
      const tarea = await TareaModel.obtenerPorId(req.params.id);
      
      if (!tarea) {
        return res.status(404).json({ error: 'Tarea no encontrada' });
      }
      
      res.json(tarea);
    } catch (error) {
      res.status(500).json({ 
        error: 'Error al obtener la tarea',
        detalle: error.message 
      });
    }
  }

  // POST /api/tareas
  async crear(req, res) {
    try {
      const { titulo, descripcion, prioridad } = req.body;
      
      // Validación
      if (!titulo || titulo.trim().length < 3) {
        return res.status(400).json({ 
          error: 'El título debe tener al menos 3 caracteres' 
        });
      }
      
      const nuevaTarea = await TareaModel.crear({ titulo, descripcion, prioridad });
      res.status(201).json(nuevaTarea);
    } catch (error) {
      res.status(500).json({ 
        error: 'Error al crear la tarea',
        detalle: error.message 
      });
    }
  }

  // PUT /api/tareas/:id
  async actualizar(req, res) {
    try {
      const tareaActualizada = await TareaModel.actualizar(req.params.id, req.body);
      
      if (!tareaActualizada) {
        return res.status(404).json({ error: 'Tarea no encontrada' });
      }
      
      res.json(tareaActualizada);
    } catch (error) {
      res.status(500).json({ 
        error: 'Error al actualizar la tarea',
        detalle: error.message 
      });
    }
  }

  // DELETE /api/tareas/:id
  async eliminar(req, res) {
    try {
      const eliminada = await TareaModel.eliminar(req.params.id);
      
      if (!eliminada) {
        return res.status(404).json({ error: 'Tarea no encontrada' });
      }
      
      res.json({ 
        mensaje: 'Tarea eliminada exitosamente',
        id: parseInt(req.params.id)
      });
    } catch (error) {
      res.status(500).json({ 
        error: 'Error al eliminar la tarea',
        detalle: error.message 
      });
    }
  }

  // GET /api/tareas/buscar
  async buscar(req, res) {
    try {
      const tareas = await TareaModel.buscar(req.query);
      res.json(tareas);
    } catch (error) {
      res.status(500).json({ 
        error: 'Error en la búsqueda',
        detalle: error.message 
      });
    }
  }

  // GET /api/estadisticas
  async obtenerEstadisticas(req, res) {
    try {
      const estadisticas = await TareaModel.obtenerEstadisticas();
      res.json(estadisticas);
    } catch (error) {
      res.status(500).json({ 
        error: 'Error al obtener estadísticas',
        detalle: error.message 
      });
    }
  }
}

module.exports = new TareasController();
```

### Ejemplo: `routes/tareas.routes.js`

```javascript
const express = require('express');
const router = express.Router();
const tareasController = require('../controllers/tareas.controller');

// Rutas CRUD
router.get('/', tareasController.obtenerTodas);
router.get('/buscar', tareasController.buscar);  // Debe ir antes de /:id
router.get('/:id', tareasController.obtenerPorId);
router.post('/', tareasController.crear);
router.put('/:id', tareasController.actualizar);
router.delete('/:id', tareasController.eliminar);

// Ruta de estadísticas
router.get('/stats/resumen', tareasController.obtenerEstadisticas);

module.exports = router;
```

### Nuevo `server.js` Modular

```javascript
const express = require('express');
const { conectarDB, cerrarDB } = require('./database/db');
const tareasRoutes = require('./routes/tareas.routes');

const app = express();

// Middlewares
app.use(express.json());

// Rutas
app.get('/', (req, res) => {
  res.json({
    mensaje: 'API de Tareas con SQLite',
    version: '2.0.0',
    endpoints: {
      tareas: '/api/tareas',
      estadisticas: '/api/estadisticas'
    }
  });
});

app.use('/api/tareas', tareasRoutes);

// Middleware de error 404
app.use((req, res) => {
  res.status(404).json({ error: 'Ruta no encontrada' });
});

// Middleware de manejo de errores
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(err.status || 500).json({
    error: err.message || 'Error interno del servidor'
  });
});

// Iniciar servidor
const PUERTO = process.env.PORT || 3000;
app.listen(PUERTO, async () => {
  await conectarDB();
  console.log(`🚀 Servidor corriendo en http://localhost:${PUERTO}`);
});

// Cerrar la base de datos al terminar
process.on('SIGINT', async () => {
  await cerrarDB();
  process.exit(0);
});
```

---

## 7. Mejores Prácticas

### ✅ Validación de Datos

Usa librerías como `express-validator`:

```bash
npm install express-validator
```

```javascript
const { body, validationResult } = require('express-validator');

// Middleware de validación
const validarTarea = [
  body('titulo')
    .trim()
    .isLength({ min: 3, max: 100 })
    .withMessage('El título debe tener entre 3 y 100 caracteres'),
  body('descripcion')
    .optional()
    .trim()
    .isLength({ max: 500 })
    .withMessage('La descripción no debe exceder 500 caracteres'),
  body('prioridad')
    .optional()
    .isIn(['baja', 'media', 'alta'])
    .withMessage('La prioridad debe ser: baja, media o alta')
];

// Usar en la ruta
router.post('/', validarTarea, (req, res) => {
  const errors = validationResult(req);
  if (!errors.isEmpty()) {
    return res.status(400).json({ errors: errors.array() });
  }
  // ... resto del código
});
```

### ✅ Variables de Entorno

Crea `.env`:

```env
PORT=3000
NODE_ENV=development
DB_PATH=./database/tareas.db
```

Instala dotenv:

```bash
npm install dotenv
```

Carga en `server.js`:

```javascript
require('dotenv').config();

const PUERTO = process.env.PORT || 3000;
```

### ✅ Manejo de Errores

Crea `middlewares/errorHandler.js`:

```javascript
function errorHandler(err, req, res, next) {
  console.error(err.stack);

  if (err.name === 'ValidationError') {
    return res.status(400).json({
      error: 'Error de validación',
      detalles: err.message
    });
  }

  if (err.code === 'SQLITE_CONSTRAINT') {
    return res.status(409).json({
      error: 'Conflicto con los datos existentes'
    });
  }

  res.status(err.status || 500).json({
    error: err.message || 'Error interno del servidor',
    ...(process.env.NODE_ENV === 'development' && { stack: err.stack })
  });
}

module.exports = errorHandler;
```

### ✅ Logging

Instala Morgan para registrar peticiones:

```bash
npm install morgan
```

```javascript
const morgan = require('morgan');

app.use(morgan('dev'));  // En desarrollo
app.use(morgan('combined'));  // En producción
```

---

## 8. Proyecto Final Integrado

### Checklist de Funcionalidades

- [x] Servidor Express configurado
- [x] Base de datos SQLite3
- [x] Operaciones CRUD completas
- [x] Async/Await
- [x] Estructura modular (MVC)
- [x] Validación de datos
- [x] Manejo de errores
- [x] Variables de entorno
- [ ] Autenticación (próximo nivel)
- [ ] Testing (próximo nivel)
- [ ] Documentación API (próximo nivel)

---

## 📚 Recursos Adicionales

**Documentación:**
- [SQLite Documentation](https://www.sqlite.org/docs.html)
- [node-sqlite3](https://github.com/TryGhost/node-sqlite3)
- [sqlite (wrapper)](https://github.com/kriasoft/node-sqlite)

**Herramientas:**
- [DB Browser for SQLite](https://sqlitebrowser.org/) - Ver y editar archivos .db
- [SQLite Online](https://sqliteonline.com/) - Probar SQL en el navegador

**Próximos Pasos:**
1. Implementar autenticación con JWT
2. Agregar tests con Jest
3. Documentar la API con Swagger
4. Desplegar en la nube (Render, Railway)

---

¡Felicidades! Ahora tienes una API REST completa con persistencia de datos. 🎉