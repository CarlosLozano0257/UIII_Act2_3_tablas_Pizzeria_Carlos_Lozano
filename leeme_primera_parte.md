# 1 - Crear carpeta del proyecto
# código de crear carpeta:
```
mkdir UIII_Pizzeria_0019
cd UIII_Pizzeria_0019
```

# 2 - Abrir VS Code sobre la carpeta
# código de abrir VS Code:
```
code .
```
# 3 - Abrir terminal en VS Code

# En VS Code: Terminal → New Terminal

# 4 - Crear carpeta del entorno virtual .venv
# código de crear entorno virtual:
```
python -m venv .venv
python3 -m venv .venv
```
# 5 - Activar el entorno virtual
# código de activar entorno virtual:
```
.venv\Scripts\Activate.ps1
.venv\Scripts\activate.bat
source .venv/bin/activate
```
# 6 - Activar intérprete de Python en VS Code
```
Presiona Ctrl+Shift+P → escribe Python: Select Interpreter → selecciona el intérprete dentro de .venv
```
# 7 - Instalar Django
# código de instalar Django:
```
pip install django
```
# 8 - Crear proyecto backend_Pizzeria sin duplicar carpeta
# código de crear proyecto:
```
django-admin startproject backend_Pizzeria .
```
# 9 - Ejecutar servidor en el puerto 8019
# código para correr servidor:
```
python manage.py runserver 8019
```
# 10 - Abrir en el navegador
```
Abre http://127.0.0.1:8019/
 o http://localhost:8019/
```
# 11 - Crear aplicación app_Pizzeria
# código de crear app:
```
python manage.py startapp app_Pizzeria
```
# 12 - Agregar modelos en models.py
# código de modelos:
```
from django.db import models

class Proveedores(models.Model):
    nombre_proveedor = models.CharField(max_length=100, unique=True)
    telefono_contacto = models.CharField(max_length=15, blank=True, null=True)
    email_contacto = models.EmailField(max_length=100, blank=True, null=True)
    direccion = models.CharField(max_length=255, blank=True, null=True)
    tipo_producto = models.CharField(max_length=100, blank=True, null=True)
    rfc = models.CharField(max_length=20, blank=True, null=True, unique=True)
    fecha_registro = models.DateField(auto_now_add=True)
    activo = models.BooleanField(default=True)

    def __str__(self):
        return self.nombre_proveedor

class Inventario(models.Model):
    nombre_articulo = models.CharField(max_length=100)
    stock = models.DecimalField(max_digits=10, decimal_places=2, default=0.0)
    unidad = models.CharField(max_length=20)
    fecha_ultima_compra = models.DateField(null=True, blank=True)
    stock_minimo = models.DecimalField(max_digits=10, decimal_places=2, default=0.0)
    costo_unitario = models.DecimalField(max_digits=10, decimal_places=2, default=0.0)
    proveedor = models.ForeignKey(
        Proveedores,
        on_delete=models.SET_NULL,
        null=True,
        blank=True,
        related_name="articulos_inventario",
        db_column="fk_id_proveedor"
    )

    def __str__(self):
        return f"{self.nombre_articulo} ({self.stock} {self.unidad})"

class Menu(models.Model):
    nombre = models.CharField(max_length=100)
    descripcion = models.TextField(blank=True, null=True)
    precio = models.DecimalField(max_digits=10, decimal_places=2)
    categoria = models.CharField(max_length=50)
    tamaño = models.CharField(max_length=50, blank=True, null=True)
    disponible = models.BooleanField(default=True)
    articulos = models.ManyToManyField(
        Inventario,
        related_name="productos_menu",
        blank=True
    )

    def __str__(self):
        return f"{self.nombre} - ${self.precio}"
```
# 12.5 - Realizar migraciones
# código de migraciones:
```
python manage.py makemigrations
python manage.py migrate
```
# 13 - Trabajar primero con el modelo Proveedores
# 14 - Crear funciones en views.py
# código de views:
```
from django.shortcuts import render, redirect, get_object_or_404
from .models import Proveedores

def inicio_pizzeria(request):
    return render(request, "inicio.html")

def agregar_proveedor(request):
    if request.method == "POST":
        nombre = request.POST.get("nombre_proveedor")
        telefono = request.POST.get("telefono_contacto")
        email = request.POST.get("email_contacto")
        direccion = request.POST.get("direccion")
        tipo = request.POST.get("tipo_producto")
        rfc = request.POST.get("rfc")
        Proveedores.objects.create(
            nombre_proveedor=nombre,
            telefono_contacto=telefono,
            email_contacto=email,
            direccion=direccion,
            tipo_producto=tipo,
            rfc=rfc
        )
        return redirect("ver_proveedores")
    return render(request, "proveedores/agregar_proveedores.html")

def ver_proveedores(request):
    proveedores = Proveedores.objects.all().order_by("nombre_proveedor")
    return render(request, "proveedores/ver_proveedores.html", {"proveedores": proveedores})

def actualizar_proveedor(request, pk):
    proveedor = get_object_or_404(Proveedores, pk=pk)
    return render(request, "proveedores/actualizar_proveedores.html", {"proveedor": proveedor})

def realizar_actualizacion_proveedor(request, pk):
    proveedor = get_object_or_404(Proveedores, pk=pk)
    if request.method == "POST":
        proveedor.nombre_proveedor = request.POST.get("nombre_proveedor")
        proveedor.telefono_contacto = request.POST.get("telefono_contacto")
        proveedor.email_contacto = request.POST.get("email_contacto")
        proveedor.direccion = request.POST.get("direccion")
        proveedor.tipo_producto = request.POST.get("tipo_producto")
        proveedor.rfc = request.POST.get("rfc")
        proveedor.save()
        return redirect("ver_proveedores")
    return redirect("actualizar_proveedor", pk=pk)

def borrar_proveedor(request, pk):
    proveedor = get_object_or_404(Proveedores, pk=pk)
    if request.method == "POST":
        proveedor.delete()
        return redirect("ver_proveedores")
    return render(request, "proveedores/borrar_proveedores.html", {"proveedor": proveedor})
```
# 15 - Crear carpeta templates dentro de app_Pizzeria

# Estructura:
```
app_Pizzeria/
  templates/
    base.html
    header.html
    navbar.html
    footer.html
    inicio.html
    proveedores/
      agregar_proveedores.html
      ver_proveedores.html
      actualizar_proveedores.html
      borrar_proveedores.html
```
# 16 - Crear archivos HTML base
# código de base.html:
```
<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>{% block title %}Pizzería - Sistema{% endblock %}</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
  <style>
    body { background-color: #f6f7fb; color: #333; }
    .footer { position: fixed; bottom: 0; left: 0; right: 0; background:#ffffff; padding: 0.5rem 1rem; border-top: 1px solid #eee; text-align:center; }
    .brand { font-weight:700; color:#2b6cb0; }
  </style>
</head>
<body>
  {% include "header.html" %}
  <div class="container-fluid">
    {% include "navbar.html" %}
    <main class="py-4">
      <div class="container">
        {% block content %}{% endblock %}
      </div>
    </main>
  </div>
  {% include "footer.html" %}
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>

# código de header.html:
<header class="py-3 mb-3" style="background: linear-gradient(90deg,#eef2ff,#f6fbff);">
  <div class="container d-flex align-items-center">
    <h1 class="h4 brand m-0">Sistema de Administración Pizzería</h1>
  </div>
</header>
```
# código de navbar.html:
```
<nav class="navbar navbar-expand-lg navbar-light bg-white shadow-sm mb-3">
  <div class="container">
    <a class="navbar-brand" href="{% url 'inicio' %}">Pizzería</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navMenu">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navMenu">
      <ul class="navbar-nav me-auto mb-2 mb-lg-0">
        <li class="nav-item"><a class="nav-link" href="{% url 'inicio' %}">Inicio</a></li>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">Proveedores</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="{% url 'agregar_proveedor' %}">Agregar proveedores</a></li>
            <li><a class="dropdown-item" href="{% url 'ver_proveedores' %}">Ver proveedores</a></li>
          </ul>
        </li>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">Inventario</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar artículo</a></li>
            <li><a class="dropdown-item" href="#">Ver artículo</a></li>
          </ul>
        </li>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">Menú</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar producto</a></li>
            <li><a class="dropdown-item" href="#">Ver producto</a></li>
          </ul>
        </li>
      </ul>
      <span class="navbar-text text-muted">Estudiante: Carlos Lozano</span>
    </div>
  </div>
</nav>
```
# código de footer.html:
```
<footer class="footer">
  <div class="container">
    <div>© {% now "Y" %} - Creado por Estudiante Carlos Lozano, Cbtis 128</div>
  </div>
</footer>
```
# código de inicio.html:
```
{% extends "base.html" %}
{% block title %}Inicio - Pizzería{% endblock %}
{% block content %}
  <div class="row align-items-center">
    <div class="col-md-6">
      <h2>Bienvenido al sistema de administración</h2>
      <p class="lead">Aquí puedes gestionar proveedores, inventario y el menú de la pizzería.</p>
    </div>
    <div class="col-md-6">
      <img src="https://images.unsplash.com/photo-1600891964599-f61ba0e24092?q=80&w=1200&auto=format&fit=crop&crop=faces" class="img-fluid rounded" alt="Pizzería">
    </div>
  </div>
{% endblock %}
```
# 17 - Crear subcarpeta proveedores dentro de templates
```
Crea app_Pizzeria/templates/proveedores/
```
# 18 - Crear plantillas para proveedores
# código de agregar_proveedores.html:
```
{% extends "base.html" %}
{% block content %}
  <h3>Agregar Proveedor</h3>
  <form method="post">
    {% csrf_token %}
    <div class="mb-3">
      <label class="form-label">Nombre</label>
      <input class="form-control" name="nombre_proveedor" required>
    </div>
    <div class="mb-3 row">
      <div class="col-md-4">
        <label class="form-label">Teléfono</label>
        <input class="form-control" name="telefono_contacto">
      </div>
      <div class="col-md-4">
        <label class="form-label">Email</label>
        <input class="form-control" name="email_contacto" type="email">
      </div>
      <div class="col-md-4">
        <label class="form-label">RFC</label>
        <input class="form-control" name="rfc">
      </div>
    </div>
    <div class="mb-3">
      <label class="form-label">Dirección</label>
      <input class="form-control" name="direccion">
    </div>
    <div class="mb-3">
      <label class="form-label">Tipo de producto</label>
      <input class="form-control" name="tipo_producto">
    </div>
    <button class="btn btn-primary">Agregar</button>
  </form>
{% endblock %}
```
# código de ver_proveedores.html:
```
{% extends "base.html" %}
{% block content %}
  <h3>Proveedores</h3>
  <a href="{% url 'agregar_proveedor' %}" class="btn btn-success mb-3">+ Nuevo Proveedor</a>
  <table class="table table-striped">
    <thead>
      <tr>
        <th>Nombre</th><th>Teléfono</th><th>Email</th><th>RFC</th><th>Activo</th><th>Acciones</th>
      </tr>
    </thead>
    <tbody>
      {% for p in proveedores %}
      <tr>
        <td>{{ p.nombre_proveedor }}</td>
        <td>{{ p.telefono_contacto }}</td>
        <td>{{ p.email_contacto }}</td>
        <td>{{ p.rfc }}</td>
        <td>{% if p.activo %}Sí{% else %}No{% endif %}</td>
        <td>
          <a class="btn btn-sm btn-primary" href="{% url 'actualizar_proveedor' p.pk %}">Editar</a>
          <a class="btn btn-sm btn-danger" href="{% url 'borrar_proveedor' p.pk %}">Borrar</a>
        </td>
      </tr>
      {% empty %}
      <tr><td colspan="6">No hay proveedores aún.</td></tr>
      {% endfor %}
    </tbody>
  </table>
{% endblock %}
```
# código de actualizar_proveedores.html:
```
{% extends "base.html" %}
{% block content %}
  <h3>Actualizar Proveedor</h3>
  <form method="post" action="{% url 'realizar_actualizacion_proveedor' proveedor.pk %}">
    {% csrf_token %}
    <div class="mb-3">
      <label class="form-label">Nombre</label>
      <input class="form-control" name="nombre_proveedor" value="{{ proveedor.nombre_proveedor }}">
    </div>
    <div class="mb-3 row">
      <div class="col-md-4">
        <label class="form-label">Teléfono</label>
        <input class="form-control" name="telefono_contacto" value="{{ proveedor.telefono_contacto }}">
      </div>
      <div class="col-md-4">
        <label class="form-label">Email</label>
        <input class="form-control" name="email_contacto" value="{{ proveedor.email_contacto }}">
      </div>
      <div class="col-md-4">
        <label class="form-label">RFC</label>
        <input class="form-control" name="rfc" value="{{ proveedor.rfc }}">
      </div>
    </div>
    <div class="mb-3">
      <label class="form-label">Dirección</label>
      <input class="form-control" name="direccion" value="{{ proveedor.direccion }}">
    </div>
    <div class="mb-3">
      <label class="form-label">Tipo de producto</label>
      <input class="form-control" name="tipo_producto" value="{{ proveedor.tipo_producto }}">
    </div>
    <button class="btn btn-primary">Guardar cambios</button>
    <a href="{% url 'ver_proveedores' %}" class="btn btn-secondary">Cancelar</a>
  </form>
{% endblock %}
```
# código de borrar_proveedores.html:
```
{% extends "base.html" %}
{% block content %}
  <h3>Eliminar Proveedor</h3>
  <p>¿Eliminar proveedor <strong>{{ proveedor.nombre_proveedor }}</strong>?</p>
  <form method="post">
    {% csrf_token %}
    <button type="submit" class="btn btn-danger">Sí, eliminar</button>
    <a href="{% url 'ver_proveedores' %}" class="btn btn-secondary">No, cancelar</a>
  </form>
{% endblock %}
```
# 19 - No utilizar forms.py
# 20 - Crear archivo urls.py en app_Pizzeria
# código de urls app:
```
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_pizzeria, name='inicio'),
    path('proveedores/agregar/', views.agregar_proveedor, name='agregar_proveedor'),
    path('proveedores/', views.ver_proveedores, name='ver_proveedores'),
    path('proveedores/editar/<int:pk>/', views.actualizar_proveedor, name='actualizar_proveedor'),
    path('proveedores/editar/realizar/<int:pk>/', views.realizar_actualizacion_proveedor, name='realizar_actualizacion_proveedor'),
    path('proveedores/borrar/<int:pk>/', views.borrar_proveedor, name='borrar_proveedor'),
]
```
# 21 - Agregar app_Pizzeria en settings.py de backend_Pizzeria
```
Abre backend_Pizzeria/settings.py y dentro de INSTALLED_APPS añade 'app_Pizzeria'
```
# 22 - Configurar urls.py de backend_Pizzeria para enlazar con app_Pizzeria
# código de urls proyecto:
```
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Pizzeria.urls')),
]
```
# 23 - Registrar modelos en admin.py
# código de admin.py:
```
from django.contrib import admin
from .models import Proveedores, Inventario, Menu

@admin.register(Proveedores)
class ProveedoresAdmin(admin.ModelAdmin):
    list_display = ('id', 'nombre_proveedor', 'telefono_contacto', 'email_contacto', 'rfc', 'activo', 'fecha_registro')
    search_fields = ('nombre_proveedor', 'rfc', 'tipo_producto')

@admin.register(Inventario)
class InventarioAdmin(admin.ModelAdmin):
    list_display = ('id', 'nombre_articulo', 'stock', 'unidad', 'proveedor')
    search_fields = ('nombre_articulo',)

@admin.register(Menu)
class MenuAdmin(admin.ModelAdmin):
    list_display = ('id', 'nombre', 'precio', 'categoria', 'disponible')
    search_fields = ('nombre',)
``` 
# 24 - Volver a realizar migraciones después de registrar modelos
# código de migraciones (repetir si necesario):
```
python manage.py makemigrations
python manage.py migrate
```
# 25 - Trabajar por ahora solo con Proveedores
# 26 - Estética: colores suaves y diseño sencillo

# Puedes ajustar los estilos en base.html si deseas cambiar colores o tipografías

# 27 - Estructura de carpetas recomendada
```
UIII_Pizzeria_0019/
  .venv/
  backend_Pizzeria/
    settings.py
    urls.py
    wsgi.py
    asgi.py
  app_Pizzeria/
    migrations/
    templates/
      base.html
      header.html
      navbar.html
      footer.html
      inicio.html
      proveedores/
        agregar_proveedores.html
        ver_proveedores.html
        actualizar_proveedores.html
        borrar_proveedores.html
    models.py
    views.py
    urls.py
    admin.py
    apps.py
  manage.py
```
# 28 - Proyecto funcional: pasos para probar
# código para probar:
```
python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver 8019
```

# Luego abre http://127.0.0.1:8019/ y prueba las rutas de proveedores
