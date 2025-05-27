# IS TALLER - Sistema de Gestión de Productos

## Objetivo del Proyecto
Desarrollar una aplicación CRUD (Crear, Leer, Actualizar, Eliminar) utilizando arquitectura por capas con Flask y SQLite3 para gestionar un inventario de productos, aplicando principios de ingeniería de software y diseño web moderno.

## Tecnologías Utilizadas
- **Frontend**: HTML5, CSS3, JavaScript
- **Backend**: Python con Flask
- **Base de Datos**: SQLite3
- **Estilo**: Diseño Neumórfico

## Arquitectura por Capas
El proyecto implementa una arquitectura de tres capas:

1. **Capa de Presentación**: HTML, CSS y JavaScript que manejan la interfaz de usuario
2. **Capa de Lógica de Negocio**: Funciones en Flask que procesan las solicitudes
3. **Capa de Acceso a Datos**: Operaciones SQLite3 para persistencia de datos

## Instrucciones de Instalación

1. Clonar el repositorio:
```bash
git clone https://github.com/tu-usuario/IS-TALLER.git
cd IS-TALLER
```

2. Crear y activar un entorno virtual:
```bash
python -m venv venv
# En Windows
venv\Scripts\activate
# En macOS/Linux
source venv/bin/activate
```

3. Instalar dependencias:
```bash
pip install flask
```

4. Ejecutar la aplicación:
```bash
python backend.py
```

5. Abrir en el navegador:
```
http://localhost:5000
```

## Funcionalidades
- Crear nuevos productos con nombre, precio y descripción
- Visualizar todos los productos en una tabla
- Editar productos existentes
- Eliminar productos
- Interfaz con diseño neumórfico para mejor experiencia de usuario

## Estructura del Proyecto
```
crud_IS/
├── backend.py          # Servidor Flask y lógica de negocio
├── productos.db        # Base de datos SQLite3
├── static/
│   ├── script.js       # Funciones JavaScript para interacción
│   ├── style.css       # Estilos CSS con diseño neumórfico
│   ├── banner.svg      # Imagen de banner superior
│   └── footer.svg      # Imagen de pie de página
└── templates/
    └── index.html      # Estructura HTML de la aplicación
```

## Capturas de Pantalla
![Captura de la aplicación](https://via.placeholder.com/800x400?text=Captura+de+la+Aplicación)

## Créditos
**Autor**: Simón Pérez
**Contacto**: simon.perez@universidad.edu.co
**Curso**: Ingeniería de Software