# DJANGO STATIC HEADERS

Serving staticfiles (.js, .css, media, ...) is an important job of a server. 

---
## SECTION 1
## Introduction to Serving Static Files
----

### Production
There is different solutions for serving static files in production. For example ngnix gives an easy to follow guide for serving static content on their documentation ( at: https://docs.nginx.com/nginx/admin-guide/web-server/serving-static-content/ ). Using ngnix is a good choice for static file serving. 

You shouldn't use django for serving static files in production. As stated ( here:  https://docs.djangoproject.com/en/4.0/howto/static-files/ ) django is inefficient and probably insecure. 

### Development
Any server should function same on development as production. The problem is, using ngnix during early development is not pretty. Also django has development server and built-in tools for development purposes. Therefor it is acceptable to use django to serve static content on development. Django has some modules for that.


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

For example client requests resource "image.png" from website-a. Does the client want to display image.png on browser? Does the client want to download image.png? Client can not know what to do with content by just content itself. There is a HTTP header for indicating the aimed use of content: 
- Content-Disposition (indicates if content is for inline display on browser or download and save) 

(further information: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Disposition)

---

## SECTION 2
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



### 2) Changing settings.py

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

### 3) Adding Static Urls

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

### 4) Add Images To Static Directory

Add any image to static directory at ".../dsh/static"

### 5) Test The Server

---
#### 5.1) Run Server:
Run the server with:
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

Link to that server is:

```
http://....:PORT 
PORT = 8000
http://....:8000/ = http://localhost:8000/
```

Visit ``http://localhost:PORT``, PORT is 8000 almost everytime. 

(link: http://localhost:8000/)

---

#### 5.2) Test Server:

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

(example link: http://localhost:8000/static/test1.png)

Image should be displayed by browser without any problem.

----

## SECTION 3

## Testing Static Files as CORS with Django

Django as default does not contain necessary headers for CORS. 

To test this, create a simpleserver directory and CORS.html file inside that directory as:

```html
<!-- simpleserver/CORS.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Django-Cors</title>
</head>
<body>
    <script>
        const run = async () => {
            // Create new image object
            let Img = new Image();

            // Fetch image from server
            // Change test1.png with any filename from dsh/static
            let response = await fetch("http://localhost:8000/static/test1.png"); 
            
            // Create url for fetched image
            response = await response.blob();
            let rurl = await URL.createObjectURL(response);
            
            // Set src for image object as fetched image's url
            Img.src = await rurl;

            Img.alt = "CORS Image"

            // Show image
            Img.onload = () => {
                document.body.appendChild(Img)
            }
        }
        // this will create a image from fetched url
        run();
    </script>
</body>
</html>
```

Serve the html with python:

```bash
# C:\...\simpleserver>
python -m  http.server 7800
```

Visit http://localhost:7800/CORS.html

This should render a blank page because there is an error. 

Visit dev console on browser. Error should be something like this:

```
> Access to fetch at 'http://localhost:8000/static/test1.png' from origin 'http://localhost:7800' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.
   
> Failed to load resource: net::ERR_FAILED

...
``` 

## Serving Static Files as CORS with Django

For CORS to work, atleast Access-Control-Allow-Origin header is needed on response. Since django handles static files we can't simply add this header.

Django handles static files with a built-in function. See ``dsh/dsh/urls.py``:

```python
# ... (irrelevant code)

from django.contrib.staticfiles.urls import staticfiles_urlpatterns # built-in function for static files
# This function is imported from: 
# - django.contrib.staticfiles.url -

# ... (irrelevant code)
if settings.DEBUG:
    urlpatterns += staticfiles_urlpatterns(settings.STATIC_URL)
```

The function can be traced from django module files. For virtualenv django module files are located as ``Lib/site-packages/django``

Since function is from "contrib.staticfiles.urls" see  ``django/contrib/staticfiles/urls.py``

```python
# ... (irrelevant code)

from django.conf.urls.static import static
# this function is imported from:
# - conf.urls.static - 

from django.contrib.staticfiles.views import serve
# this function is imported from:
# - contrib.staticfiles.views - 

def staticfiles_urlpatterns(prefix=None):
    # ... (irrelevant code)

    # conf.urls.static.static function is returned
    return static(prefix, view=serve)
```

There is two path to check. 

1)  ``django/conf/urls/static.py``
2)  ``django/contrib/staticfiles/views.py``
 
See both.  

1)See ``django/conf/urls/static.py`` :

```python 
# ... (irrelevant code)
from django.views.static import serve
# this function is imported from:
# - views.static -

def static(prefix, view=serve, **kwargs):
    # Note that view = django.views.static.serve as default 
    # but view = contrib.staticfiles.views.serve was given at django/contrib/staticfiles/urls.py

    # ... (irrelevant code)
    
    # returns re_path for urls.py with serve function as views from django.views.static
    return [
        re_path(r'^%s(?P<path>.*)$' % re.escape(prefix.lstrip('/')), view, kwargs=kwargs),
    ]
```

2)See ``django/contrib/staticfiles/views.py`` :

```python 
# ... (irrelevant code)
from django.views import static
# this is imported from:
# - views.static -
# and used as 
# - views.static.serve as it was in django/conf/urls/static.py-

def serve(request, path, insecure=False, **kwargs):
    # ... (irrelevant code)

    # return views.static.serve function
    return static.serve(request, path, document_root=document_root, **kwargs)
```

``django/views/static.py`` was imported at both modules. CORS headers can be added on that module.

Add header to ``django/views/static.py``:

```python
from django.http import (
    FileResponse, Http404, HttpResponse, HttpResponseNotModified,
) 
# FileResponse function is imported

def serve(request, path, document_root=None, show_indexes=False):
    path = posixpath.normpath(path).lstrip('/')
    fullpath = Path(safe_join(document_root, path))

    # ... (irrelevant code)
    # response = FileResponse(...)

    # Add header for CORS 
    response.headers["Access-Control-Allow-Origin"] = "*"
    # - this wont be called if DEBUG = False but
    # - if setttings.DEBUG: can be added for additional safety
    # - import django.conf.settings for settings.DEBUG
    # - - -

    # ... (irrelevant code)
    return response

# ... (irrelevant code)
```

Test CORS.html again. It works now.

----

## SECTION 4

## Content-Disposition

Content-Disposition header is used for telling client "what to do with content". 

Normally adding download attribute to html anchor element (``<a href="..." download></a>``) works fine for downloading the content from that link. And html image element (``<img src="...">``) will display content from that link. 

### Create download folder on static folder

Create a directory for downloadable files inside first dsh/static folder:

```bash
# C:\...\source\dsh\static>
mkdir download
```

Add any image to download directory at ".../dsh/static/download"


### Try Anchor To Download Images

Create download.html on simpleserverdirectory:

```html
<!-- simpleserver/download.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <a download href="http://localhost:8000/static/download/download.png"></a>
    <!-- Change download.png with any filename from dsh/static/download -->
</body>
</html>
```

### Test Anchor To Download Images

Visit http://localhost:7800/download.html

Click on download. Image should be opened on browser instead of downloading and saving.

### Serving Static Files as downloadable with Django

For download to work, Content-Disposition header is needed on response. Since django handles static files we can't simply add this header.

Add header to ``django/views/static.py`` again:

```python
from django.http import (
    FileResponse, Http404, HttpResponse, HttpResponseNotModified,
) 
# FileResponse function is imported

def serve(request, path, document_root=None, show_indexes=False):
    path = posixpath.normpath(path).lstrip('/')
    fullpath = Path(safe_join(document_root, path))

    # ... (irrelevant code)
    # response = FileResponse(...)

    if fullpath.parent.name == "download":
             response.headers["Content-Disposition"] = "attachment"

    # Add header for CORS 
    response.headers["Access-Control-Allow-Origin"] = "*"
    # - this wont be called if DEBUG = False but
    # - if setttings.DEBUG: can be added for additional safety
    # - import django.conf.settings for settings.DEBUG
    # - - -

    # Add header for download 
    if "download" in str(path):
        response.headers["Content-Disposition"] = "attachment"
    # - this wont be called if DEBUG = False but
    # - if setttings.DEBUG: can be added for additional safety
    # - import django.conf.settings for settings.DEBUG
    # - - -

    # ... (irrelevant code)
    return response

# ... (irrelevant code)
```

### Test Anchor To Download Images Again

Visit http://localhost:7800/download.html

Click on download. Image should be downloaded.


--- 
END 
---
