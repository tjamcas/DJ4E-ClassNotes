##### **Attribution:**  
- *This document contains my notes from the "Django for Everybody (DJ4E)" MOOC series created by Dr. Charles Severance, University of Michigan. The materials in the DJ4E courses are available under a Creative Commons License to allow for teachers to make use of these materials in their own courses.*  
- *The following notes contain images based on the DJ4E presentation slides, also created by Dr. Severance as course resources. The notes and images in this document were assembled by me as a learning aid to supplement the course content and as a reference for completing the course assignments and quizzes.*

*My notes represent my interpretation of the DJ4E course videos, transcripts and presentation slides.* ***Any content errors or omissions in these notes should be attributed to me, as the note-taker.***



# Django for Everybody

## Course #3: Django Features and Libraries

### Week 4 - One to Many Data Models

#### One to Many Data Models Overview

-	`model.py` is where we define the structure of our database.
-	We access the database through data models and, more specifically, use data model objects to reference the database and create, read, update, and delete table records.
-	We register our data models in `admin.py`.
-	It is important to have a well-defined database model so that your web application scales well and has good performance.
-	The database model is represented in a picture with tables and links between tables.
-	Database normalization is used to assess the design
    -	The key design principle can be summed up by: “Don’t replicate string data”
    -	Instead, define a string once in a table with an integer key for an index
    -	Integers and integer keys are more efficient in storage usage, and easier to access and manipulate

#### Removing Replication in One-to-Many Models

-	If you were start with a single flat table of all your application data, then you look for where you have vertical replication of data – i.e. repeating of data in the columns
    -	This is where you will want to represent the replicated data once in a single table and provide links between tables
    -	Removal of the replication of data results in a performance advantage, a storage advantage and it modifiability advantage (there's only one place where you need to go to update the data).
-	In a picture of our database model, tables are represented by boxes (with the columns/attributes listed) and links are represented by lines
    -	The lines are arrows and use the following legend at the start and end of each arrow to represent their cardinality
        -	1		One
        -	1..* 	Many with a minimum of 1
        -	0..*	Many with a minimum of 0 (null value is OK)
    -	Legends can vary with different modeling systems.


#### Storing Primary and Foreign Keys in a Database

-	The primary key is a column that we add, and is the handle for the rows
    -	The primary key is where we end a link arrow
    -	We can auto-increment the primary key index to increase performance
-	The foreign key is the column that we add to a table so that we can point to a row in another table.
    -	The foreign key is where we will start a link arrow
    -	By convention, the name of the foreign key column is the name of the table to which it point followed by “_id”.


#### Representing One-to-Many Models in Django

-	Here is an example of how you represent a One-to-Many model in Django
    -	`/dj4e-samples/bookone/models.py`
        ```
        from django.db import models

        class Lang(models.Model):
            name = models.CharField(max_length=200)

        class Book(models.Model):
            title = models.CharField(max_length=200)
            isbn = models.CharField(max_length=13)
            lang = models.ForeignKey('Lang', on_delete=models.SET_NULL, null=True)

        class Instance(models.Model):
            book = models.ForeignKey('Book', on_delete=models.CASCADE)
            due_back = models.DateField(null=True, blank=True)
        ```
    -	Remember, these are the instructions for how to create a SQL database for your application – this is not the actual SQL syntax
    -	Django will automatically include the instructions add the “id” field to a table
    -	Django will automatically include the instructions for a foreign key so that the field uses the naming convention of the table to which you are linking followed by “_id”
    -	`null=True`  allows the field to be empty
    -	`on_delete=models.SET_NULL` indicates that if the foreign key value is deleted in the linked table, then set the foreign key in this table to NULL
    -	`on_delete=models.CASCADE` indicates that if the foreign key value is deleted in the linked table, then delete the rows in this table that had that value


#### Using the Django Shell to Explore One-to-Many Models

-	To run the Django shell type: `python manage.py shell`
    -	In creating the Django shell, this command causes the settings.py file to be read for all the listed applications, preloads the applications, and then gives you a shell.


#### Loading One-to-Many Data using a Django Batch Script

-	Assume you have some source of data, perhaps in .CSV format, and you want to rad and put it into the database – but not manually by hand. here is how you write a script to load data into your Django models after you define those Django models.
-	Running scripts is part of a feature of Django called the Django extensions.
    -	Go into your virtual environment and type: `pip3 install django-extensions`
    -	After you install it, you have to put a link into your `settings.py`, specifically
        -	`ProjectName/ProjectName/settings.py`
              ```
              …
              INSTALLED_APPS = [
                  'django.contrib.admin',
                  'django.contrib.auth',
                  'django.contrib.contenttypes',
                  'django.contrib.sessions',
                  'django.contrib.messages',
                  …

                  # Extensions - installed with pip3 / requirements.txt
                  'django_extensions',
                  'crispy_forms',  
                  …

                  # Sample Applications - don't copy
                  'hello.apps.HelloConfig',
                  'users.apps.UsersConfig',
                  'bookone.apps.BookoneConfig',
                  'bookmany.apps.BookmanyConfig',
                  …
                  'cats.apps.CatsConfig',
                  …
              ]
              ….
              ```
    -	Create a “scripts” folder in your project directory
        -	`mkdir scripts`
        -	`touch scripts/__init __.py`
            -	The latter command creates an empty file used by python for “housekeeping”
            -	More documentation on `__init__.py` can be found with an online search but it is no longer needed in python 3.3.
        -	In the `/ProjectName/scripts` folder, write your python script program
            -	e.g., in `/dj4e-samples/scripts/cats_load.py`
                    ```
                    import csv  # https://docs.python.org/3/library/csv.html

                    # https://django-extensions.readthedocs.io/en/latest/runscript.html

                    # python3 manage.py runscript cats_load

                    from cats.models import Cat, Breed

                    def run():
                        fhand = open('cats/meow.csv')
                        reader = csv.reader(fhand)
                        next(reader)  # Advance past the header

                        Cat.objects.all().delete()
                        Breed.objects.all().delete()

                        # Name,Breed,Weight
                        # Abby,Sphinx,6.4
                        # Annie,Burmese,7.6
                        # Ash,Manx,7.8
                        # Athena,Manx,8.9
                        # Baby,Tabby,6.9

                        for row in reader:
                            print(row)

                            b, created = Breed.objects.get_or_create(name=row[1])

                            c = Cat(nickname=row[0], breed=b, weight=row[2])
                            c.save()
                    ```
            -	`next(reader)  # Advance past the header`
                -	Moves the CSV reader past the header row of titles
            -	`b, created = Breed.objects.get_or_create(name=row[1])`
                -	In the current row, get the second field.
                -	If there is not a record with this value in the table, then create the record
            -	c = Cat(nickname=row[0], breed=b, weight=row[2])    
            c.save()
                -	Create a Cat record using the first and third fields along with the breed object, b, that we just got, and then save it (commit it to the database)


##### Postscript attributions:

*The Django for Everybody course and slides are Copyright 2019-  Charles R. Severance (www.dr-chuck.com) as part of www.dj4e.com and made available under a Creative Commons Attribution 4.0 License.  Please maintain this postscript in all copies of the document to comply with the attribution requirements of the license.  If you make a change, feel free to add your name and organization to the list of contributors on this page as you republish the materials.*

*Initial Development: Charles Severance, University of Michigan School of Information*

*Insert new Contributors and Translators here including names and dates:*  
*Tim Castle, January 2022*
