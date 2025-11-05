1 — Crear carpeta del proyecto

En tu explorador o en terminal:

# desde la carpeta donde quieras crear el proyecto
mkdir UIII_Pizzeria_0019
cd UIII_Pizzeria_0019

2 — Abrir VS Code sobre la carpeta

En la terminal (desde dentro de UIII_Pizzeria_0019):

code .


(O abre VS Code y selecciona File → Open Folder... y elige UIII_Pizzeria_0019.)

3 — Abrir terminal en VS Code

En VS Code: Terminal → New Terminal (se abrirá en la ruta del proyecto).

4 — Crear carpeta entorno virtual .venv desde terminal de VS Code

Recomendado crear dentro de la carpeta del proyecto:

# Windows (PowerShell)
python -m venv .venv

# Linux / macOS
python3 -m venv .venv


Esto crea .venv/ en UIII_Pizzeria_0019.

5 — Activar el entorno virtual
# Windows (PowerShell)
.venv\Scripts\Activate.ps1

# Windows (cmd)
.venv\Scripts\activate.bat

# Linux / macOS
source .venv/bin/activate


Después de activar verás (.venv) en tu prompt.

6 — Activar intérprete de Python en VS Code

En VS Code: Ctrl+Shift+P → escribe Python: Select Interpreter → selecciona el intérprete dentro de UIII_Pizzeria_0019/.venv (ej. .venv\Scripts\python.exe o .venv/bin/python).

7 — Instalar Django

Con entorno activado:

pip install django


(O pip install "django~=4.2" si quieres fijar versión.)

8 — Crear proyecto backend_Pizzeria sin duplicar carpeta

Dentro de UIII_Pizzeria_0019 (NO crear carpeta extra):

django-admin startproject backend_Pizzeria .


Nota: el punto final . crea el proyecto en la carpeta actual y evita duplicar carpeta.

9 — Ejecutar servidor en el puerto 8019

Con entorno activado y migraciones iniciales aplicadas (ver después), ejecutar:

python manage.py runserver 8019


Servidor en http://127.0.0.1:8019/.

10 — Copiar y pegar el link en el navegador

Abre: http://127.0.0.1:8019/ o http://localhost:8019/ en tu navegador.

11 — Crear aplicación app_Pizzeria

En terminal (dentro del proyecto):

python manage.py startapp app_Pizzeria

12 — Aquí el modelo models.py

Usa el código que ya pegaste. Reemplaza el contenido de app_Pizzeria/models.py por el que ya proporcionaste (lo incluiste en el enunciado). Ejemplo: pega exactamente ese bloque en app_Pizzeria/models.py.

(Recapitulando — tu models.py contiene Proveedores, Inventario, Menu con la ManyToMany entre Menu e Inventario.)

12.5 — Procedimiento para realizar migraciones

Después de agregar modelos y registrar la app en settings.py (item 25), ejecutar:

# crear migraciones
python manage.py makemigrations

# aplicar migraciones a la base de datos
python manage.py migrate

13 — Primero trabajamos con el MODELO: PROVEEDORES

Seguiremos implementando vistas y plantillas para Proveedores. Los modelos Inventario y Menu se dejan pendientes (puedes conservarlos en models.py).

14 — views.py de app_Pizzeria: funciones

Crea o reemplaza app_Pizzeria/views.py con el siguiente código (sin uso de forms.py, formularios en HTML puro):

from django.shortcuts import render, redirect, get_object_or_404
from .models import Proveedores

# Inicio del sistema
def inicio_pizzeria(request):
    return render(request, "inicio.html")

# Agregar proveedor (muestra formulario y procesa POST)
def agregar_proveedor(request):
    if request.method == "POST":
        nombre = request.POST.get("nombre_proveedor")
        telefono = request.POST.get("telefono_contacto")
        email = request.POST.get("email_contacto")
        direccion = request.POST.get("direccion")
        tipo = request.POST.get("tipo_producto")
        rfc = request.POST.get("rfc")
        # No validamos (según tu requerimiento)
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

# Ver proveedores (lista)
def ver_proveedores(request):
    proveedores = Proveedores.objects.all().order_by("nombre_proveedor")
    return render(request, "proveedores/ver_proveedores.html", {"proveedores": proveedores})

# Actualizar proveedor (muestra formulario con datos actuales)
def actualizar_proveedor(request, pk):
    proveedor = get_object_or_404(Proveedores, pk=pk)
    return render(request, "proveedores/actualizar_proveedores.html", {"proveedor": proveedor})

# Realizar actualización (procesa POST de actualización)
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
    # en caso de acceso GET, redirigimos al formulario
    return redirect("actualizar_proveedor", pk=pk)

# Borrar proveedor (confirmación y borrado)
def borrar_proveedor(request, pk):
    proveedor = get_object_or_404(Proveedores, pk=pk)
    if request.method == "POST":
        proveedor.delete()
        return redirect("ver_proveedores")
    return render(request, "proveedores/borrar_proveedores.html", {"proveedor": proveedor})


Nombres de vistas / rutas que usaré más abajo:

inicio_pizzeria

agregar_proveedor

ver_proveedores

actualizar_proveedor (muestra formulario)

realizar_actualizacion_proveedor (procesa POST)

borrar_proveedor

15 — Crear la carpeta templates dentro de app_Pizzeria

Estructura recomendada:

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

16 — Archivos HTML (plantillas)

A continuación vienen plantillas sencillas, modernas y con colores suaves. Pégalas en los archivos indicados.

app_Pizzeria/templates/base.html
<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>{% block title %}Pizzería - Sistema{% endblock %}</title>

  <!-- Bootstrap (CDN) -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
  <style>
    body { background-color: #f6f7fb; color: #333; }
    .footer { position: fixed; bottom: 0; left: 0; right: 0; background:#ffffff; padding: 0.5rem 1rem; border-top: 1px solid #eee; text-align:center; }
    .brand { font-weight:700; color:#2b6cb0; }
  </style>
  {% block extra_head %}{% endblock %}
</head>
<body>
  {% include "header.html" %}
  <div class="container-fluid">
    <div class="row">
      <div class="col-12">
        {% include "navbar.html" %}
      </div>
    </div>
    <main class="py-4">
      <div class="container">
        {% block content %}{% endblock %}
      </div>
    </main>
  </div>

  {% include "footer.html" %}

  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
  {% block extra_js %}{% endblock %}
</body>
</html>

app_Pizzeria/templates/header.html
<header class="py-3 mb-3" style="background: linear-gradient(90deg,#eef2ff,#f6fbff);">
  <div class="container d-flex align-items-center">
    <h1 class="h4 brand m-0">Sistema de Administración Pizzería</h1>
  </div>
</header>

app_Pizzeria/templates/navbar.html
<nav class="navbar navbar-expand-lg navbar-light bg-white shadow-sm mb-3">
  <div class="container">
    <a class="navbar-brand" href="{% url 'inicio' %}">Pizzería</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navMenu">
      <span class="navbar-toggler-icon"></span>
    </button>

    <div class="collapse navbar-collapse" id="navMenu">
      <ul class="navbar-nav me-auto mb-2 mb-lg-0">
        <li class="nav-item">
          <a class="nav-link" href="{% url 'inicio' %}">Inicio</a>
        </li>

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


Nota: pediste iconos en opciones principales — se han dejado simples (puedes añadir fontawesome si deseas).

app_Pizzeria/templates/footer.html
<footer class="footer">
  <div class="container">
    <div>© {{ now|date:"Y" }} - Creado por Estudiante Carlos Lozano, Cbtis 128</div>
  </div>
</footer>


Para que now esté disponible, en la vista de inicio_pizzeria o globalmente puedes activar django.template.context_processors.request (por defecto) y usar {% now "Y" %} si lo prefieres. Ejemplo simple: reemplazar {{ now|date:"Y" }} por {% now "Y" %}:

<div>© {% now "Y" %} - Creado por Estudiante Carlos Lozano, Cbtis 128</div>

app_Pizzeria/templates/inicio.html
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

21 — Crear subcarpeta proveedores dentro de templates (ya indicada arriba)

app_Pizzeria/templates/proveedores/

22 — Plantillas para proveedores (sin forms.py)
proveedores/agregar_proveedores.html
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

proveedores/ver_proveedores.html
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

proveedores/actualizar_proveedores.html
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

proveedores/borrar_proveedores.html
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

23 — No utilizar forms.py

Nada que cambiar: los formularios son HTML en plantillas; las vistas leen request.POST.

24 — urls.py en app_Pizzeria

Crea app_Pizzeria/urls.py con este contenido:

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

25 — Agregar app_Pizzeria en settings.py de backend_Pizzeria

En backend_Pizzeria/settings.py, dentro de INSTALLED_APPS añade:

INSTALLED_APPS = [
    # ... apps por defecto ...
    'app_Pizzeria',
]


Asegúrate también de tener TEMPLATES con APP_DIRS: True (por defecto Django lo trae) para que templates dentro de la app se detecten.

26 — Configurar urls.py de backend_Pizzeria para enlazar app_Pizzeria

En backend_Pizzeria/urls.py reemplaza o actualiza:

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    # ruta raíz delega a app_Pizzeria
    path('', include('app_Pizzeria.urls')),
]

27 — Registrar los modelos en admin.py y volver a migrar

En app_Pizzeria/admin.py:

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


Después:

python manage.py makemigrations
python manage.py migrate

27 (nota) — Sólo trabajar por ahora con “proveedores”

Aunque registramos Inventario y Menu en admin, las vistas y plantillas creadas están centradas en Proveedores. Puedes dejar las funcionalidades de Inventario y Menú pendientes hasta que quieras implementarlas.

28 — Estética: colores suaves y código sencillo

Las plantillas usan Bootstrap y fondo suave. Ajusta variables en <style> si quieres cambiar tonos.

29 — Al inicio crear estructura completa de carpetas y archivos

Resumen de estructura mínima que debes crear en UIII_Pizzeria_0019:

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

30 — Proyecto totalmente funcional (cómo probar)

Activar .venv.

python manage.py makemigrations → python manage.py migrate.

(Opcional) crear superusuario para admin: python manage.py createsuperuser.

python manage.py runserver 8019.

Abrir http://127.0.0.1:8019/ → debería mostrar la página de inicio.

Ir a http://127.0.0.1:8019/proveedores/ → ver la lista (vacía inicialmente).

Agregar proveedor → regresar a la lista y ver los botones Editar/Borrar.

31 — Finalmente ejecutar servidor en el puerto 8019

Ya cubierto arriba:

python manage.py runserver 8019

