# Views (Vistas) en Django

Una View es un lugar donde ponemos la "lógica" de nuestra aplicación, es decir, la conexión entre el cliente y el servidor.
De acuerdo con la documentación oficial de Django, una vista es un identificador que toma una solicitud y devuelve una respuesta. Esto puede ser más que una simple función, y Django proporciona un ejemplo de algunas clases que se pueden usar como vistas. Estos le permiten estructurar sus vistas y reutilizar el código aprovechando la herencia y los mixins.

Una vista puede ser una simple función de python tal como la que se muestra a continuación:

```
from django.http import HttpResponse

def my_view(request):
    if request.method == 'GET':
        # <view logic>
        return HttpResponse('result')
```

Django proporciona Vistas basadas en clases que se adaptarán a una amplia gama de aplicaciones. Todas las vistas heredan de la clase View, que controla la vinculación de la vista a las direcciones URL, la distribución de métodos HTTP y otras características comunes. RedirectView proporciona una redirección HTTP y TemplateView extiende la clase base para que también represente una plantilla.

Existen diferentes Vistas basadas en clases y cada una tiene su funcionalidad. Algunas de estas son:
- [ListView](https://docs.djangoproject.com/en/4.0/ref/class-based-views/generic-display/#listview)
- [DetailView](https://docs.djangoproject.com/en/4.0/ref/class-based-views/generic-display/#detailview)
- [CreateView](https://docs.djangoproject.com/en/4.0/ref/class-based-views/generic-editing/#createview)
- [UpdateView](https://docs.djangoproject.com/en/4.0/ref/class-based-views/generic-editing/#updateview)
- [DeleteView](https://docs.djangoproject.com/en/4.0/ref/class-based-views/generic-editing/#deleteview)
- [FormView](https://docs.djangoproject.com/en/4.0/ref/class-based-views/generic-editing/#formview)

A continuación se mostrará un ejemplo de una vista basada en clases:
```
from django.views.generic import ListView
from books.models import Publisher

class PublisherListView(ListView):
    # Indica que template se va a renderizar
    template_name = 'books/acme_list.html'

    # Modelo del cual se va a extraer la información
    model = Publisher
    
    # Da nombre al contexto que se usará en la template para 
    # extraer los datos de los publicadores
    context_object_name = 'my_favorite_publishers'
```
Template __acme_list.html__
```
{% extends "base.html" %}

{% block content %}
    <h2>Publishers</h2>
    <ul>
        {% for publisher in my_favorite_publishers %}
            <li>{{ publisher.name }}</li>
        {% endfor %}
    </ul>
{% endblock %}
```

Las vistas que hagamos para nuestras apps deben colocarse en el archivo correspondiente archivo __views.py__ que contiene la app a la que va a pertenecer esa vista.

Para que una vista sea funcional debemos "engancharla" a una URL para que al momento de que se visite ese enlace nuestra Vista sea "llamada".

Para más información acerca de las vistas basadas en clases visita la [documentación oficial](https://docs.djangoproject.com/en/4.0/ref/class-based-views/).