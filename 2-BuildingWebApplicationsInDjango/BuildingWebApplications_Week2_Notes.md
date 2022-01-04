##### **Attribution:**  
- *This document contains my notes from the "Django for Everybody (DJ4E)" MOOC series created by Dr. Charles Severance, University of Michigan. The materials in the DJ4E courses are available under a Creative Commons License to allow for teachers to make use of these materials in their own courses.*  
- *The following notes contain images based on the DJ4E presentation slides, also created by Dr. Severance as course resources. The notes and images in this document were assembled by me as a learning aid to supplement the course content and as a reference for completing the course assignments and quizzes.*

*My notes represent my interpretation of the DJ4E course videos, transcripts and presentation slides.* ***Any content errors or omissions in these notes should be attributed to me, as the note-taker.***



# Django for Everybody

## Course #2: Building Web Applications in Django

### Week 2 - Django views

#### URL Routing in Django

-	`views.py` will take one of two actions:
  - redirect the user to another screen/page
  - produce the HTML for the user's requested webpage


-	Django urls use a standard format as seen in the following example: `https://samples.dj4e.com/appName/viewName/42`
- where,
  -	domain name: `samples.dj4e.com`
    - The domain name can also be thought of as the Django project which consists of one or more applications.
  -	application name: `appName`
  -	view name: `viewName`
  -	URL path parameter: `42`
  -	the application name, view name, and the parameter can be named any string – it is their position and separation by forward slashes “/”, that is parsed
  -	Optionally, the parameter can be expressed as a key-value pair, and a question mark is substituted for the last forward slash: `https://samples.dj4e.com/appName/viewName?part=42`
    - key-value parameter: `part=42`


-	`urls.py`
  - behaves as a router to the appropriate code/page construction
  - defines how urls will be parsed and handled
  - more specifically, it parses the user requested URL, and based on the URL, it determines which view code (usually in `views.py`) should be used to generate the response to the user
  - each application will have its own `urls.py` file
  - each application will have its own `views.py` file
  - `urls.py` and `views.py` are reserved file names, you cannot name them something else -- if you do, Django won't find the routing and view construction instructions defined in them.


-	`urls.py` uses three different patterns for routing parsed URL's to view code:
  1.	route the URL pattern to a predefined class (in Django)
  2.	route the URL to a function in `views.py` (that is defined by the application developer) and that takes the HTTP request as a parameter (to the function) and returns a response
      - The request itself is an object. For example, this request becomes an object:
        - `GET http://data.pr4e.org/page2.htm`
      - This object captures everything associated with this simple command: all of the data, the parameters, the URL, whether it was a secure request or not, what host it came from, what IP address it came from, and more.
  3.	route the URL pattern to a class in `views.py` (that is defined by the application developer) and that takes the HTTP request as a parameter (to a function) and returns a response


-	inside the `urls.py` file is a global variable, `urlpatterns`
  - `urlpatterns` is a python list variable
  - it maps parsed view patterns from the user request to specific view instructions
  - as stated above, the view instruction may be a pre-defined class from Django, a class defined by the app developer inside `views.py`, or a function defined by the app developer inside `views.py`

1.	`urls.py` can call (or route to) a Django predefined class
  - format (in `urls.py`):
    ```
    from django.views.generic import TemplateView
    …
    path(' ', TemplateView.as_view(template_name = 'views.main.html')),
    ```
    - This saves you from writing code if all you want to do is take a template out of the templates folder and return it. By calling TemplateView, you don't have to write your own code in `views.py`.

2.	`urls.py` can call (or route to) a function defined by the app developer inside `views.py`:
  - format: `path('funky', views.funky)`
  - In the above example, this `path` method says that when the user request contains the view name of "funky", then execute the "funky" function which is defined in the `views.py` file

3. `urls.py` can call (or route to) a class defined by the app developer inside `views.py`:
  - format: `path('main', views.MainView.as_view())`
  -	In the above example, this `path` method says that when the user request contains the view name of `main`, then reference the `MainView` class, and execute the `as_view()` method/function, all of which is defined in the application’s `views.py` file
  -	In the `urls.py` file, don’t forget to import the necessary library so that the `path` method is recognized, and the `views.py` file is found:
  ```
  from django.urls import path
  from . import views
  ```
  - this finds the `views.py` file in the same directory that `urls.py` resides in
  - Take note of other required imports in your `urls.py` file


#### Django Views

-	If you want to send the user a simple, static page/view that is predefined, then you do it from the `urls.py` file using the `TemplateView` function:
  - You must import the `TemplateView` class and its methods that have been built in Django
      - `from django.urls import path`
  - In the `urls.py` file, call the following class and method:
      - `path('', TemplateView.as_view(template_name='appName/main.html')),`
        - where `appName` is the name of your app
  - Note that you are already in the `appName` folder that contains `urls.py`, the method assumes that you have moved into the `templates` subfolder, and from `appName/templates` you are specifying `appName/fileName.html`
  - the full path for the file that you want to send to the user is thus  `/appName/templates/appName/fileName.html`
  - We use the redundant `/appName/templates/appName/fileName.html`, because the filename could be a name for which multiple apps in your project might have a version of - like `main.html`. Because these names are global and repeated, we repeat the appName twice in the path.
-	If your response is not a predefined class and method like `TemplateView.as_view()`, then you have to write python code inside of `views.py` using variables (or attributes) in the user request.
  - The user request is an object of the class `HttpRequest`
  - The `HttpRequest` class is pre-defined in Django
  - The variables that you will use in your python code are attributes of the class `HttpRequest`
  - For example, the `HttpRequest.scheme` attribute is usually the http or https signifier that begins the user request.
  - There are many attributes to the class that are documented (look in <https://docs.djangoproject.com/en/3.0/ref>  for the documentation general page, and at <https://docs.djangoproject.com/en/3.0/ref/request-response/> for the Request and Response object documentation.)
  - the actual url string that the user sends to the Django server is the `HttpRequest.body` attribute
  - Other attributes are defined later in the request-response cycle
  - When we return a response to the user, we are sending an object of the class `HttpResponse`
-	In our application's `urls.py` file, if we have a path statement like `path('viewName', views.viewName),` then when we get a user request for `https://samples.dj4e.com/appName/viewName/42`, the following happens:
  - all the user request data is placed in an object (named "request"?)
  - pass the `request` object to the developer-defined function named `viewName` in the `views.py` file and execute the function
  - so, in summary, `viewName` is just a function that the developer defines inside of the `views.py` file.
-	When an application uses a developer defined function, and when a page is requested by a client browser,
  - Django creates an `HttpRequest` object. This object contains meta data about the request.
  - Django loads the appropriate view and passes the `HttpRequest` object as the first argument (named “request”?) to the view function.
  - Each view is responsible for returning an `HttpResponse` object.
  - The API’s for `HttpRequest` and `HttpResponse` objects are defined in the `django.http` module (library)
-	if we want to pass a parameter to the function, then we append a question mark and key-value pair at the end of the user requested URL
  - the url will look something like:
    - `https://samples.dj4e.com/appName/viewName?guess=42`
  - `guess=42` is the key value pair
  - this key-value pair is stored in the `HttpRequest` object
  - The value can be retrieved by using the object's `GET` object in our `views.py` function named `viewName`, like so:
      - `request.GET['guess']` where `request` is the name of the `HttpRequest` object
      - This will return the value of “42”
      - Note: `GET` is an object not a method. More specifically, `HttpRequest.GET` is a dictionary-like object containing all given HTTP GET parameters.

#### Inside Django Views and HTML Escaping in Django

-	Security Problem: accepting data without restriction from a user, and concatenating it into the web server's response, allows an unauthorized user to insert code into their data that the server will execute.
  - The following code is a dangerous python function in Django because the user can insert their data without any checks into the server's behavior/response:
  ```
      def danger(request) :
        response = """<html><body>
        <p>Your guess was """+request.GET['guess']+"""</p>
        </body></html>"""
        return HttpResponse(response)
  ```
  - Cross-site scripting: malicious exploit where a user runs code on a legitimate host server in order to send data to another server/rogue site.
  - This code can take the form of JavaScript and will cause the server to perform actions that are not intended and will likely be malicious in nature - e.g., stealing server data or redirecting legitimate users of the site.
  - The following python code imports `django.utils.html` to access the `escape` function. It automatically processes and filters any user-provided data and transforms certain character sequences into harmless html entities, thus inactivating any attempted code insertion.
  - The following code safely accepts user data input with the `escape` function:
  ```
      from django.utils.html import escape
      def game(request) :
          response = """<html><body>
          <p>Your guess was """+escape(request.GET['guess'])+"""</p>
          </body></html>"""
          return HttpResponse(response)
  ```

-	Function-based view. To use a function that parses a parameter from the user-requested URL, do the following:
  - in `urls.py`, in the url patterns global list variable, set a condition to look for (in this case) an integer and place that value in a parameter (here, named "guess"):
      `path('rest/<int:guess>', views.rest),`
  - in `views.py`:
      ```
      from django.http import HttpResponse
      from django.utils.html import escape
      …
      def rest(request, guess) :
          response = """<html><body>
          <p>Your guess was """+escape(guess)+"""</p>
          </body></html>"""
          return HttpResponse(response)
      ```
        - Note: Instead of passing the “guess” parameter with the integer value of 42 into the function, we could also use the `request.GET` attribute of the `HttpRequest` object, and read and parse the URL in order to find the “42” value.
            - Recall that `GET` is a defined attribute of the `HttpRequest` class. It is NOT a method. It takes the form of a dictionary object containing all the parameters (i.e., values) of the user’s HTTP request.


-	Class-based view. Up to this point we have used functions to parse the user supplied URL for views, but the app developer can alternatively define their own classes to create views.
  - Guidelines for defining a class-based view:
      - A developer-defined class-based view will inherit from Django’s base `View` class.
      -	Documentation on the Django base `View` class can be found at: <https://docs.djangoproject.com/en/3.2/ref/class-based-views/base/#django.views.generic.base.View>
      -	The developer must define a function in their view named `get`, and that `get` function passes the `self` and `request` objects as the first and second parameters (i.e., arguments). Later, we’ll see that multiple parameters can also be passed in the `get` function.
      -	Because the app developer is inheriting the Django-defined `View` class, and the `View` class has a predefined list of acceptable HTTP method names, the app developer must choose the following method names:
        - http_method_names = ['get', 'post', 'put', 'patch', 'delete', 'head', 'options', 'trace']
  -	in urls.py:
  ```
      from django.urls import path
      from . import views
      …
      path('main', views.MainView.as_view()),
      ```
  -	in views.py:
  ```
          from django.http import HttpResponse
          from django.utils.html import escape
          from django.views import View
          …
          class MainView(View) :
              def get(self, request):
                  response = """<html><body><p>Hello world MainView in HTML</p>
                  <p>This sample code is available at
                  <a href="https://github.com/csev/dj4e-samples">
                  https://github.com/csev/dj4e-samples</a></p>
                  </body></html>"""
                  return HttpResponse(response)
  ```
  -	Passing a parameter into a class based view.
      - User requests URL: https://samples.dj4e.com/views/remain/abc123
      -	Django/python pulls the string after "remain" in the URL using a class-defined method, and uses the string in its response.
      -	in urls.py:
          `path('remain/<slug:guess>', views.RestMainView.as_view()),`
      -	in views.py (slug is a python string type with lowercase alphabet characters, numbers and dashes):
      ```
              from django.http import HttpResponse
              from django.utils.html import escape
              from django.views import View
              …
              class RestMainView(View) :
                  def get(self, request, guess):
                      print("We got a slug from the URL",guess);
                      response = """<html><body>
                      <p>Your guess was """+escape(guess)+"""</p>
                      </body></html>"""
                      return HttpResponse(response)
      ```
  -	a `302 HTTP Status Code` is a response that, based on the user requested URL, the server redirects the user to some other URL. It was originally used to find servers when their domain name was changed. In the model-view-controller architecture, the redirect is part of the controller phase.
  -	Function-based view to redirect a user to another URL.
      - The user requests the url: <https://samples.dj4e.com/views/bounce>
      -	When the server receives the request for the "bounce" view, it redirects the user to another url.
      -	in `urls.py`:
        `path('bounce', views.bounce),`
      -	in `views.py`:
        ```
            from django.http import HttpResponseRedirect
            from django.views import View
            def bounce(request) :
                return HttpResponseRedirect('https://www.dj4e.com/simple.htm')
        ```


#### Using Templates in Django

-	Views are the python code that the developer writes. Views code generates HTML that is sent in a response to the user. This user interface can be generated through view functions and classes that perform string concatenation, or through templates.
-	Errors are likely by the developer when they attempt to generate an HTML response to a user request by using classes or functions to concatenate text. Templates are a much cleaner solution.
-	The Django Template Language software has a render engine that takes data and inserts it into a template. The data is sometimes referred to as the render data or as the context. The template is pre-defined HTML code with designated hot spots where the data should be inserted. The data can be a python dictionary that has been retrieved from the model, i.e., application database.
  - For more information on the Django `render()` function, which can be found in the `django.shortcuts` library, go to:
  <https://docs.djangoproject.com/en/3.2/topics/http/shortcuts/>
  - From the shortcuts documentation,
      - Note that the “request” object and the template_name are required arguments to the `render()` function and must be placed in the first and second argument positions.
      - A context dictionary value is optional, but if used, it must be in the third position.
      - `render()` format:
        - `render(request, template_name, context=None, content_type=None, status=None, using=None)`
    - `render()` combines a given template with a given context dictionary and returns an `HttpResponse` object with that rendered text.


-	The render template designates hot spots with double curly braces: `{{ variableName }}` The render engine will transcribe the appropriate render data into hot spots designated with the double curly brace format.

- Using a template with a class-based view
  - User requests URL: https://samples.dj4e.com/tmpl/game/200
  - in urls.py:
 			`path('game/<slug:guess>', views.GameView.as_view()),`
  - in views.py:
```
      from django.shortcuts import render
      from django.views import View
      class GameView(View) :
          def get(self, request, guess) :
              x = {'guess' : int(guess) }
              return render(request, 'tmpl/cond.html', x)
```
  -	The entire request coming in from the browser goes into the object request.
  -	The 200 comes from the user requested URL and goes into the variable guess.
  -	We create the context, aka render data, by placing the value of guess into a dictionary variable, x, with the key of g-u-e-s-s.


-	Format and example of the template file
  -	In the template file, we use double curly braces with variable names to designate hotspots for substitution.
  -	We use {% .... %} to insert code.
    ```
      <html>
      <head>
          <title>A conditional template</title>
      </head>
      <body>
          <p>Your guess was {{ guess }}</p>
          {% if guess < 42 %}
              <p>Too low</p>
          {% elif guess > 42 %}
              <p>Too high</p>
          {% else %}
              <p>Just right</p>
          {% endif %}
      </body>
      </html>
    ```
-	Django Template naming convention and file structure
  -	Files in `/appName/templates` are global. When you start up the Django project, it loads all the applications. In order to distinguish file names that may be used in common across multiple applications, e.g., main.html or detail.html, we use a standard naming convention that repeats the application name.
  -	Template naming convention:
      -	`/appName/templates/appName/templateName.html`
      -	where `appName` is the name of the app, and `templateName` is the name of the template.
  -	If you INSTEAD were to store the template in `/appName/templates/ templateName.html`, then any other app can access `templateName.html`, without reference to the application name – this could create confusion for commonly used template names across multiple apps like login.html
  - Recall the subfolder structure for a Django web application:
    ![Django Web Application Folder Structure](images/Django-Application-Directory-Structure.png)



#### The Django Template Language

-	If you use the Django Template Language (DTL), when you implement substitution with the double curly braces, then you automatically "escape" any user input data.
-	To undo the automatic "escape" function, then add the `|safe` filter after the user input parameter.
  -	e.g., `{{userInput|safe}}`
-	Between `{% .... %}`, you can call code (i.e., functions and class methods), specify logic (if-then, for loops), or you can substitute block content by calling part or all of another template.


-	DTL example that uses a simple function with no parameters:
  -	The user requests the URL:
  https://samples.dj4e.com/tmpl/simple
      -	app name: tmpl
      -		view name: simple
  -	in `urls.py`:
      ```
          app_name='tmpl'
          urlpatterns = [
              path('simple', views.simple),]
      ```
  -	in `views.py`:
  ```
          from django.shortcuts import render
          def simple(request):
              return render(request, 'tmpl/simple.html')
  ```
  -	in DTL, `simple.html`
      ```
        <html>
        <head>
            <title>A simple page</title>
        </head>
        <body>
            <h1>This is pretty simple</h1>
        </body>
        </html>
      ```


-	DTL example that uses a simple function with hard-coded parameters:
  -	The user requests the URL:
    https://samples.dj4e.com/tmpl/guess
      -	app name: tmpl
      -	view name: guess
  -	in `urls.py`:
    ```
        app_name='tmpl'
        urlpatterns = [
            path('guess', views.guess),]
    ```
  -	in `views.py`:
  ```
          from django.shortcuts import render
          def guess(request):
              context = { 'zap' : '42' }
              return render(request, 'tmpl/guess.html', context)
  ```
  -	in DTL, `simple.html`
    ```
        <html>
        <head>
            <title>A simple page</title>
        </head>
        <body>
            <p>Your guess was {{ zap }}</p>
        </body>
        </html>
    ```


-	DTL example that uses a simple function with parameters, and Django template with embedded `for` loop and `if` state event code:
  - The user requests the URL:
    https://samples.dj4e.com/tmpl/loop
      -	app name: tmpl
      -	view name: loop
  -	in `urls.py`:
    ```
        app_name='tmpl'
        urlpatterns = [
            path('loop', views.loop),]
    ```
  -	In `views.py`, two arrays f & n are passed in along with the render data, the dictionary x. x has keys that point to both arrays:
  ```
          def loop(request) :
              f = ['Apple', 'Orange', 'Banana', 'Lychee']
              n = ['peanut', 'cashew']
              x = {'fruits' : f, 'nuts' : n, 'zap' : '42' }
              return render(request, 'tmpl/loop.html', x)
  ```
  -	in `loop.html`:
    ```
      <html>
      <head>
          <title>A loop template</title>
      </head>
      <body>
          <p>Your guess was {{ zap }}</p>
          <ul>
          {% for x in fruits  %}
          <li>{{ x }}</li>
          {% endfor %}
          </ul>
          {% if nuts %}
              <p>Number of nuts: {{ nuts|length }}</p>
          {% else %}
              <p>No nuts.</p>
          {% endif %}
      </body>
      </html>
    ```

-	DTL example that uses a simple function with hard-coded parameters that consists of hierarchical, nested objects:
  -	The user requests the URL:
    https://samples.dj4e.com/tmpl/nested
      -	app name: tmpl
      -	view name: nested
  -	in urls.py:
    ```
        app_name='tmpl'
        urlpatterns = [
            path('nested', views.nested),]
    ```
  -	in `views.py`
    ```
          def nested(request) :
              x = {'outer' : { 'inner' : '42' } }
              return render(request, 'tmpl/nested.html', x)
    ```
  - in nested.html:
    ```
      <html>
      <head>
          <title>A nested page</title>
      </head>
      <body>
          <p>Your guess was {{ outer.inner }}</p>
      </body>
      </html>
    ```
-	DTL example that uses a simple function with user requested parameters:
  -	The user requests the URL:
    https://samples.dj4e.com/tmpl/game/200
      -	app name: tmpl
      -	view name: game
      -	parameter: 200
  -	in `urls.py`:
```
          app_name='tmpl'
          urlpatterns = [
              path('game/<slug:guess>', views.GameView.as_view()),]
```
  -	in `views.py`:
```
      from django.shortcuts import render
      from django.views import View
      class GameView(View) :
          def get(self, request, guess) :
              x = {'guess' : int(guess) }
              return render(request, 'tmpl/cond.html', x)
```
  - in `cond.html`:
```
      <html>
      <head>
          <title>A conditional template</title>
      </head>
      <body>
          <p>Your guess was {{ guess }}</p>
          {% if guess < 42 %}
              <p>Too low</p>
          {% elif guess > 42 %}
              <p>Too high</p>
          {% else %}
              <p>Just right</p>
          {% endif %}
      </body>
      </html>
```


#### Inheritance in Django Templates

-	Inheritance enables the objective/best practice in object oriented programming to not repeat yourself. You have some kind of a shape (class) and then you make a bunch of objects that mirror that shape, and with inheritance you can also have a shape that makes another shape (sub-class), like a shape in general that's a triangle.
-	In Django we use classes and objects that were already created for us, meaning that they've got a predefined structure with data and methods in them, like `request.scheme`, that are ready for us to use as they are, or can be used as the starting point to add other data attributes and methods.
-	Template rendering combines some render data, a template, and a base template into an ultimate template -- you pass in the render data and produce the rendered output.
-	If you have an application with many pages, and many of the pages repeat the same thing, e.g., a navigation bar, you want to code that repeated item once in the base template.
-	The unique items on each page are placed in a template that inherit from the base template.


-	DTL example that uses template inheritance:
  - The user requests the URL: https://samples.dj4e.com/tmpl/game2/200
      -	app name: tmpl
      -	view name: game2
      -	parameter: 200
  -	in `urls.py`:
  ```
          app_name='tmpl'
          urlpatterns = [
              path('game2/<slug:guess>', views.Game2View.as_view()),]
  ```
  -	in `views.py`
  ```
          class Game2View(View) :
              def get(self, request, guess) :
                  x = {'guess' : int(guess) }
                  return render(request, 'tmpl/cond2.html', x)
  ```
  -	in `tmpl/cond2.html`:
  ```
          {% extends "tmpl/base.html" %}

          {% block content %}
              <p>Your guess was {{ guess }}</p>
              {% if guess < 42 %}
                  <p>Too low</p>
              {% elif guess > 42 %}
                  <p>Too high</p>
              {% else %}
                  <p>Just right</p>
              {% endif %}
          {% endblock %}
  ```
  -	in `tmpl/base.html`:
  ```
          <html>
          <head>
              <title>Base template</title>
          </head>
          <body>
              {% block content %}{% endblock %}
          </body>
          </html>
  ```
      -	The template, `tmpl/cond2.html`, called by the view class, Game2View, has a reference that extends the base template, `tmpl/base.html`.
      -	The base view uses the `{% block %}` format to create a block hotspot where the first (calling) template,  `tmpl/cond2.html`, can be inserted.
      -	So, the content in the `cond2.html` that is between the `block` and the `endblock` is what is then pushed into the base template. And then the base template, with those blocks replaced, is rendered with the context data and then returned to Django as the HTTP response.


#### Reversing Django Views and URLs

-	A user requested URL, which is just a string, will take the user to a predetermined view. A developer will sometimes know the view they require, and will want the URL string so they can embed that in a page. This is URL reversing.
-	The developer could hard-code the desired URL string into their coded response/template, but this would make the code difficult to re-use as another project/application, or if the developer renames the application.
-	URL reversing will be used when we setup login/logout in our web application pages. We will need to be able to provide a reverse URL that points to the login/logout location.


-	Reversed URL
  -	In urls.py, we add names to the various path statements
  ```
          from django.urls import path
          from . import views
          from django.views.generic import TemplateView
          app_name = 'route'
          urlpatterns = [
              path('', TemplateView.as_view(template_name='route/main.html')),
              path('first', views.FirstView.as_view(), name='first-view'),
              path('second', views.SecondView.as_view(), name='second-view'),
          ]
  ```
  -	We can search on these added names, e.g. `name="first-view'`, to find the correct view, i.e., `views.FirstView.asView()`. We don't search on the path (which is the first parameter), i.e., `'first'`, because that could change over the lifecycle of the app.
  -	In views.py:
  ```
          from django.shortcuts import render
          from django.urls import reverse
          from django.views import View

          class FirstView(View):
              def get(self, request) :
                  return render(request, 'route/main.html')

          class SecondView(View):
              def get(self, request) :
                  u = reverse('gview:cats')
                  u2 = reverse('gview:dogs')
                  u3 = reverse('gview:dog', args=['42'] )
                  ctx = {'x1' : u, 'x2': u2, 'x3': u3 }
                  return render(request, 'route/second.html', ctx)
  ```
  -	The developer uses the reverse URL in the Django Template.
  -	Syntax: `{% url 'appName:viewName' %}`
  -	Example: `{% url 'route:first-view' %}`
      -	`url` is a utility that you must specify, and that searches the application's `urls.py` file for the desired view name.
      -	the `url` Django utility will return as a string the path associated with the view name -- i.e.,the first parameter in the path statement. In our example, the string "/route/first" will be returned.
      -	“url” utility form: `{% url 'appName:viewName' param1 %}`
            -	for example: `{% url 'gview:cat' 42 %}`
  -	In the template, `route/templates/route/main.html`:
  ```
          <h1>Using the url tag</h2>
          <ul>
              <li>
                  <a href="/route/second-view">
                  hard-coded</a> (not DRY)
              </li>
              <li>
                  {% url 'route:first-view' %}
                  (url 'route:first-view')
              </li>
              <li>
                  <a href="{% url 'route:second-view' %}">
                  url 'route:second-view'</a>
              </li>
              <li>
                  {% url 'gview:cats' %}
                  (url 'gview:cats') from gview/urls.py
              </li>
              <li>
                  {% url 'gview:cat' 42 %}
                  (url 'gview:cat' 42) from gview/urls.py
              </li>
              <li>
                  {% url 'nsroute:second-view' %}
                  (url 'nsroute:second-view') from dj4e-samples/urls.py
              </li>
          </ul>
  ```
-	Inside of a Django template associated with one application, we can search for a view belonging to another application (in the same project)
  -	In this example, we are in the "route" application, but we request the url for a view ("cats") in the "gview" application.
  -	`<li> {% url 'gview:cats' %} (url 'gview:cats') from gview/urls.py </li>`
-	In this section of the Django template, we are searching for a view name whose url has a parameter
  -	`<li> {% url 'gview:cat' 42 %} (url 'gview:cat' 42) from gview/urls.py </li>`
  -	In the template, `route/templates/route/main.html`:
  ```
      <h1>Using the url tag</h2>
      <ul>
          <li>
              <a href="/route/second-view">
              hard-coded</a> (not DRY)
          </li>
          <li>
              {% url 'route:first-view' %}
              (url 'route:first-view')
          </li>
          <li>
              <a href="{% url 'route:second-view' %}">
              url 'route:second-view'</a>
          </li>
          <li>
              {% url 'gview:cats' %}
              (url 'gview:cats') from gview/urls.py
          </li>
          <li>
              {% url 'gview:cat' 42 %}
              (url 'gview:cat' 42) from gview/urls.py
          </li>
          <li>
              {% url 'nsroute:second-view' %}
              (url 'nsroute:second-view') from dj4e-samples/urls.py
          </li>
      </ul>
  ```
-	You can name URLs in an application's `urls.py` file, using syntax `name = '...'`, in the application folder,
-	OR you can name URLs in a project's `urls.py` file using syntax `namespace = '...'`, in the project folder.
  -	For example, in `/dj4e-samples/dj4e-samples/urls.py` file, you can additionally add a namespace to a path in the `urlpatterns` list variable:
  ```
          urlpatterns = [
              ...
              path('route/', include('route.urls', namespace='nsroute')),
              ...
          ]
  ```
  -	this will give you a second way to access the `route/` URL path.
-	We have just seen how you incorporate reverse URLs in Django templates (.html files).
-	You can also use reverse URLs in python code (.py files)
  -	In `views.py`:
  ```
          from django.shortcuts import render
          from django.urls import reverse
          from django.views import View

          class FirstView(View):
              def get(self, request) :
                  return render(request, 'route/main.html')

          class SecondView(View):
              def get(self, request) :
                  u = reverse('gview:cats')
                  u2 = reverse('gview:dogs')
                  u3 = reverse('gview:dog', args=['42'] )
                  ctx = {'x1' : u, 'x2': u2, 'x3': u3 }
                  return render(request, 'route/second.html', ctx)
  ```



##### Postscript attributions:

*The Django for Everybody course and slides are Copyright 2019-  Charles R. Severance (www.dr-chuck.com) as part of www.dj4e.com and made available under a Creative Commons Attribution 4.0 License.  Please maintain this postscript in all copies of the document to comply with the attribution requirements of the license.  If you make a change, feel free to add your name and organization to the list of contributors on this page as you republish the materials.*

*Initial Development: Charles Severance, University of Michigan School of Information*

*Insert new Contributors and Translators here including names and dates:*  
*Tim Castle, January 2022*
