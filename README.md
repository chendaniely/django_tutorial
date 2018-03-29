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

# Database setup
