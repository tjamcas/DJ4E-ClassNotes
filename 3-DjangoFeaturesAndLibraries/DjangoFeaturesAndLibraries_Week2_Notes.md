##### **Attribution:**  
- *This document contains my notes from the "Django for Everybody (DJ4E)" MOOC series created by Dr. Charles Severance, University of Michigan. The materials in the DJ4E courses are available under a Creative Commons License to allow for teachers to make use of these materials in their own courses.*  
- *The following notes contain images based on the DJ4E presentation slides, also created by Dr. Severance as course resources. The notes and images in this document were assembled by me as a learning aid to supplement the course content and as a reference for completing the course assignments and quizzes.*

*My notes represent my interpretation of the DJ4E course videos, transcripts and presentation slides.* ***Any content errors or omissions in these notes should be attributed to me, as the note-taker.***



# Django for Everybody

## Course #3: Django Features and Libraries

### Week 2 - Users and Authentication

#### Creating and Managing Users in Django    

- Django login/logout system    
    -	Django comes with its own user administration system and interface
    -	This allows the developer to add users, superusers, groups, permissions


- Superusers
    -	Superusers have all permissions
    -	To create a superuser, you must give it a name and an email address - the email address is for password recovery
    -	By creating a superuser, you are adding row to a table in your SQLite (or whatever database you are using with Django)
    -	You can wipe out your database and start fresh


- *Refresher on rebuilding the Django database and creating a superuser:*
    -	To remove the database, go to your project folder, and type:
        -	`rm db.sqlite`
    -	To rebuild your database:
        -	`python3 manage.py migrate`
    -	To create a superuser:
        -	`python3 manage.py createsuperuser`


#### Login and Logout URLs in Django

- Logging into an app puts data into a session about the current user and their name, their email. Logging out removes the data. Logging in is NOT a session.
    -	Sessions are constructed on top of cookies, and logins are constructed on top of sessions.
    -	In a single session, you could login and logout as the same or multiple users


- Enabling user authentication in Django
    1.	User authentication and administration is automatically enabled by default in the project `settings.py` file.
        -	In `/home/paAccountName/django_projects/mysite/mysite/settings.py`:
            ```
            INSTALLED_APPS = [
                'polls.apps.PollsConfig',
                'home.apps.HomeConfig',
                'hello.apps.HelloConfig',
                'django.contrib.admin',
                'django.contrib.auth',
                'django.contrib.contenttypes',
                'django.contrib.sessions',
                'django.contrib.messages',
                'django.contrib.staticfiles',
            ]
            ```
    2.	The path to the authentication and administration tools must be added by the app developer into the project `urls.py` file
    -	In `/home/paAccountName/django_projects/mysite/mysite/urls.py`:
        ```urlpatterns = [
            path('admin/', admin.site.urls),
            path(‘accounts’, include(‘django.contrib.auth.urls)),
            path('polls/', include('polls.urls')),
            path('hello/', include('hello.urls')),
            path('', TemplateView.as_view(template_name='home/main.html')),
            url(r'^site/(?P<path>.*)$', serve,
                {'document_root': SITE_ROOT, 'show_indexes': True},
                name='site_path'
            ),
        ]
        ```


-	In django.contrib.auth.urls, there is a named view (using the “name =viewName” format). This is a view that Django automatically writes into a file.
    -	In the file there is a
        -	`name = 'login'`
        -	`name = 'logout'`
    -	This allows the developer to write a reverse lookup for `login` and `logout`


-	In an application webpage, the app developer may require that users are logged in to access the page – i.e., the page is protected by login. In this case, we want the user to access the page, go to a login screen and return to the very same page.
    -	this required behavior can be enforced with the `next` parameter in a Django template html file.
        -	see an example at </dj4e-samples/authz/templates/authz/main.html>
        -	more specifically from the example file:
            ```
            {% if user.is_authenticated %}
            <p>Authenticated as
            <pre>
            Name: {{ user.get_full_name }}
            Email: {{ user.email }}
            Id: {{ user.id }}
            </pre>
            </p>
            <p>You can <a href="{% url 'logout' %}?next={% url 'authz:open' %}">Logout</a></p>
            {% else %}
            <p>You are not logged in</p>
            <p>You can <a href="{% url 'login' %}?next={{ request.path }}">Login</a> if you like.</p>
            {% endif %}
            ```

        -	The `user` object is automatically injected into the template context by Django
        -	`user` has the attributes: `is_authenticated` (Boolean variable that is either true or false), `get_full_name`, `email`, and `id` (and other attributes)
            -	In addition to accessing the `user` object in a template, it can also be accessed in the python (e.g., in `views.py`) – see next section for how to access user object in python code
        -	`{% url 'logout' %}?next={% url 'authz:open' %}` is code that sends the user to the logout page, and, when they are done, returns the user to the `open` view in the `authz` application
        -	The `request.path` syntax is an important pattern to be familiar with:
            -	`<a href="{% url 'login' %}?next={{ request.path }}">Login</a>`
                -	`{% url 'login' %}` is a reverse Django view to the login page
                -	`?next={{ request.path }}` is code that returns the user to the present page after they are done at the `login` page

#### Using Django Login in Views

- To create a login page in Django that uses the built-in authentication, administration, error handling, etc., the developer must create a template – ideally, a template that matches the application style and possibly provides other helpful links and information.
    - The developer must create a template that can be accessed as `registration/login.html`
        -	No matter which application in Django project you're working in, the `registration/login` name is global.
    -	The `login.html` must:
        -	handle errors and have some type of output:
        ```
        {% if form.errors %}
          <p>Your username and password didn't match. Please try again.</p>
        {% endif %}
        ```
    -	The form within the login.html must:
        1. post to the login url:
            -	`form.as_p` is a context variable (?????)
        2. Place a submit button in the form
        3. Pass the ‘next’ variable back as a hidden field. A hidden form field is used in the pattern so that after the user submits this form, the user is directed to the next view.
            ```
            <form method="post" action="{% url 'login' %}">
            {% csrf_token %}
            {{ form.as_p }}
            <input type="submit" class="btn btn-primary" value="Login" />
            <input type="hidden" name="next" value="{{ next }}" />
            </form>
            {% endblock %}
            ```
    -	As stated earlier, the “user” object is automatically injected into the template context by Django, and in addition to accessing the “user” object in a template, it can also be accessed in the python (e.g., in `views.py`)
    -	The “user” object is part of the request object, and its attributes can be accessed by referring to the HttpRequest.user object in the python `views.py` code: `is_authenticated` (Boolean variable that is either true or false), `get_full_name`, `email`, and `id` (and other attributes)
        ```
        class DumpPython(View) :
            def get(self, req):
                resp = "<pre>\nUser Data in Python:\n\n"
                resp += "Login url: " + reverse('login') + "\n"
                resp += "Logout url: " + reverse('logout') + "\n\n"
                if req.user.is_authenticated:
                    resp += "User: " + req.user.username + "\n"
                    resp += "Email: " + req.user.email + "\n"
                else:
                    resp += "User is not logged in\n"

                resp += "\n"
                resp += "</pre>\n"
                resp += """<a href="/authz">Go back</a>"""
                return HttpResponse(resp)
        ```
- Add `LoginRequiredMixin` to views that can only be accessed  by a logged in user.
    -	“Mixins are a form of multiple inheritance where behaviors and attributes of multiple parent classes can be combined…. Mixins are an excellent way of reusing code across multiple classes, but they come with some cost. The more your code is scattered among mixins, the harder it will be to read a child class and know what exactly it is doing, and the harder it will be to know which methods from which mixins to override if you are subclassing something that has a deep inheritance tree…. Note also that you can only inherit from one generic view - that is, only one parent class may inherit from View and the rest (if any) should be mixins.” (From djangoproject “Using mixins” documentation).
    -	From /dj43-samples/authz/views.py
        -	`class ManualProtect(View)` is the long (manual) way of using `request.user.is_authenticated` to determine whether to route the user to an authentication page or directly to the requested page
        -	`class ProtectView(LoginRequiredMixin, View)` is the simpler pattern using the Django mixin, `LoginRequiredMixin`, to do the same thing
            -	note that the app developer-defined class, `ProtectView`, inherits from both the Django base class, `View`, and mixin, `LoginRequiredMixin`
                ```
                from django.shortcuts import render, redirect
                from django.views import View
                from django.urls import reverse
                from django.utils.http import urlencode

                class ManualProtect(View) :
                    def get(self, request):
                        if not request.user.is_authenticated :
                            loginurl = reverse('login')+'?'+urlencode({'next': request.path})
                            return redirect(loginurl)
                        return render(request, 'authz/main.html')

                from django.contrib.auth.mixins import LoginRequiredMixin

                class ProtectView(LoginRequiredMixin, View) :
                    def get(self, request):
                        return render(request, 'authz/main.html')
                ```
            -	In `ProtectView`, the get method only runs if the `LoginRequiredMixin` determines that the user is, in fact, logged in.


  ##### Postscript attributions:

  *The Django for Everybody course and slides are Copyright 2019-  Charles R. Severance (www.dr-chuck.com) as part of www.dj4e.com and made available under a Creative Commons Attribution 4.0 License.  Please maintain this postscript in all copies of the document to comply with the attribution requirements of the license.  If you make a change, feel free to add your name and organization to the list of contributors on this page as you republish the materials.*

  *Initial Development: Charles Severance, University of Michigan School of Information*

  *Insert new Contributors and Translators here including names and dates:*  
  *Tim Castle, January 2022*
