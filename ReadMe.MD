# DJANGO STATIC HEADERS

Serving staticfiles (.js, .css, media, ...) is an important job of a server. 

---
## Introduction to Serving Static Files
----

### Production
There is different solutions for serving static files in production. For example ngnix gives an easy to follow guide for serving static content on their documentation ( at: https://docs.nginx.com/nginx/admin-guide/web-server/serving-static-content/ ). Using ngnix is a good choice for static file serving. 

You shouldn't use django for serving static files in production. As stated ( here:  https://docs.djangoproject.com/en/4.0/howto/static-files/ ) django is inefficient and probably insecure. 

### Development
Any server should function same on development as production. The problem is, using ngnix during early development is not pretty. Also django has development server and tools built-in for development purposes. Therefor it is acceptable to use django to serve static content on development. Django has some modules for that.


### Necessary Headers For Serving Static Files
Every HTTP request/response has headers to pass additional information with body. That way client and server can communicate better. Static files are no different. Headers are necessary for client or server to understand "what is the content?" and "what to do with content?".

---
#### CORS 

Cross-Origin Resource Sharing (CORS) is used when you try to load content from a different (domain, scheme, or port) website. 

For example website-a has a resource "image.png" that website-b requests to use. Since website-a and website-b has different origins this is called CORS.

Modern browsers handles that for client side but acording to standards, servers should handle it for requests and responses.

Server handles that via HTTP headers. 

Some CORS related request headers are:
- Access-Control-Request-Method (indicates which method CORS requests can use)
- Access-Control-Request-Headers (indicates which headers CORS requests can use)

Some CORS related response headers are:
- Access-Control-Allow-Origin (indicates which origins CORS response will be allowed)
- Access-Control-Allow-Credentials (indicates (related to credentials) if CORS response can be allowed)

There are more headers related to CORS in HTTP but will not be used at this project. (further information: https://fetch.spec.whatwg.org/#http-cors-protocol) 

---
#### Content-Disposition 

For example client requests resource "image.png" from website-a. Does client want to display image.png on browser? Does client want to download image.png? Client can not know what to do with content by just content. There is a HTTP header for indicating use of content: 
- Content-Disposition (indicates if content is for inline display on browser or download and save) 

(further information: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Disposition)

---

## Serving Static Files With Django

Django has some methods to handle/serve static files on development. In this article static files (media) will be served with django's built-in modules.

---
You need:
- Python virtual environment- 
- django package-
- django project-

-ready to follow. (See setup.MD for help if needed.)

---

### 1) Creating static files directory

Create a directory for static files inside first dsh folder:
```bash
# C:\...\source\dsh>
mkdir static
```

---

Creating sub-directories for styling (.css), scripts (.js) and media (images etc.) is a better practice! 
There is no sub-directory because there is only media in this tutorial.

---



### 1) Changing settings.py

Add static settings to dsh/dsh/settings.py as :

```python
# - Before -
STATIC_URL = '/static/'
# - - -


# - After -
STATIC_URL = '/static/'

STATICPATHX = BASE_DIR / "static"
STATICFILES_DIRS = [
    STATICPATHX,
]
# - - -
```

### 2) Adding Static Urls

Add static urls to dsh/dsh/urls.py as :

```python
# - Before -
from django.contrib import admin
from django.urls import path

urlpatterns = [
    path('admin/', admin.site.urls),
]
# - - -


# - After -
from django.contrib import admin
from django.urls import path

from django.conf import settings
from django.contrib.staticfiles.urls import staticfiles_urlpatterns

urlpatterns = [
    path('admin/', admin.site.urls),
]

if settings.DEBUG:
    urlpatterns += staticfiles_urlpatterns(settings.STATIC_URL)
# - - -
```

### 3) Add Images To Static Directory

Add any image to static directory at ".../dsh/static"

### 4) Test The Server

#### Run Server:
---
Test the server with:
```bash
# C:\...\source\dsh>

# makemigrations
python manage.py makemigrations

# migrate
python manage.py migrate

# start development server
python manage.py runserver
```

Output should be something like that:
```bash
Django version 3.2.12, using settings 'dsh.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CTRL-BREAK.
```

link to that server is:

```
http://....:PORT 
PORT = 8000
http://....:8000/ = http://localhost:8000/
```

visit ``http://localhost:PORT``, PORT is 8000 almost everytime. 

(link: http://localhost:8000/)

---

#### Test Server:

Get the name of an image file on static folder (example: test1.png). 

```
└───static
        test1.png
        test2.png
```

Create the url to that image as:
```
base_url = http://localhost:8000/

staticurl = http://localhost:8000/static

imageurl = http://localhost:8000/static/test1.png
```

Visit imageurl (change image1.png part as your file name).

(link: http://localhost:8000/static/test1.png)

Image should be displayed by browser without any problem.
