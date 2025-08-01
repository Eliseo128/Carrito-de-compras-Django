Aquí tienes el código de `urls.py` convertido a **Markdown**, ideal para documentación técnica (como un `README.md` o documentación interna):

```markdown
# URLs de la Aplicación Web

Este archivo define las rutas (URLs) de la aplicación `web` utilizando el sistema de enrutamiento de Django.

```python
from django.urls import path
from . import views

app_name = 'web'

urlpatterns = [
    # Página principal
    path('', views.index, name='index'),

    # Productos por categoría
    path('categoria/<int:categoria_id>/', views.producto_categoria, name='producto_categoria'),

    # Búsqueda de productos por nombre
    path('buscar/', views.productosPorNombre, name='productos_por_nombre'),

    # Detalle de un producto específico
    path('producto/<int:producto_id>/', views.productoDetalle, name='producto_detalle'),

    # Carrito de compras
    path('carrito/', views.carrito, name='carrito'),

    # Agregar producto al carrito
    path('agregar/<int:producto_id>/', views.agregarProducto, name='agregar_producto'),

    # Eliminar producto del carrito
    path('eliminar/<int:producto_id>/', views.eliminarProducto, name='eliminar_producto'),

    # Vaciar todo el carrito
    path('vaciar/', views.vaciarCarrito, name='vaciar_carrito'),

    # Aumentar cantidad de un producto en el carrito
    path('carrito/aumentar/<int:producto_id>/', views.aumentar_producto, name='aumentar_producto'),

    # Disminuir cantidad de un producto en el carrito
    path('carrito/disminuir/<int:producto_id>/', views.disminuir_producto, name='disminuir_producto'),

    # Inicio de sesión
    path('login/', views.login_view, name='login'),

    # Registro de usuario
    path('registro/', views.registro, name='registro'),

    # Cierre de sesión
    path('logout/', views.logout_view, name='logout'),

    # Registrar pedido
    path('pedido/', views.registrarPedido, name='registrar_pedido'),

    # Actualizar datos del cliente
    path('actualizar_cliente/', views.actualizarUsuario, name='actualizar_cliente'),
]
```

## Descripción de Rutas

| Ruta | Vista | Nombre (`name`) | Descripción |
|------|-------|------------------|-------------|
| `/` | `index` | `index` | Página de inicio del sitio. |
| `/categoria/<int:categoria_id>/` | `producto_categoria` | `producto_categoria` | Muestra productos de una categoría específica. |
| `/buscar/` | `productosPorNombre` | `productos_por_nombre` | Busca productos por nombre. |
| `/producto/<int:producto_id>/` | `productoDetalle` | `producto_detalle` | Muestra detalles de un producto. |
| `/carrito/` | `carrito` | `carrito` | Muestra el contenido del carrito de compras. |
| `/agregar/<int:producto_id>/` | `agregarProducto` | `agregar_producto` | Agrega un producto al carrito. |
| `/eliminar/<int:producto_id>/` | `eliminarProducto` | `eliminar_producto` | Elimina un producto del carrito. |
| `/vaciar/` | `vaciarCarrito` | `vaciar_carrito` | Vacía todos los productos del carrito. |
| `/carrito/aumentar/<int:producto_id>/` | `aumentar_producto` | `aumentar_producto` | Aumenta la cantidad de un producto en el carrito. |
| `/carrito/disminuir/<int:producto_id>/` | `disminuir_producto` | `disminuir_producto` | Disminuye la cantidad de un producto en el carrito. |
| `/login/` | `login_view` | `login` | Formulario de inicio de sesión. |
| `/registro/` | `registro` | `registro` | Registro de nuevos usuarios. |
| `/logout/` | `logout_view` | `logout` | Cierra la sesión del usuario. |
| `/pedido/` | `registrarPedido` | `registrar_pedido` | Procesa y registra un nuevo pedido. |
| `/actualizar_cliente/` | `actualizarUsuario` | `actualizar_cliente` | Permite al usuario actualizar sus datos personales. |

> **Nota**: Todas las URLs están bajo el espacio de nombres `web`, por lo que en las plantillas deben referenciarse como `web:nombre_ruta` (ej: `{% url 'web:index' %}`).
```

Este formato es claro, profesional y útil tanto para desarrolladores como para documentación de proyectos.
