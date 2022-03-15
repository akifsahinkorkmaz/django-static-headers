# DJANGO STATIC HEADERS

Serving staticfiles (.js, .css, media, ...) is an important job of a server. 

## Serving Static Files

### Production
There is different solutions for serving static files in production. For example ngnix gives an easy to follow guide for serving static content on their documentation ( at: https://docs.nginx.com/nginx/admin-guide/web-server/serving-static-content/ ). Using ngnix is a good choice for static file serving. 

You shouldn't use django for serving static files in production. As stated ( here:  https://docs.djangoproject.com/en/4.0/howto/static-files/ ) django is inefficient and probably insecure. 

### Development
Any server should function same on development as production. The problem is, using ngnix during early development is not pretty. Also django has development server and tools built-in for development purposes. Therefor it is acceptable to use django to serve static content on development. Django has some modules for that.


## Necessary Headers For Serving Static Files

Every HTTP request/response has headers to pass additional information with body. That way client and server can communicate better. Static files are no different. Headers are necessary for client or server to understand "what is the content?" and "what to do with content?".

### CORS 

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


### Content-Disposition 

For example client requests resource "image.png" from website-a. Does client want to display image.png on browser? Does client want to download image.png? Client can not know what to do with content by just content. There is a HTTP header for indicating use of content: 
- Content-Disposition (indicates if content is for inline display on browser or download and save) 

(further information: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Disposition)






