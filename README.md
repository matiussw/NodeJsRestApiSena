# 📚 Mi Primera API REST con Node.js

> Proyecto educativo para aprender desarrollo de APIs REST con Node.js y Express

## 📖 Descripción

Este es un proyecto educativo diseñado para enseñar los fundamentos del desarrollo de APIs REST usando Node.js y Express. Incluye ejemplos prácticos desde lo más básico hasta integración con bases de datos.

## 🎯 Objetivos de Aprendizaje

- Comprender los conceptos básicos de Node.js
- Crear y configurar un servidor con Express
- Implementar operaciones CRUD (Create, Read, Update, Delete)
- Trabajar con bases de datos usando SQLite3
- Aplicar buenas prácticas en desarrollo de APIs

## 🚀 Requisitos Previos

Antes de comenzar, asegúrate de tener instalado:

- **Node.js** (versión 18 o superior) - [Descargar aquí](https://nodejs.org/)
- **npm** (viene incluido con Node.js)
- **Visual Studio Code** (recomendado) - [Descargar aquí](https://code.visualstudio.com/)
- **Git** (opcional) - [Descargar aquí](https://git-scm.com/)

### Verificar Instalación

```bash
node --version
# Debería mostrar: v18.x.x o superior

npm --version
# Debería mostrar: 9.x.x o superior
```

## 📦 Instalación

### 1. Clonar o Descargar el Proyecto

**Opción A - Con Git:**
```bash
git clone https://github.com/tuusuario/NodeJsRestApiSena.git
cd NodeJsRestApiSena/mi-primera-api
```

**Opción B - Descarga Manual:**
1. Descarga el proyecto como ZIP
2. Extrae los archivos
3. Abre la carpeta `mi-primera-api` en tu terminal

### 2. Instalar Dependencias

```bash
npm install
```

Este comando instalará todas las dependencias necesarias listadas en `package.json`.

## 🎮 Cómo Usar

### Iniciar el Servidor (Modo Básico)

```bash
node server.js
```

El servidor estará disponible en: `http://localhost:3000`

### Iniciar el Servidor (Modo Desarrollo)

```bash
npm run dev
```

Este modo usa **nodemon** y reinicia automáticamente el servidor cuando detecta cambios en el código.

### Detener el Servidor

Presiona `Ctrl + C` en la terminal

## 🛠️ Estructura del Proyecto

```
mi-primera-api/
│
├── server.js           # Servidor principal con rutas básicas
├── package.json        # Configuración del proyecto y dependencias
├── package-lock.json   # Versiones exactas de dependencias
├── .gitignore         # Archivos a ignorar por Git
│
├── docs/              # Documentación adicional
│   ├── guia-completa.md
│   └── ejercicios.md
│
└── database/          # (Próximamente) Archivos de base de datos
    └── tareas.db
```

## 📡 Endpoints de la API

### Obtener todas las tareas
```http
GET /api/tareas
```

**Respuesta de ejemplo:**
```json
[
  {
    "id": 1,
    "titulo": "Aprender Node.js",
    "completada": false
  },
  {
    "id": 2,
    "titulo": "Crear una API REST",
    "completada": false
  }
]
```

### Obtener una tarea por ID
```http
GET /api/tareas/:id
```

**Ejemplo:** `GET /api/tareas/1`

### Crear una nueva tarea
```http
POST /api/tareas
Content-Type: application/json

{
  "titulo": "Nueva tarea"
}
```

### Actualizar una tarea
```http
PUT /api/tareas/:id
Content-Type: application/json

{
  "titulo": "Tarea actualizada",
  "completada": true
}
```

### Eliminar una tarea
```http
DELETE /api/tareas/:id
```

## 🧪 Probar la API

### Opción 1: Thunder Client (VS Code)

1. Instala la extensión "Thunder Client" en VS Code
2. Abre Thunder Client (icono del rayo en la barra lateral)
3. Crea una nueva petición
4. Usa las URLs de los endpoints descritos arriba

### Opción 2: Navegador Web (solo GET)

Abre tu navegador y visita:
```
http://localhost:3000/api/tareas
```

### Opción 3: cURL (Terminal)

```bash
# Obtener todas las tareas
curl http://localhost:3000/api/tareas

# Crear una nueva tarea
curl -X POST http://localhost:3000/api/tareas \
  -H "Content-Type: application/json" \
  -d '{"titulo":"Aprender cURL"}'

# Actualizar una tarea
curl -X PUT http://localhost:3000/api/tareas/1 \
  -H "Content-Type: application/json" \
  -d '{"completada":true}'

# Eliminar una tarea
curl -X DELETE http://localhost:3000/api/tareas/2
```

## 📚 Documentación Adicional

- [Guía Completa de Node.js y APIs REST](docs/guia-completa.md)
- [Ejercicios Prácticos](docs/ejercicios.md)
- [Documentación de Express](https://expressjs.com/)
- [Documentación de Node.js](https://nodejs.org/docs/)

## 🐛 Solución de Problemas

### Error: "Cannot find module 'express'"

**Solución:** Ejecuta `npm install` para instalar las dependencias.

### Error: "Port 3000 is already in use"

**Solución:** Otro programa está usando el puerto 3000. Opciones:
1. Cierra el otro programa
2. Cambia el puerto en `server.js`: `const PUERTO = 3001;`

### El servidor no se reinicia con cambios

**Solución:** 
- Si usas `node server.js`: Detén (Ctrl+C) y reinicia manualmente
- Si usas `npm run dev`: Verifica que nodemon esté instalado: `npm install --save-dev nodemon`

## 🤝 Contribuir

Este es un proyecto educativo. Si encuentras errores o tienes sugerencias:

1. Reporta el problema en la sección de Issues
2. Propón mejoras mediante Pull Requests
3. Comparte tus ejercicios adicionales

## 📝 Licencia

Este proyecto es de uso educativo libre para estudiantes del SENA y la comunidad.

## 👨‍🏫 Autor

**SENA - Formación en Desarrollo de Software**

---

## 🎓 Próximos Pasos

Una vez domines este proyecto básico, puedes avanzar a:

1. ✅ **Validación de datos** (express-validator)
2. ✅ **Base de datos SQLite3**
3. ✅ **Variables de entorno** (.env)
4. ✅ **Manejo de errores avanzado**
5. ✅ **Autenticación con JWT**
6. ✅ **Documentación con Swagger**
7. ✅ **Testing con Jest**

Revisa la carpeta `docs/` para ejercicios progresivos.

---

**¿Necesitas ayuda?** Consulta con tu instructor o revisa la documentación oficial de Node.js y Express.