### Django Generic Views

#### Django Generic Views

-	Hardcoded view - "cats" example. This code is not re-usable.
-	`dj4e-samples/gview/urls.py:`
  ```
  from django.urls import path
  from . import views
  from django.views.generic import TemplateView

  app_name = 'gview'

  # Note use of plural for list view and singular for detail view
  urlpatterns = [
      path('', TemplateView.as_view(template_name='gview/main.html')),
      path('cats', views.CatListView.as_view(), name='cats'),
      path('cat/<int:pk_from_url>', views.CatDetailView.as_view(), name='cat'),
      path('dogs', views.DogListView.as_view(), name='dogs'),
      path('dog/<int:pk>', views.DogDetailView.as_view(), name='dog'),
      path('horses', views.HorseListView.as_view(), name='horses'),
      path('horse/<int:pk>', views.HorseDetailView.as_view(), name='horse'),
      path('cars', views.CarListView.as_view(), name='cars'),
      path('car/<int:pk>', views.CarDetailView.as_view(), name='car'),
      path('wacky', views.WackyEquinesView.as_view(), name='whatever'),
  ]
  ```
-	`dj4e-samples/gview/views.py:`
  ```
  from gview.models import Cat, Dog, Horse, Car

  from django.views import View
  from django.shortcuts import render

  # Create your views here.

  class CatListView(View):
      def get(self, request) :
          stuff = Cat.objects.all()
          cntx = { 'cat_list': stuff }
          return render(request, 'gview/cat_list.html', cntx)

  class CatDetailView(View):
      def get(self, request, pk_from_url) :
          obj = Cat.objects.get(pk=pk_from_url)
          cntx = { 'cat': obj }
          return render(request, 'gview/cat_detail.html', cntx)
  ```
-	Template to create list view
-	`dj4e-samples/gview/templates/gview/cat_list.html`
  ```
  <h1>Cat List</h1>
  <p>
  {% if cat_list %}
  <ul>
    {% for cat in cat_list %}
      <li>
        <a href="{% url 'gview:cat' cat.id %}">{{ cat.name }}</a>
      </li>
    {% endfor %}
  </ul>
  {% else %}
    <p>There are no cats in the database.</p>
  {% endif %}
  </p>
  ```
-	Script to pre-load the model data for this example (“cats”)
-	`/dj4e-samples/scripts/gview_load.py`
  ```
  # In linux, mac terminal, WSL, or PythonAnywhere bash do this

  # python3 manage.py runscript gview_load

  # Otherwise make a super user and add the records
  # or run the shell and paste the commands.

  # https://django-extensions.readthedocs.io/en/latest/runscript.html

  from gview.models import Cat, Dog, Horse, Car

  def run() :
      Cat.objects.all().delete()
      Dog.objects.all().delete()
      Horse.objects.all().delete()
      Car.objects.all().delete()

      x = Cat(name='Sophie')
      x.save()
      x = Cat(name='Frankie')
      x.save()

      x = Dog(name='Shelby')
      x.save()
      x = Dog(name='Luna')
      x.save()

      x = Horse(name='Penny')
      x.save()
      x = Horse(name='Bravo')
      x.save()

      x = Car(name='SakaiCar')
      x.save()
      x = Car(name='Subaru')
      x.save()

      print('gview data loaded')
  ```
-	Re-usable code explained. The following uses the Don’t repeat yourself – DRY – coding principle. In a later example, we will further simplify this code using the Django generic views.
-	`dj4e-samples/gview/views.py:`
  ```
  from gview.models import Cat, Dog, Horse, Car

  from django.views import View
  from django.shortcuts import render

  # Create your views here.
  class DogListView(View):
      model = Dog
      def get(self, request) :
      # model is the “Dog” data model – for access to the “Dog” database records
      # modelname is a string for the name of the model – in this case, “Dog”
          modelname = self.model._meta.verbose_name.title().lower()
          stuff = self.model.objects.all()
          cntx = { modelname+'_list': stuff }
          return render(request, 'gview/'+modelname+'_list.html', cntx)

  class DogDetailView(View):
      model = Dog
      def get(self, request, pk) :
          modelname = self.model._meta.verbose_name.title().lower()
          obj = self.model.objects.get(pk=pk)
          cntx = { modelname : obj }
          return render(request, 'gview/'+modelname+'_detail.html', cntx)
  ```
-	Django has built several generic view classes including one named `ListView` that already defines the "get" method that we used in the DoglistView class above – Django has created a built-in generic list view. Therefore, the developer can import the necessary code rather than write their own "get" function. The following example uses a Django generic view and greatly simplifies the `views.py` code.
-	`dj4e-samples/gview/views.py:`
  ```
  from gview.models import Cat, Dog, Horse, Car

  from django.views import View
  from django.shortcuts import render

  # Create your views here.
  # Lets save time and use the built-in generics
  # https://docs.djangoproject.com/en/3.0/topics/class-based-views/generic-display/

  from django.views import generic

  class HorseListView(generic.ListView):
      model = Horse

  class HorseDetailView(generic.DetailView):
      model = Horse
  ```
  -	the `model` in the `model = Horse` line of python code above, is effectively a reserved word.
    -	If you write this line of code
      `model = modelName`
      and then create a class that extends Django’s generic.ListView, i.e,
      `class modelNameListView(generic.ListView)`
    -	Then you will successfully invoke Django’s generic List View, but you still have to write the template that loops through all the objects in your model.
      -	And that template has to be named “modelName_list.html”
        - e.g., `Horse_list.html`
    -	Similarly, you can extend Django's `generic.DetailView`, but you still have to write a template that is named: `modelName_detail.html`
      -	e.g., `Horse_detail.html`
 
