# Modelos en Django

Hasta este punto hemos configurado nuestro entorno de trabajo, hemos visto
como hacer el diseño de templates, hemos visto el uso de vistas y su enlace
a un URL para poder desplegar templates cuando se visita cierto URL, etc.

Hasta este punto hemos abarcado dos elementos del patrón MVT, los cuales son 
las vista y las templates, pero nos falta uno de los elementos más importantes de este patrón...
el encargado de manejar la información almacenada para el funcionamiento correcto de nuestro
sistema.

## ¿Qué son los modelos en Django?
De acuerdo con la documentación de Django, un modelos es la fuente de información definitiva acerca de tus datos.
Contiene los campos esenciales y comportamientos de la información que se está almacenando. Generalmente, cada modelo
es referenciado a una tabla en la base de datos.

Lo básico para saber al hacer uso de modelos:
- Cada modelo es una clase de Python que hereda de la clase [django.db.models.Model](https://docs.djangoproject.com/en/4.0/ref/models/instances/#django.db.models.Model).
- Cada atributo de el modelo representa un campo de la tabla en la base de datos.

Ejemplo de un modelo:

Este modelo de ejemplo define una Persona, el cual tiene
como campos __first_name__ y __last_name__.


```
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
```

__first_name__ y __last_name__ son campos de este modelo.
Cada campo es especificado por un atributo de la clase, y cada atributo representa una columna de la base de datos.

El modelo __Person__ mostrado arriba se crearía como una tabla de base de datos de la siguiente manera:

```
CREATE TABLE myapp_person (
    "id" serial NOT NULL PRIMARY KEY,
    "first_name" varchar(30) NOT NULL,
    "last_name" varchar(30) NOT NULL
);
```

Notas:
- El nombre de la tabla, __myapp_person__, se deriva automáticamente de algunos metadatos del modelo, pero puede ser sobreescrito. [Obtener más información](https://docs.djangoproject.com/en/4.0/ref/models/options/#table-names)

- El campo id es agregado automáticamente, pero su comportamiento puede ser sobreescrito. [Obtener más información](https://docs.djangoproject.com/en/4.0/topics/db/models/#automatic-primary-key-fields)

- Cada que agreguemos o modifiquemos un modelo en alguna app de nuestro proyecto, debemos correr el siguiente comando para que Django detecte los nuevos modelos o los cambios que se han hecho:

```
Linux
python3 manage.py makemigrations
```

```
Windows
python manage.py makemigrations
```

- Para que se vean reflejados los cambios de nuestros modelos en la base de datos, deberás ejecutar el siguiente comando para que el ORM se encargue de hacer los respectivos cambios en la base de datos:

```
Linux
python3 manage.py migrate
```

```
Windows
python manage.py migrate
```

# Campos de los modelos

La parte más importante y requerida de un modelo es la lista de campos de la base de datos que lo d efine. Lo campos son especificados por los atributos de la clase, pero debemos tener cuidado de escoger nombres de atributos que no provoquen conflictos con la API de los modelos como __clean__, __save__, o __delete__.

Ejemplo:

```
from django.db import models

class Musician(models.Model):
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)
    instrument = models.CharField(max_length=100)

class Album(models.Model):
    artist = models.ForeignKey(Musician, on_delete=models.CASCADE)
    name = models.CharField(max_length=100)
    release_date = models.DateField()
    num_stars = models.IntegerField()
```

# Tipos de campos

Cada campo en tu modelo debería ser una instancia apropiada de la clase Field. Django utiliza los tipos de clase Field para determinar algunas cosas como:
- El tipo de columna, la cual dictará en la base de datos que tipo de información almacenar (__INTEGER__, __VARCAHR__, __TEXT__)
- El widget HTML por defecto a utilizar cuando sea necesario renderizar un campo de formulario para este atributo.
- Los requerimientos mínimos de validación.

# Opciones de los campos

Cada campo toma un cierto conjunto de argumentos específicos correspondientes a cada tipo de campo. Por ejemplo, __CharField__ requiere un argumento  __max__length__, el cual específica el tamaño del campo __VARCHAR__ en la base de datos.

También existe un conjunto de argumentos comunes disponibles para todos los tipos de campos, algunos de estos argumentos son:

- __null__
Si su valor es __True__, Django va a almacenar valores vacíos como __NULL__. El valor por defecto es __False__.

- __blank__
Si su valor es __True__, el campo es permitido para almacenar campos vacíos. El valor por defecto es __False__.

Se debe de tener en cuenta que este argumento es diferente a __null__. __null__ únicamente relacionado con nuestra base de datos, mientras que __blank__ está relacionado con validaciones. Si el campo tiene blank=True, el formulario permitirá la entrada de un campo vacío. Si el campo tiene blank=False, el campo será requerido.

- __choices__

Una secuencia de tuplas con dos valores para utilizar como opciones para este campo. Si este es proporcionado, el valor por defecto en el widget del formulario será un __select box__ en vez de un __text field__ y va a limitar las opciones a las que se han dado.

La lista de opciones luce de la siguiente manera:

```
YEAR_IN_SCHOOL_CHOICES = [
    ('FR', 'Freshman'),
    ('SO', 'Sophomore'),
    ('JR', 'Junior'),
    ('SR', 'Senior'),
    ('GR', 'Graduate'),
]
```

El primer elemento de cada tupla es el valor que será almacenado en la base de datos. El segundo elemento de la tupla es el que será mostrado en el widget en el formulario.

```
from django.db import models

class Person(models.Model):
    SHIRT_SIZES = (
        ('S', 'Small'),
        ('M', 'Medium'),
        ('L', 'Large'),
    )
    name = models.CharField(max_length=60)
    shirt_size = models.CharField(max_length=1, choices=SHIRT_SIZES)
```

- __default__ 
El valor por defecto de un campo. Este puede ser un objecto invocable. Si es un objeto invocable, este será llamado cada ve que un nuevo objeto sea creado.

- __primary_key__

Si su valor es __True__, este campo se convertirá en la llave primaria de su modelo.

Si ninguno de los atributos del modelo son específicados como __primary_key__, Django automáticamente agregará un __IntegerField__ al modelo con nombre __id__, el cual se encargará de ser la llave primaria de ese modelo.

```
from django.db import models

class Fruit(models.Model):
    name = models.CharField(max_length=100, primary_key=True)
```

- __unique__

Si su valor es __True___, este campo deberá ser único en la tabla.