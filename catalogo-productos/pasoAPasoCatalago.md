# 🛍️ Catálogo de Productos - Fake Store API

## Paso a Paso desde Cero

### 📋 ¿Qué vamos a crear?

Un catálogo de productos que consume la API de Fake Store, mostrando:
- Imágenes de productos
- Nombre y descripción
- Precio
- Botón "Agregar al carrito"
- Carrito de compras funcional

---

## 🚀 Paso 1: Crear la Estructura de Carpetas

Crea esta estructura en tu computadora:

```
catalogo-productos/
├── index.html
├── css/
│   └── styles.css
└── js/
    └── app.js
```

**Comandos en la terminal:**

```bash
# Crear la carpeta del proyecto
mkdir catalogo-productos
cd catalogo-productos

# Crear las subcarpetas
mkdir css
mkdir js

# Crear los archivos
touch index.html
touch css/styles.css
touch js/app.js
```

**En Windows (PowerShell):**
```powershell
New-Item -ItemType Directory -Path catalogo-productos
cd catalogo-productos
New-Item -ItemType Directory -Path css, js
New-Item -ItemType File -Path index.html, css/styles.css, js/app.js
```

---

## 📄 Paso 2: Crear el HTML (index.html)

Copia este código en `index.html`:

```html

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Catálogo de Productos - Fake Store</title>
    <link rel="stylesheet" href="css/styles.css">
</head>
<body>
    <!-- ENCABEZADO -->
    <header>
        <div class="container">
            <h1>🛍️ Mi Tienda Online</h1>
            <div class="carrito-header">
                <button id="btn-carrito">
                    🛒 Carrito (<span id="contador-carrito">0</span>)
                </button>
            </div>
        </div>
    </header>

    <!-- CONTENEDOR PRINCIPAL -->
    <div class="container">
        
        <!-- LOADER (mientras carga) -->
        <div id="loader" class="loader">
            <div class="spinner"></div>
            <p>Cargando productos...</p>
        </div>

        <!-- CATÁLOGO DE PRODUCTOS -->
        <div id="catalogo" class="catalogo">
            <!-- Los productos se cargarán aquí con JavaScript -->
        </div>

    </div>

    <!-- MODAL DEL CARRITO -->
    <div id="modal-carrito" class="modal">
        <div class="modal-contenido">
            <span class="cerrar">&times;</span>
            <h2>🛒 Tu Carrito de Compras</h2>
            <div id="items-carrito"></div>
            <div class="total-carrito">
                <h3>Total: $<span id="total-carrito">0.00</span></h3>
            </div>
            <button class="btn-finalizar">Finalizar Compra</button>
            <button class="btn-vaciar">Vaciar Carrito</button>
        </div>
    </div>

    <!-- JavaScript -->
    <script src="js/app.js"></script>
</body>
</html>
```

---

## 🎨 Paso 3: Crear los Estilos (css/styles.css)

Copia este código en `css/styles.css`:

```css
/* RESET Y ESTILOS GENERALES */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    padding-bottom: 50px;
}

.container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 20px;
}

/* ENCABEZADO */
header {
    background: white;
    box-shadow: 0 2px 10px rgba(0,0,0,0.1);
    margin-bottom: 30px;
}

header .container {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

header h1 {
    color: #667eea;
    font-size: 2rem;
}

.carrito-header button {
    background: #667eea;
    color: white;
    border: none;
    padding: 12px 24px;
    border-radius: 25px;
    font-size: 1rem;
    cursor: pointer;
    transition: all 0.3s;
}

.carrito-header button:hover {
    background: #764ba2;
    transform: scale(1.05);
}

/* FILTROS */
.filtros {
    background: white;
    padding: 20px;
    border-radius: 10px;
    margin-bottom: 30px;
    box-shadow: 0 2px 10px rgba(0,0,0,0.1);
}

.filtros h2 {
    margin-bottom: 15px;
    color: #333;
}

.filtro-btn {
    background: #f0f0f0;
    border: 2px solid transparent;
    padding: 10px 20px;
    margin: 5px;
    border-radius: 20px;
    cursor: pointer;
    transition: all 0.3s;
    font-size: 0.9rem;
}

.filtro-btn:hover {
    background: #e0e0e0;
}

.filtro-btn.active {
    background: #667eea;
    color: white;
    border-color: #667eea;
}

/* LOADER */
.loader {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    min-height: 300px;
    background: white;
    border-radius: 10px;
    box-shadow: 0 2px 10px rgba(0,0,0,0.1);
}

.spinner {
    border: 4px solid #f3f3f3;
    border-top: 4px solid #667eea;
    border-radius: 50%;
    width: 50px;
    height: 50px;
    animation: spin 1s linear infinite;
}

@keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
}

.loader p {
    margin-top: 20px;
    color: #667eea;
    font-size: 1.2rem;
}

/* CATÁLOGO DE PRODUCTOS */
.catalogo {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
    gap: 25px;
}

.producto {
    background: white;
    border-radius: 15px;
    padding: 20px;
    box-shadow: 0 5px 15px rgba(0,0,0,0.1);
    transition: all 0.3s;
    display: flex;
    flex-direction: column;
}

.producto:hover {
    transform: translateY(-10px);
    box-shadow: 0 10px 25px rgba(0,0,0,0.2);
}

.producto-imagen {
    width: 100%;
    height: 200px;
    object-fit: contain;
    margin-bottom: 15px;
    border-radius: 10px;
}

.producto-categoria {
    background: #f0f0f0;
    color: #667eea;
    font-size: 0.75rem;
    padding: 5px 10px;
    border-radius: 15px;
    display: inline-block;
    margin-bottom: 10px;
    text-transform: uppercase;
}

.producto-titulo {
    font-size: 1.1rem;
    color: #333;
    margin-bottom: 10px;
    min-height: 50px;
    line-height: 1.3;
}

.producto-descripcion {
    color: #666;
    font-size: 0.9rem;
    margin-bottom: 15px;
    flex-grow: 1;
    display: -webkit-box;
    -webkit-line-clamp: 3;
    -webkit-box-orient: vertical;
    overflow: hidden;
}

.producto-info {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-top: auto;
    padding-top: 15px;
    border-top: 1px solid #f0f0f0;
}

.producto-precio {
    font-size: 1.5rem;
    font-weight: bold;
    color: #667eea;
}

.producto-rating {
    font-size: 0.9rem;
    color: #ffa500;
}

.btn-agregar {
    width: 100%;
    background: #667eea;
    color: white;
    border: none;
    padding: 12px;
    border-radius: 25px;
    font-size: 1rem;
    cursor: pointer;
    margin-top: 15px;
    transition: all 0.3s;
}

.btn-agregar:hover {
    background: #764ba2;
    transform: scale(1.02);
}

.btn-agregar:active {
    transform: scale(0.98);
}

/* MODAL DEL CARRITO */
.modal {
    display: none;
    position: fixed;
    z-index: 1000;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.6);
    animation: fadeIn 0.3s;
}

@keyframes fadeIn {
    from { opacity: 0; }
    to { opacity: 1; }
}

.modal-contenido {
    background-color: white;
    margin: 5% auto;
    padding: 30px;
    border-radius: 15px;
    width: 90%;
    max-width: 600px;
    max-height: 80vh;
    overflow-y: auto;
    animation: slideDown 0.3s;
    box-shadow: 0 10px 50px rgba(0,0,0,0.3);
}

@keyframes slideDown {
    from {
        transform: translateY(-50px);
        opacity: 0;
    }
    to {
        transform: translateY(0);
        opacity: 1;
    }
}

.cerrar {
    color: #aaa;
    float: right;
    font-size: 28px;
    font-weight: bold;
    cursor: pointer;
    transition: color 0.3s;
}

.cerrar:hover {
    color: #667eea;
}

.modal-contenido h2 {
    margin-bottom: 20px;
    color: #333;
}

/* ITEMS DEL CARRITO */
.item-carrito {
    display: flex;
    gap: 15px;
    padding: 15px;
    margin-bottom: 15px;
    background: #f8f8f8;
    border-radius: 10px;
    align-items: center;
}

.item-carrito img {
    width: 60px;
    height: 60px;
    object-fit: contain;
    border-radius: 5px;
}

.item-carrito-info {
    flex-grow: 1;
}

.item-carrito-info h4 {
    font-size: 0.95rem;
    margin-bottom: 5px;
    color: #333;
}

.item-carrito-precio {
    color: #667eea;
    font-weight: bold;
}

.item-carrito-cantidad {
    display: flex;
    gap: 10px;
    align-items: center;
    margin-top: 10px;
}

.btn-cantidad {
    background: #667eea;
    color: white;
    border: none;
    width: 30px;
    height: 30px;
    border-radius: 50%;
    cursor: pointer;
    font-size: 1rem;
    transition: all 0.3s;
}

.btn-cantidad:hover {
    background: #764ba2;
    transform: scale(1.1);
}

.btn-eliminar {
    background: #ff4757;
    color: white;
    border: none;
    padding: 8px 15px;
    border-radius: 5px;
    cursor: pointer;
    font-size: 0.9rem;
    transition: all 0.3s;
}

.btn-eliminar:hover {
    background: #ff3838;
}

/* TOTAL DEL CARRITO */
.total-carrito {
    background: #667eea;
    color: white;
    padding: 20px;
    border-radius: 10px;
    margin: 20px 0;
    text-align: center;
}

.total-carrito h3 {
    font-size: 1.8rem;
}

/* BOTONES DEL MODAL */
.btn-finalizar,
.btn-vaciar {
    width: 100%;
    padding: 15px;
    border: none;
    border-radius: 25px;
    font-size: 1.1rem;
    cursor: pointer;
    margin-top: 10px;
    transition: all 0.3s;
}

.btn-finalizar {
    background: #4CAF50;
    color: white;
}

.btn-finalizar:hover {
    background: #45a049;
    transform: scale(1.02);
}

.btn-vaciar {
    background: #f44336;
    color: white;
}

.btn-vaciar:hover {
    background: #da190b;
    transform: scale(1.02);
}

/* CARRITO VACÍO */
.carrito-vacio {
    text-align: center;
    padding: 40px;
    color: #999;
}

.carrito-vacio p {
    font-size: 1.2rem;
    margin-top: 20px;
}

/* RESPONSIVE */
@media (max-width: 768px) {
    header h1 {
        font-size: 1.5rem;
    }

    .catalogo {
        grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
        gap: 15px;
    }

    .modal-contenido {
        width: 95%;
        margin: 10% auto;
        padding: 20px;
    }

    .filtros {
        text-align: center;
    }

    .filtro-btn {
        font-size: 0.85rem;
        padding: 8px 15px;
    }
}
```

---

## 💻 Paso 4: Crear el JavaScript (js/app.js)

Copia este código en `js/app.js`:

```javascript
// ==============================================
// VARIABLES GLOBALES
// ==============================================
let productos = [];
let carrito = [];
const API_URL = 'https://fakestoreapi.com/products';

// ==============================================
// ELEMENTOS DEL DOM
// ==============================================
const catalogo = document.getElementById('catalogo');
const loader = document.getElementById('loader');
const btnCarrito = document.getElementById('btn-carrito');
const modalCarrito = document.getElementById('modal-carrito');
const cerrarModal = document.querySelector('.cerrar');
const itemsCarrito = document.getElementById('items-carrito');
const totalCarrito = document.getElementById('total-carrito');
const contadorCarrito = document.getElementById('contador-carrito');
const btnVaciar = document.querySelector('.btn-vaciar');
const btnFinalizar = document.querySelector('.btn-finalizar');
const filtrosBtns = document.querySelectorAll('.filtro-btn');

// ==============================================
// FUNCIÓN PARA OBTENER PRODUCTOS DE LA API
// ==============================================
async function obtenerProductos() {
    try {
        mostrarLoader();
        
        const response = await fetch(API_URL);
        
        if (!response.ok) {
            throw new Error('Error al obtener los productos');
        }
        
        productos = await response.json();
        mostrarProductos(productos);
        ocultarLoader();
        
    } catch (error) {
        console.error('Error:', error);
        catalogo.innerHTML = `
            <div style="grid-column: 1/-1; text-align: center; padding: 50px;">
                <h2 style="color: #ff4757;">❌ Error al cargar los productos</h2>
                <p style="color: white; margin-top: 10px;">Por favor, intenta nuevamente más tarde</p>
            </div>
        `;
        ocultarLoader();
    }
}

// ==============================================
// FUNCIÓN PARA MOSTRAR PRODUCTOS EN EL CATÁLOGO
// ==============================================
function mostrarProductos(productosArray) {
    catalogo.innerHTML = '';
    
    if (productosArray.length === 0) {
        catalogo.innerHTML = `
            <div style="grid-column: 1/-1; text-align: center; padding: 50px;">
                <h2 style="color: white;">No se encontraron productos</h2>
            </div>
        `;
        return;
    }
    
    productosArray.forEach(producto => {
        const productoDiv = document.createElement('div');
        productoDiv.className = 'producto';
        
        productoDiv.innerHTML = `
            <img src="${producto.image}" alt="${producto.title}" class="producto-imagen">
            <span class="producto-categoria">${producto.category}</span>
            <h3 class="producto-titulo">${producto.title}</h3>
            <p class="producto-descripcion">${producto.description}</p>
            <div class="producto-info">
                <span class="producto-precio">$${producto.price.toFixed(2)}</span>
                <span class="producto-rating">⭐ ${producto.rating.rate} (${producto.rating.count})</span>
            </div>
            <button class="btn-agregar" onclick="agregarAlCarrito(${producto.id})">
                🛒 Agregar al Carrito
            </button>
        `;
        
        catalogo.appendChild(productoDiv);
    });
}

// ==============================================
// FUNCIÓN PARA AGREGAR PRODUCTO AL CARRITO
// ==============================================
function agregarAlCarrito(id) {
    const producto = productos.find(p => p.id === id);
    
    if (!producto) return;
    
    // Verificar si el producto ya está en el carrito
    const productoEnCarrito = carrito.find(item => item.id === id);
    
    if (productoEnCarrito) {
        productoEnCarrito.cantidad++;
    } else {
        carrito.push({
            ...producto,
            cantidad: 1
        });
    }
    
    actualizarCarrito();
    mostrarNotificacion('Producto agregado al carrito');
}

// ==============================================
// FUNCIÓN PARA ACTUALIZAR EL CARRITO
// ==============================================
function actualizarCarrito() {
    // Actualizar contador
    const totalItems = carrito.reduce((sum, item) => sum + item.cantidad, 0);
    contadorCarrito.textContent = totalItems;
    
    // Actualizar vista del carrito
    if (carrito.length === 0) {
        itemsCarrito.innerHTML = `
            <div class="carrito-vacio">
                <h3>🛒</h3>
                <p>Tu carrito está vacío</p>
            </div>
        `;
        totalCarrito.textContent = '0.00';
        return;
    }
    
    itemsCarrito.innerHTML = '';
    
    carrito.forEach(item => {
        const itemDiv = document.createElement('div');
        itemDiv.className = 'item-carrito';
        
        itemDiv.innerHTML = `
            <img src="${item.image}" alt="${item.title}">
            <div class="item-carrito-info">
                <h4>${item.title}</h4>
                <p class="item-carrito-precio">$${item.price.toFixed(2)}</p>
                <div class="item-carrito-cantidad">
                    <button class="btn-cantidad" onclick="cambiarCantidad(${item.id}, -1)">-</button>
                    <span>Cantidad: ${item.cantidad}</span>
                    <button class="btn-cantidad" onclick="cambiarCantidad(${item.id}, 1)">+</button>
                </div>
            </div>
            <button class="btn-eliminar" onclick="eliminarDelCarrito(${item.id})">🗑️</button>
        `;
        
        itemsCarrito.appendChild(itemDiv);
    });
    
    // Calcular total
    const total = carrito.reduce((sum, item) => sum + (item.price * item.cantidad), 0);
    totalCarrito.textContent = total.toFixed(2);
    
    // Guardar en localStorage
    localStorage.setItem('carrito', JSON.stringify(carrito));
}

// ==============================================
// FUNCIÓN PARA CAMBIAR CANTIDAD DE UN PRODUCTO
// ==============================================
function cambiarCantidad(id, cambio) {
    const producto = carrito.find(item => item.id === id);
    
    if (!producto) return;
    
    producto.cantidad += cambio;
    
    if (producto.cantidad <= 0) {
        eliminarDelCarrito(id);
        return;
    }
    
    actualizarCarrito();
}

// ==============================================
// FUNCIÓN PARA ELIMINAR PRODUCTO DEL CARRITO
// ==============================================
function eliminarDelCarrito(id) {
    carrito = carrito.filter(item => item.id !== id);
    actualizarCarrito();
    mostrarNotificacion('Producto eliminado del carrito');
}

// ==============================================
// FUNCIÓN PARA VACIAR EL CARRITO
// ==============================================
function vaciarCarrito() {
    if (carrito.length === 0) return;
    
    if (confirm('¿Estás seguro de que quieres vaciar el carrito?')) {
        carrito = [];
        actualizarCarrito();
        mostrarNotificacion('Carrito vaciado');
    }
}

// ==============================================
// FUNCIÓN PARA FINALIZAR COMPRA
// ==============================================
function finalizarCompra() {
    if (carrito.length === 0) {
        alert('Tu carrito está vacío');
        return;
    }
    
    const total = carrito.reduce((sum, item) => sum + (item.price * item.cantidad), 0);
    
    alert(`
        ¡Compra finalizada! 🎉
        
        Total de productos: ${carrito.reduce((sum, item) => sum + item.cantidad, 0)}
        Total a pagar: $${total.toFixed(2)}
        
        ¡Gracias por tu compra!
    `);
    
    carrito = [];
    actualizarCarrito();
    modalCarrito.style.display = 'none';
}

// ==============================================
// FUNCIÓN PARA FILTRAR PRODUCTOS
// ==============================================
function filtrarProductos(categoria) {
    if (categoria === 'all') {
        mostrarProductos(productos);
    } else {
        const productosFiltrados = productos.filter(p => p.category === categoria);
        mostrarProductos(productosFiltrados);
    }
}

// ==============================================
// FUNCIÓN PARA MOSTRAR/OCULTAR LOADER
// ==============================================
function mostrarLoader() {
    loader.style.display = 'flex';
    catalogo.innerHTML = '';
}

function ocultarLoader() {
    loader.style.display = 'none';
}

// ==============================================
// FUNCIÓN PARA MOSTRAR NOTIFICACIONES
// ==============================================
function mostrarNotificacion(mensaje) {
    // Crear elemento de notificación
    const notificacion = document.createElement('div');
    notificacion.style.cssText = `
        position: fixed;
        top: 20px;
        right: 20px;
        background: #4CAF50;
        color: white;
        padding: 15px 25px;
        border-radius: 10px;
        box-shadow: 0 5px 15px rgba(0,0,0,0.3);
        z-index: 2000;
        animation: slideInRight 0.3s ease;
    `;
    notificacion.textContent = mensaje;
    
    document.body.appendChild(notificacion);
    
    // Eliminar después de 3 segundos
    setTimeout(() => {
        notificacion.style.animation = 'slideOutRight 0.3s ease';
        setTimeout(() => {
            document.body.removeChild(notificacion);
        }, 300);
    }, 3000);
}

// ==============================================
// EVENT LISTENERS
// ==============================================

// Abrir modal del carrito
btnCarrito.addEventListener('click', () => {
    modalCarrito.style.display = 'block';
});

// Cerrar modal
cerrarModal.addEventListener('click', () => {
    modalCarrito.style.display = 'none';
});

// Cerrar modal al hacer clic fuera
window.addEventListener('click', (e) => {
    if (e.target === modalCarrito) {
        modalCarrito.style.display = 'none';
    }
});

// Vaciar carrito
btnVaciar.addEventListener('click', vaciarCarrito);

// Finalizar compra
btnFinalizar.addEventListener('click', finalizarCompra);

// Filtros de categoría
filtrosBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        // Remover clase active de todos los botones
        filtrosBtns.forEach(b => b.classList.remove('active'));
        
        // Agregar clase active al botón clickeado
        btn.classList.add('active');
        
        // Filtrar productos
        const categoria = btn.dataset.categoria;
        filtrarProductos(categoria);
    });
});

// ==============================================
// CARGAR CARRITO DEL LOCALSTORAGE AL INICIAR
// ==============================================
function cargarCarrito() {
    const carritoGuardado = localStorage.getItem('carrito');
    if (carritoGuardado) {
        carrito = JSON.parse(carritoGuardado);
        actualizarCarrito();
    }
}

// ==============================================
// INICIALIZAR LA APLICACIÓN
// ==============================================
document.addEventListener('DOMContentLoaded', () => {
    obtenerProductos();
    cargarCarrito();
});
```

---

## 🎯 Paso 5: Abrir el Proyecto

### Opción 1: Doble clic en index.html

Simplemente haz doble clic en el archivo `index.html` y se abrirá en tu navegador.

### Opción 2: Usar Live Server (Recomendado)

1. Instala la extensión "Live Server" en VS Code
2. Haz clic derecho en `index.html`
3. Selecciona "Open with Live Server"

---

## ✅ ¿Qué hace cada archivo?

### 📄 index.html
- Estructura HTML del catálogo
- Header con botón del carrito
- Filtros por categoría
- Sección para mostrar productos
- Modal del carrito de compras

### 🎨 styles.css
- Diseño moderno con gradientes
- Grid responsivo para productos
- Estilos para el modal
- Animaciones y transiciones
- Diseño adaptable para móviles

### 💻 app.js
- Consumo de la API con `fetch()`
- Renderizado dinámico de productos
- Lógica del carrito de compras
- Filtros por categoría
- Persistencia con LocalStorage
- Notificaciones

---

## 🎓 Conceptos que se Aprenden

✅ **Consumo de APIs REST** con `fetch()`  
✅ **Async/Await** para operaciones asíncronas  
✅ **Manipulación del DOM** con JavaScript  
✅ **LocalStorage** para persistencia de datos  
✅ **CSS Grid** para layouts responsivos  
✅ **Event Listeners** y manejo de eventos  
✅ **Arrays** y métodos como `map()`, `filter()`, `reduce()`

---

## 🚀 Funcionalidades

### ✨ Catálogo
- ✅ Muestra productos de la Fake Store API
- ✅ Filtros por categoría
- ✅ Diseño tipo tarjeta
- ✅ Información completa (imagen, precio, rating)

### 🛒 Carrito
- ✅ Agregar productos
- ✅ Aumentar/disminuir cantidad
- ✅ Eliminar productos
- ✅ Calcular total automáticamente
- ✅ Guardar en LocalStorage
- ✅ Contador de productos en el header

### 🎨 Diseño
- ✅ Responsivo (funciona en móviles)
- ✅ Animaciones suaves
- ✅ Colores atractivos
- ✅ Modal para el carrito

---

## 🔧 Mejoras Opcionales

### Para estudiantes avanzados:

1. **Agregar búsqueda**: Input para buscar productos por nombre
2. **Ordenamiento**: Botones para ordenar por precio
3. **Más/Menos**: Ver más detalles del producto
4. **Backend propio**: Guardar el carrito en una base de datos
5. **Autenticación**: Login de usuarios
6. **Pasarela de pago**: Integrar Stripe o PayPal

---

## 📱 Cómo Quedará

El proyecto incluye:
- ✅ Catálogo de productos con grid responsivo
- ✅ Carrito funcional con contador
- ✅ Modal para ver el carrito
- ✅ Filtros por categoría
- ✅ Notificaciones de acciones
- ✅ Persistencia con LocalStorage
- ✅ Diseño moderno y atractivo

---

## 🆘 Problemas Comunes

### ❌ "No se cargan los productos"
**Solución:** Verifica tu conexión a internet. La API necesita internet.

### ❌ "Los estilos no se aplican"
**Solución:** Verifica que las rutas en el HTML sean correctas:
```html
<link rel="stylesheet" href="css/styles.css">
<script src="js/app.js"></script>
```

### ❌ "Error de CORS"
**Solución:** Usa Live Server en vez de abrir el HTML directamente.

---

## 🎉 ¡Listo!

Ahora tienes un catálogo de productos completamente funcional que consume una API real.

**Fake Store API:** https://fakestoreapi.com/

---

¡Felicitaciones! Has creado tu primer proyecto consumiendo una API REST. 🚀