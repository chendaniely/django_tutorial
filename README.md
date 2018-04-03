# Doing the Django Tutorial

Notes and stuff while going through the tutorial.

# Initial Files
```
mysite/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        wsgi.py
```

These files are:

- The outer `mysite/` root directory is just a container for your project.
  Its name doesn’t matter to Django; you can rename it to anything you like.
- `manage.py`: A command-line utility that lets you interact with this Django project in various ways.
  You can read all the details about manage.py in django-admin and manage.py.
- The inner `mysite/` directory is the actual Python package for your project.
  Its name is the Python package name you’ll need to use to import anything inside it (e.g. mysite.urls).
- `mysite/__init__.py`: An empty file that tells Python that this directory should be considered a Python package.
  If you’re a Python beginner, read more about packages in the official Python docs.
- `mysite/settings.py`: Settings/configuration for this Django project.
  Django settings will tell you all about how settings work.
- `mysite/urls.py`: The URL declarations for this Django project; a "table of contents" of your Django-powered site.
  You can read more about URLs in URL dispatcher.
- `mysite/wsgi.py`: An entry-point for WSGI-compatible web servers to serve your project.
  See How to deploy with WSGI for more detail.

#  Changing the port

By default, the runserver command starts the development server on the
internal IP at port 8000.

If you want to change the server’s port, pass it as a command-line
argument. For instance, this command starts the server on port 8080:

```
$ python manage.py runserver 8080
```
If you want to change the server’s
IP, pass it along with the port. For example, to listen on all
available public IPs (which is useful if you are running Vagrant or
want to show off your work on other computers on the network), use:

```
$ python manage.py runserver 0:8000
```
0 is a shortcut for 0.0.0.0. Full
docs for the development server can be found in the runserver
reference.

# Creating the Polls app

 In this tutorial, we’ll create our poll app right next to your
 manage.py file so that it can be imported as its own top-level
 module, rather than a submodule of mysite.

To create your app, make sure you’re in the same directory as manage.py and type this command:

```
$ python manage.py startapp polls
```
That’ll create a directory polls, which is laid out like this:

```
polls/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py  # the views file
```

This directory structure will house the poll application.

Views go in the `polls/views.py` file.
To call the view, we need to map it to a URL - and for this we need a URLconf.

To create a URLconf in the polls directory, create a file called `urls.py`.

```
polls/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    urls.py  # url file goes here
    views.py
```

The next step is to point the root URLconf at the polls.urls
module. In `mysite/urls.py`, add an import for `django.urls.include` and
insert an `include()` in the `urlpatterns` list.

## `include()`

The include() function allows referencing other URLconfs. Whenever
Django encounters include(), it chops off whatever part of the URL
matched up to that point and sends the remaining string to the
included URLconf for further processing.

The idea behind include() is to make it easy to plug-and-play
URLs. Since polls are in their own URLconf (polls/urls.py), they can
be placed under “/polls/”, or under “/fun_polls/”, or under
“/content/polls/”, or any other path root, and the app will still
work.

> When to use include()
>
> You should always use include() when you
> include other URL patterns. admin.site.urls is the only exception to
> this.

# Database setup (`mysite/settings.py`)

If not using sqlite make sure you create the database first,
and the user in `mysite/settings.py` has `create` privileges.

- `mysite/settings.py` will specify the database setup under the `DATABSES` global dictionary, sqlite is the default.
    - `ENGINE` – Either
        - 'django.db.backends.sqlite3'
        - 'django.db.backends.postgresql'
        - 'django.db.backends.mysql', or
        - 'django.db.backends.oracle'
        - Other backends are also available.
    - `NAME` – The name of your database.
    If you’re using SQLite, the database will be a file on your computer;
    in that case, NAME should be the full absolute path, including filename, of that file.
    The default value, os.path.join(BASE_DIR, 'db.sqlite3'), will store the file in your project directory.

# Installed apps (`mysite/settings.py`)

`INSTALLED_APPS` holds the names of all Django applications that are activated in this Django instance.
Apps can be used in multiple projects, and you can package and distribute them for use by others in their projects.

By default, INSTALLED_APPS contains the following apps, all of which come with Django:

- django.contrib.admin – The admin site. You’ll use it shortly.
- django.contrib.auth – An authentication system.
- django.contrib.contenttypes – A framework for content types.
- django.contrib.sessions – A session framework.
- django.contrib.messages – A messaging framework.
- django.contrib.staticfiles – A framework for managing static files.

Some of these applications make use of at least one database table, though,
so we need to create the tables in the database before we can use them

```bash
python manage.py migrate
```

The migrate command looks at the INSTALLED_APPS setting and creates any necessary database tables according to the database settings in your mysite/settings.py file and the database migrations shipped with the app

# Models

models – essentially, your database layout, with additional metadata

> A model is the single, definitive source of truth about your data. It contains the essential fields and behaviors of the data you’re storing. Django follows the DRY Principle. The goal is to define your data model in one place and automatically derive things from it.

> This includes the migrations - unlike in Ruby On Rails, for example, migrations are entirely derived from your models file, and are essentially just a history that Django can roll through to update your database schema to match your current models.

In our simple poll app, we’ll create two models: Question and Choice. A Question has a question and a publication date. A Choice has two fields: the text of the choice and a vote tally. Each Choice is associated with a Question.

These concepts are represented by simple Python classes. Edit the polls/models.py

```python
from django.db import models


class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')


class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

note a relationship is defined, using `ForeignKey`.
That tells Django each Choice is related to a single Question

# Activating models

add `'polls.apps.PollsConfig',` to the `INSTALLED_APPS` section in `mysite/settings.py`

```bash
python manage.py makemigrations polls
```
By running makemigrations, you’re telling Django that you’ve made some changes to your models (in this case, you’ve made new ones) and that you’d like the changes to be stored as a migration.

There’s a command that will run the migrations for you and manage your database schema automatically - that’s called `migrate`

but first, let’s see what SQL that migration would run. The sqlmigrate command takes migration names and returns their SQL:

```bash
python manage.py sqlmigrate polls 0001
```

The `sqlmigrate` command doesn’t actually run the migration on your database - it just prints it to the screen so that you can see what SQL Django thinks is required. It’s useful for checking what Django is going to do or if you have database administrators who require SQL scripts for changes.

```bash
# checks for any problems in your project without making migrations or touching the database
python manage.py check
```

run migrate again to create those model tables in your database:
```bash
python manage.py migrate
```

remember the three-step guide to making model changes:

1. Change your models (in models.py).
2. Run python manage.py makemigrations to create migrations for those changes
3. Run python manage.py migrate to apply those changes to the database.

# Playing with the API
