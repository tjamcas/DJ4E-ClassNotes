# Django for Everybody

## Course #2: Building Web Applications in Django

### Week 1 - Data Models

#### Django for Everybody Introduction

Other Python-based web frameworks:

-	Flask
  - Django is better to learn first, and Flask can be learned afterwards
-	Sanic
  - More specialized
-	Web2py
  - mentioned as another framework
-	Django and Python’s shortcomings in web development will be addressed through future releases – because people want to code in Python

For debugging:
-	Use Firefox or Chrome
-	Safari is not recommended – it’s developer view hides too much information

Setting up PythonAnywhere with Django and how to resolve common errors/problems
- Get supplemental instructions (and troubleshooting tips), from the DJ4E website, to install Django on PythonAnywhere at:
  - <https://www.dj4e.com/assn/dj4e_tutpaw.md>


#### Understanding Model View Controller (MVC)

Mapping Django architecture to Model-View-Controller structure:

-	Controller:  `urls.py` and some of `views.py` both inform the controller. `urls.py` determines routing of what view to display next. `urls.py` and `views.py` decide what happens next as the web server/application responds to the user during the request-response cycle.
-	Model: `models.py` defines the persistent storage (i.e., database) of the application
-	View: `views.py` (performs both a Controller and a View functions), along with templates and `forms.py`, produces the output and sends it out.


#### Django Data Models

Object Relational Mapping (ORM)
-	Database Architecture: the physical database layer, and then you have SQL, which is an abstraction, and then Django builds another abstraction on top of that called object relational mapper.
-	With ORM, we create a Python object. The object has data/attributes like a name, or an email address, or a birthday. The object has methods, e.g., code to insert the object’s data into a table or updating a row of the table with the data.
-	The “object” has data and methods that “map” into the “relational” database – ORM
-	There is other information that is communicated to the database through the ORM, e.g., indexing, ordering, etc.
-	In Django, as you develop more advanced databases, you can bypass the ORM and write directly to the database in SQL. However, 80% of what the developer wants to accomplish can be coded in ORM.
-	ORM allows the developer to have multiple/different databases for their DEV, QA, and PROD environment because Django ORM code knows/interprets the differences between SQLite, Postgres, and MySQL. This allows the developer to start an application using SQLite in DEV, and moving to MySQL or Postgres in PROD.

Creating `models.py`:
-	By creating `models.py`, we are creating the data model for the applications SQL database structure
-	`models.py` has to import the “models” library from the Django file `django.db`. In `models.py`:
  - `from django.db import models`
-	To create a table in the ORM, the developer creates a class that extends (i.e., inherits from) the `django.db` `models.Model` superclass. For example, in `models.py`:
  - `class User(models.Model)`

Performing C-R-U-D manipulations on the database using the table class data attributes and methods:
- for instructions on how to open a Django shell go to: <https://www.dj4e.com/lectures/DJ-02-Model-Single.txt>
- Create examples into User database table while in a Python shell:
  - `u = User(name='Kristen', email='kf@umich.edu')`
    - `User (…)` is a constructor and creates a row instance
  - `u.save()`
- Read examples of User database table:
  - `User.objects.values()`
    - User.objects is an attribute of the User class, and values() is a method that can be performed on the “objects” attribute
  - `User.objects.filter(email='csev@umich.edu').values()`
    - filter(…) is also a method that can be performed on the User.objects attribute
  - `User.objects.values().order_by('email')`
  - `User.objects.values().order_by('-name')`
    - Likewise .order_by(…) is a method
- Delete examples on User database table:
  - `User.objects.filter(email='ted@umich.edu').delete()`
- Update examples on User database table:
  - `User.objects.filter(email='csev@umich.edu').update(name='Charles')`

#### Django Data Models

`models.py` is used to create migrations files
-	The file resides in the application folder. Therefore, every application will have its own `models.py` file.
-	It uses object relational mapping language to specify generic SQL database structure/tables.
-	The programmer creates and edits `models.py` to specify the SQL database they want to have created in the Django environment.

makemigrations
-	Automatically generate the migration files used to create the SQL database files
-	python shell command:
  - `python manage.py makemigrations`
-	The above command in a python shell will create the SQL database file with the structure that the programmer specified in `models.py`.
-	The migration files reside in the "migrations" subfolder of the application folder. e.g. `\~/polls/migrations/0001_initial.py`, `\~/polls/migrations/0002_initial.py`, etc.
-	The migration files are NOT the database, and are NOT SQL, but they ARE the instructions for making the application’s database.


migrate
-	Automatically activate the migration files to generate the SQL database
-	Python shell command:
  - `python manage.py migrate`
-	initiates activities to apply the migrations files, e.g. `0001_initial.py`, to make or change the database structure of the application.
-	By applying the init migrations files, - which are python files/programs - SQL commands in the init files are executed in whatever SQL platform runs in your environment. It could be a sqlite3 environment in DEV, and an Oracle SQL environment in PROD.
-	The Django shell will handle any unique requirements or syntax to interface with the SQL platform in the application environment. The python-based init files and their internal commands, which use the Object Relationship Mapper, will work with all the SQL platforms - you can copy your `models.py` and migrations files from DEV to PROD without modification.

settings.py
-	`makemigrations` activities are guided by the file `settings.py`, and it has to be properly edited to support the `makemigrations` generation of migration files.
-	`settings.py` contains the list of applications whose database migration files should be created or modified.

Migrations files are logical and evolutionary and coded in python.
-	When you first create the `models.py` file and run `makemigrations`, the `0001_initial.py` file is created.
-	When you modify the `models.py` file, perhaps to add a column to a table, the `0002_initial.py` file is created.
-	Subsequent modifications of `models.py` result in the creation of `0003_initial.py`, `0004_initial.py`, and so on.
-	The contents of these init files are not SQL - they are logical representations of the database structure.
-	Because they are a logical abstraction, the Django shell can interpret them and translate them to the appropriate syntax and structure for the SQL environment that is being used.

Portability
-	Because the migration init files are a logical abstraction of SQL and not the SQL syntax of a specific SQL product, the Django shell can interpret the init files and translate them to the appropriate syntax and structure for the SQL environment that is actually being used by the web application.
-	This allows the developer to create a data model and migration in SQLite3 for DEV on the desktop, and copy the `model.py` file to a MySQL environment in PROD for launch.

Database re-build
-	If you want to restart and rebuild your application from scratch, then delete the database, and re-run `makemigrations`
-	In sqlite3, you would delete (`rm`) `db.sqlite3`.
-	Deletion of `db.sqlite3` will erase the database and all its data.
-	It also wipes out any admin accounts that you created - these will have to be manually created again.
