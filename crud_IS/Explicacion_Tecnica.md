# Taller Seguimiento 3 – Ingeniería de Software

## Portada

**Nombre del estudiante**: Simón Pérez  
**Nombre del curso**: Ingeniería de Software  
**Fecha**: Mayo 2023  
**Enlace al repositorio en GitHub**: [https://github.com/tu-usuario/IS-TALLER](https://github.com/tu-usuario/IS-TALLER)  

![Captura de pantalla del resultado final](https://via.placeholder.com/800x400?text=Captura+de+la+Aplicación)

## Explicación de la Estructura por Capas

La aplicación implementa una arquitectura por capas que separa claramente las responsabilidades del sistema:

### Capa de Presentación

Compuesta por los archivos:
- **index.html**: Define la estructura visual de la aplicación
- **style.css**: Implementa los estilos visuales con diseño neumórfico
- **script.js**: Maneja la interacción del usuario y las peticiones al servidor

Esta capa se encarga exclusivamente de la interfaz de usuario y la experiencia de interacción, sin contener lógica de negocio.

### Capa de Lógica de Negocio

Implementada en **backend.py** a través de las rutas de Flask:
- Ruta principal (`/`): Renderiza la página inicial
- Ruta de productos (`/productos`): Maneja la creación y listado de productos
- Ruta de producto específico (`/productos/<id>`): Gestiona operaciones sobre un producto individual

Esta capa procesa las solicitudes del cliente, aplica reglas de negocio y coordina la comunicación entre la presentación y los datos.

### Capa de Acceso a Datos

También implementada en **backend.py** mediante funciones específicas:
- `init_db()`: Inicializa la base de datos y crea la estructura necesaria
- `get_db()`: Proporciona conexión a la base de datos
- Operaciones CRUD implementadas en las rutas mediante consultas SQL

Esta capa se encarga de la persistencia de datos y el acceso a la base de datos SQLite3.

## Backend – backend.py

### Importación de Librerías

```python
from flask import Flask, request, jsonify, render_template
import sqlite3
import os
```

- **Flask**: Framework web para Python que facilita la creación de aplicaciones web
- **sqlite3**: Biblioteca para interactuar con bases de datos SQLite
- **os**: Módulo para interactuar con el sistema operativo, usado para manejar rutas de archivos

### Rutas

#### Ruta Principal
```python
@app.route('/')
def home():
    return render_template('index.html')
```
Esta ruta renderiza la plantilla HTML principal cuando se accede a la raíz de la aplicación.

#### Ruta de Productos (GET y POST)
```python
@app.route('/productos', methods=['GET', 'POST'])
def productos():
    db = get_db()
    if request.method == 'POST':
        data = request.get_json()
        db.execute('INSERT INTO productos (nombre, precio, descripcion) VALUES (?, ?, ?)',
                   [data['nombre'], data['precio'], data['descripcion']])
        db.commit()
        return jsonify({"mensaje": "Producto guardado correctamente"})
    cursor = db.execute('SELECT * FROM productos')
    productos = [dict(id=row[0], nombre=row[1], precio=row[2], descripcion=row[3]) for row in cursor.fetchall()]
    return jsonify(productos)
```
Esta ruta maneja dos operaciones:
- **GET**: Devuelve todos los productos en formato JSON
- **POST**: Crea un nuevo producto con los datos recibidos

#### Ruta de Producto Específico (GET, PUT, DELETE)
```python
@app.route('/productos/<int:id>', methods=['GET', 'PUT', 'DELETE'])
def producto_id(id):
    db = get_db()
    if request.method == 'GET':
        cursor = db.execute('SELECT * FROM productos WHERE id = ?', [id])
        row = cursor.fetchone()
        if row:
            return jsonify(dict(id=row[0], nombre=row[1], precio=row[2], descripcion=row[3]))
        else:
            return jsonify({"error": "Producto no encontrado"}), 404

    elif request.method == 'PUT':
        data = request.get_json()
        db.execute('UPDATE productos SET nombre = ?, precio = ?, descripcion = ? WHERE id = ?',
                   [data['nombre'], data['precio'], data['descripcion'], id])
        db.commit()
        return jsonify({"mensaje": "Producto actualizado"})

    elif request.method == 'DELETE':
        db.execute('DELETE FROM productos WHERE id = ?', [id])
        db.commit()
        return jsonify({"mensaje": "Producto eliminado"})
```
Esta ruta maneja tres operaciones sobre un producto específico:
- **GET**: Obtiene los detalles de un producto por su ID
- **PUT**: Actualiza un producto existente
- **DELETE**: Elimina un producto

### Conexión con la Base de Datos

```python
def init_db():
    with app.app_context():
        db = sqlite3.connect(DATABASE)
        db.execute('''CREATE TABLE IF NOT EXISTS productos (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            nombre TEXT NOT NULL,
            precio REAL NOT NULL,
            descripcion TEXT)''')
        db.commit()

def get_db():
    return sqlite3.connect(DATABASE)
```

Estas funciones:
- **init_db()**: Inicializa la base de datos y crea la tabla si no existe
- **get_db()**: Proporciona una conexión a la base de datos

## HTML – index.html

El archivo `index.html` funciona como la estructura principal de la vista, definiendo:

1. **Encabezado**: Metadatos, título y enlace al archivo CSS
2. **Cuerpo**:
   - Banner superior
   - Formulario para crear/editar productos
   - Tabla para mostrar los productos
   - Imagen de pie de página
   - Enlace al archivo JavaScript

### Conexión con CSS y JavaScript

```html
<link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
```
Esta línea enlaza el archivo CSS utilizando la función `url_for` de Flask para generar la URL correcta.

```html
<script src="{{ url_for('static', filename='script.js') }}"></script>
```
Esta línea enlaza el archivo JavaScript al final del documento para asegurar que se cargue después del contenido HTML.

## JavaScript – script.js

### Función cargarProductos()

```javascript
function cargarProductos() {
    fetch('/productos')
        .then(response => response.json())
        .then(data => {
            const tbody = document.querySelector('#tabla-productos tbody');
            tbody.innerHTML = '';
            data.forEach(producto => {
                tbody.innerHTML += `
                    <tr>
                        <td>${producto.id}</td>
                        <td>${producto.nombre}</td>
                        <td>${producto.precio}</td>
                        <td>${producto.descripcion}</td>
                        <td>
                            <button onclick="editarProducto(${producto.id})">Editar</button>
                            <button onclick="eliminarProducto(${producto.id})">Eliminar</button>
                        </td>
                    </tr>
                `;
            });
        });
}
```

Esta función:
1. Realiza una petición GET a la ruta `/productos`
2. Convierte la respuesta a formato JSON
3. Limpia el contenido actual de la tabla
4. Itera sobre los productos recibidos y los agrega a la tabla
5. Incluye botones para editar y eliminar cada producto

### Función guardarProducto()

```javascript
function guardarProducto() {
    const producto = {
        nombre: document.getElementById('nombre').value,
        precio: parseFloat(document.getElementById('precio').value),
        descripcion: document.getElementById('descripcion').value
    };

    const url = productoEditandoId ? `/productos/${productoEditandoId}` : '/productos';
    const method = productoEditandoId ? 'PUT' : 'POST';

    fetch(url, {
        method: method,
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(producto)
    })
    .then(response => response.json())
    .then(data => {
        document.getElementById('message').textContent = data.mensaje;
        cargarProductos();
        limpiarFormulario();
        productoEditandoId = null;
    });
}
```

Esta función:
1. Recopila los datos del formulario
2. Determina si es una creación o actualización basándose en `productoEditandoId`
3. Realiza una petición POST o PUT según corresponda
4. Muestra el mensaje de respuesta
5. Recarga la lista de productos
6. Limpia el formulario

### Función editarProducto(id)

```javascript
function editarProducto(id) {
    fetch(`/productos/${id}`)
        .then(response => response.json())
        .then(producto => {
            productoEditandoId = id;
            document.getElementById('nombre').value = producto.nombre;
            document.getElementById('precio').value = producto.precio;
            document.getElementById('descripcion').value = producto.descripcion;
            window.scrollTo(0, 0);
        });
}
```

Esta función:
1. Realiza una petición GET para obtener los datos del producto
2. Almacena el ID del producto en edición
3. Rellena el formulario con los datos del producto
4. Desplaza la página hacia arriba para mostrar el formulario

### Función eliminarProducto(id)

```javascript
function eliminarProducto(id) {
    if (confirm('¿Eliminar este producto?')) {
        fetch(`/productos/${id}`, { method: 'DELETE' })
            .then(response => response.json())
            .then(data => {
                document.getElementById('message').textContent = data.mensaje;
                cargarProductos();
            });
    }
}
```

Esta función:
1. Solicita confirmación al usuario
2. Si se confirma, realiza una petición DELETE
3. Muestra el mensaje de respuesta
4. Recarga la lista de productos

### Uso de fetch()

La API `fetch()` se utiliza para realizar peticiones HTTP al backend:
- Permite comunicación asíncrona sin recargar la página
- Maneja diferentes métodos HTTP (GET, POST, PUT, DELETE)
- Procesa respuestas JSON
- Actualiza la interfaz dinámicamente con los datos recibidos

## CSS – style.css

### Clases y Estilos Principales

#### Estilos Generales
```css
body {
    font-family: 'Segoe UI', sans-serif;
    background: #e8f5f0;
    padding: 40px;
    color: #2c5c4c;
}
```
Establece la tipografía, color de fondo, espaciado y color de texto base.

#### Imágenes
```css
img.banner, img.footer {
    width: 100%;
    border-radius: 25px;
    margin-bottom: 30px;
    box-shadow: 12px 12px 24px #c4d0cb, -12px -12px 24px #ffffff;
    transition: transform 0.3s ease;
}
```
Aplica estilos neumórficos a las imágenes de banner y pie de página.

#### Contenedores
```css
#form-container, table {
    background: #e8f5f0;
    border-radius: 25px;
    padding: 20px;
    box-shadow: 12px 12px 24px #c4d0cb, -12px -12px 24px #ffffff;
    margin-bottom: 30px;
    transition: transform 0.3s ease;
}
```
Aplica estilos neumórficos a los contenedores principales.

#### Formularios
```css
input, textarea {
    width: 100%;
    padding: 12px;
    border: none;
    border-radius: 15px;
    background: #e8f5f0;
    box-shadow: inset 6px 6px 12px #c4d0cb, inset -6px -6px 12px #ffffff;
    font-size: 14px;
    color: #2c5c4c;
    transition: all 0.3s ease;
}
```
Aplica estilos neumórficos a los campos de entrada.

#### Botones
```css
button {
    padding: 12px 24px;
    margin-right: 12px;
    border: none;
    border-radius: 15px;
    background: #4a9677;
    color: #ffffff;
    font-weight: 600;
    cursor: pointer;
    box-shadow: 6px 6px 12px #c4d0cb, -6px -6px 12px #ffffff;
    transition: all 0.3s ease;
}
```
Aplica estilos neumórficos a los botones con efectos de hover y active.

### Importancia del Diseño Neumórfico

El diseño neumórfico utilizado en esta aplicación:

1. **Mejora la experiencia de usuario**: Proporciona una interfaz moderna y atractiva
2. **Aumenta la usabilidad**: Los elementos parecen tridimensionales, facilitando la identificación de componentes interactivos
3. **Crea jerarquía visual**: Ayuda a distinguir entre diferentes secciones y elementos
4. **Proporciona feedback visual**: Las transiciones y efectos hover/active dan retroalimentación inmediata al usuario
5. **Mantiene consistencia**: El estilo unificado crea una experiencia coherente en toda la aplicación

Los efectos de sombra, transiciones suaves y cambios sutiles al interactuar con los elementos contribuyen a una experiencia de usuario más intuitiva y agradable.