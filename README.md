# Carrito-de-compras-Django
Proyecto carrito de compras
# El modelo

Perfecto, Eliseo. Te detallo un procedimiento completo y paso a paso para implementar tu proyecto de **carrito de compras en Django**, utilizando funciones en `views.py`, sin `forms.py`, basándome en los modelos y rutas que ya proporcionaste.

---

## ✅ 1. Crear el Proyecto Django

```bash
django-admin startproject tienda_carrito
cd tienda_carrito
python manage.py startapp web
```

## ✅ 2. Configurar `settings.py`

En `tienda_carrito/settings.py`:

* Agrega `'web'` en `INSTALLED_APPS`.
* Configura `MEDIA_ROOT`, `MEDIA_URL` y `STATICFILES_DIRS`:

```python
INSTALLED_APPS = [
    ...
    'web',
]

MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'

STATIC_URL = '/static/'
STATICFILES_DIRS = [BASE_DIR / "static"]
```

## ✅ 3. Configurar `urls.py` del proyecto

En `tienda_carrito/urls.py`:

```python
from django.contrib import admin
from django.urls import path, include
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('web.urls', namespace='web')),
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

---

## ✅ 4. Crear las vistas en `views.py`

Crea las funciones correspondientes (simplificado por ahora):

```python
from django.shortcuts import render, redirect, get_object_or_404
from django.contrib.auth import authenticate, login, logout
from .models import Producto, Categoria, Pedido, PedidoDetalle, Cliente
from django.contrib.auth.models import User
from django.contrib.auth.decorators import login_required
from django.db import transaction
from django.http import JsonResponse
import uuid

# Carrito en sesión
def carrito(request):
    carrito = request.session.get('carrito', {})
    productos = []
    total = 0
    for producto_id, cantidad in carrito.items():
        producto = Producto.objects.get(id=producto_id)
        subtotal = producto.precio * cantidad
        total += subtotal
        productos.append({
            'producto': producto,
            'cantidad': cantidad,
            'subtotal': subtotal
        })
    return render(request, 'web/carrito.html', {'carrito': productos, 'total': total})

def agregarProducto(request, producto_id):
    carrito = request.session.get('carrito', {})
    carrito[str(producto_id)] = carrito.get(str(producto_id), 0) + 1
    request.session['carrito'] = carrito
    return redirect('web:carrito')

def eliminarProducto(request, producto_id):
    carrito = request.session.get('carrito', {})
    carrito.pop(str(producto_id), None)
    request.session['carrito'] = carrito
    return redirect('web:carrito')

def vaciarCarrito(request):
    request.session['carrito'] = {}
    return redirect('web:carrito')

def aumentar_producto(request, producto_id):
    carrito = request.session.get('carrito', {})
    if str(producto_id) in carrito:
        carrito[str(producto_id)] += 1
    request.session['carrito'] = carrito
    return redirect('web:carrito')

def disminuir_producto(request, producto_id):
    carrito = request.session.get('carrito', {})
    if str(producto_id) in carrito and carrito[str(producto_id)] > 1:
        carrito[str(producto_id)] -= 1
    request.session['carrito'] = carrito
    return redirect('web:carrito')

# Vistas básicas
def index(request):
    productos = Producto.objects.all()
    return render(request, 'web/index.html', {'productos': productos})

def producto_categoria(request, categoria_id):
    productos = Producto.objects.filter(categoria_id=categoria_id)
    return render(request, 'web/index.html', {'productos': productos})

def productosPorNombre(request):
    nombre = request.GET.get('nombre', '')
    productos = Producto.objects.filter(nombre__icontains=nombre)
    return render(request, 'web/index.html', {'productos': productos})

def productoDetalle(request, producto_id):
    producto = get_object_or_404(Producto, pk=producto_id)
    return render(request, 'web/detalle.html', {'producto': producto})

# Registro y login
def registro(request):
    if request.method == 'POST':
        username = request.POST['username']
        password = request.POST['password']
        user = User.objects.create_user(username=username, password=password)
        Cliente.objects.create(usuario=user, provincia='', localidad='', codigo_postal='', direccion='')
        return redirect('web:login')
    return render(request, 'web/registro.html')

def login_view(request):
    if request.method == 'POST':
        username = request.POST['username']
        password = request.POST['password']
        user = authenticate(username=username, password=password)
        if user:
            login(request, user)
            return redirect('web:index')
    return render(request, 'web/login.html')

def logout_view(request):
    logout(request)
    return redirect('web:index')

@login_required
def actualizarUsuario(request):
    cliente = Cliente.objects.get(usuario=request.user)
    if request.method == 'POST':
        cliente.telefono = request.POST['telefono']
        cliente.provincia = request.POST['provincia']
        cliente.localidad = request.POST['localidad']
        cliente.codigo_postal = request.POST['codigo_postal']
        cliente.direccion = request.POST['direccion']
        cliente.save()
        return redirect('web:index')
    return render(request, 'web/actualizar_cliente.html', {'cliente': cliente})

@login_required
def registrarPedido(request):
    cliente = Cliente.objects.get(usuario=request.user)
    carrito = request.session.get('carrito', {})
    if not carrito:
        return redirect('web:carrito')

    with transaction.atomic():
        pedido = Pedido.objects.create(
            cliente=cliente,
            nro_pedido=str(uuid.uuid4())[:10],
            estado='0',
        )
        total = 0
        for producto_id, cantidad in carrito.items():
            producto = Producto.objects.get(id=producto_id)
            subtotal = producto.precio * cantidad
            PedidoDetalle.objects.create(
                pedido=pedido,
                producto=producto,
                cantidad=cantidad,
                subtotal=subtotal
            )
            total += subtotal
        pedido.monto_total = total
        pedido.save()
        request.session['carrito'] = {}
    return redirect('web:index')
```

---

## ✅ 5. Crear las plantillas HTML

Estructura sugerida en `web/templates/web/`:

* `index.html`
* `detalle.html`
* `carrito.html`
* `login.html`
* `registro.html`
* `actualizar_cliente.html`

Ejemplo mínimo `carrito.html`:

```html
{% for item in carrito %}
  <p>{{ item.producto.nombre }} - {{ item.cantidad }} x ${{ item.producto.precio }} = ${{ item.subtotal }}</p>
{% endfor %}
<p><strong>Total:</strong> ${{ total }}</p>
<a href="{% url 'web:vaciar_carrito' %}">Vaciar Carrito</a>
<a href="{% url 'web:registrar_pedido' %}">Confirmar Pedido</a>
```

---

## ✅ 6. Migraciones y superusuario

```bash
python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser
```

---

## ✅ 7. Cargar imágenes de productos

Cargar desde el panel de administración (`/admin`) y asegurarse de que `MEDIA_URL` funciona para mostrar imágenes.

---

¿Deseas que te prepare los archivos HTML con Bootstrap listos para usar o un archivo `.zip` del proyecto base?

