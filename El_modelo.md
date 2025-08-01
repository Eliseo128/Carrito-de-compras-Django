# Modelos de Django

Este archivo define los modelos principales de la aplicación utilizando Django ORM.

```python
from django.db import models
from django.contrib.auth.models import User


# Modelo: Categoría
class Categoria(models.Model):
    nombre = models.CharField(max_length=200)
    fecha_registro = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.nombre


# Modelo: Producto
class Producto(models.Model):
    categoria = models.ForeignKey(Categoria, on_delete=models.RESTRICT)
    nombre = models.CharField(max_length=200)
    descripcion = models.TextField(null=True, blank=True)
    precio = models.DecimalField(max_digits=9, decimal_places=2)
    fecha_registro = models.DateTimeField(auto_now_add=True)
    imagen = models.ImageField(upload_to='productos', blank=True)

    def __str__(self):
        return self.nombre


# Modelo: Cliente
class Cliente(models.Model):
    usuario = models.OneToOneField(User, on_delete=models.RESTRICT)
    telefono = models.CharField(max_length=20, null=True, blank=True)
    provincia = models.CharField(max_length=50)
    localidad = models.CharField(max_length=50)
    codigo_postal = models.CharField(max_length=20)
    direccion = models.CharField(max_length=200)

    def __str__(self):
        return self.usuario.username


# Modelo: Pedido
class Pedido(models.Model):
    ESTADO_CHOICES = [
        ('0', 'Pendiente'),
        ('1', 'Pagado'),
        ('2', 'Enviado'),
    ]
    cliente = models.ForeignKey(Cliente, on_delete=models.RESTRICT)
    fecha_pedido = models.DateTimeField(auto_now_add=True)
    nro_pedido = models.CharField(max_length=20, unique=True)
    monto_total = models.DecimalField(max_digits=10, decimal_places=2, default=0.00)
    estado = models.CharField(max_length=20, choices=ESTADO_CHOICES, default='0')

    def __str__(self):
        return f'Pedido {self.id} - {self.cliente.usuario.username}'


# Modelo: Detalle del Pedido
class PedidoDetalle(models.Model):
    pedido = models.ForeignKey(Pedido, on_delete=models.RESTRICT, related_name='detalles')
    producto = models.ForeignKey(Producto, on_delete=models.RESTRICT)
    cantidad = models.PositiveIntegerField(default=1)
    subtotal = models.DecimalField(max_digits=10, decimal_places=2, default=0.00)

    def __str__(self):
        return self.producto.nombre
