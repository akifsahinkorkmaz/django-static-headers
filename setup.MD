# Setup 

## Virtual Environment :

---
virtualenv package is recommended!

---

Installation:
```bash
pip install virtualenv
``` 

Setup:
```bash
# this will create a virtualenv directory named source
virtualenv source
``` 

Activate:
```bash
# get source directory
cd source

# this will activate it on windows:
scripts\activate

# this will probably activate it for another OS:
# (source is a command/not related with name of the virtual environment/it's just coincidence):
source bin/activate
```
(for further information: https://virtualenv.pypa.io/en/latest/user_guide.html#activators)


Output should be:
```bash
# source virtualenv activated
(source) C:\...\source>
```

## Django :

---
Django 3.2.12 is used! 

---

Installation:
```bash
# (source) C:\...\source>
pip install django
``` 

Setup project:
```bash
# (source) C:\...\source>
django-admin startproject dsh
``` 
(for further information: https://docs.djangoproject.com/en/4.0/intro/tutorial01/)


Output should be:
```bash
# for source/dsh
│   manage.py
│
└───dsh
        asgi.py
        settings.py
        urls.py
        wsgi.py
        __init__.py

```


