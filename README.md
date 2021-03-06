Basic Nginx Proxy To Web Server Serving Static Assets

```lang nginx
/*
  The error_log directive sets the location of the nginx error
  log as well as the security level.
*/
error_log /var/log/nginx/error.log warn;

/*
  The `events` block configures connection processing.
*/
events {
  /*
    The epoll directive tells nginx to use the epoll processing
    method based on the Linux 2.6+ epoll system call for detecting
    incoming connections.
  */
  use epoll;

  /*
    The `worker_connections` directive tells
    nginx to allocate memory for up to 1024 connections.
  */
  worker_connections 1024;
}

/* The `http` block creates and configures http/https service */
http {

   /* 
    The `include` directive will take all the directives in  the given file and
    place them here. In this case, it’s the "mime.types" file in the same directory
    as this file,  which contains a mapping of file extensions to MIME types.
  */
  include mime.types;

  /* 
   `default_type` sets the default MIME type as application/octet-stream
   which is basically binary data.
 */
  default_type application/octet-stream;

  /* 
   Every `http` block must contain at least one `server` block which
   defines http and/or https virtual hosts.
 */
  server {

    /* Have this server listen to port 80, the standard HTTP port */
    listen 80;

    /* `server_name` creates a virtual host and sets its names */
    server_name example.org www.example.org example.com;

    /* 
     `location` applies directives depending on the request URI. In this case,
     it applies to every web request except for certain static files, due to the
     second `location` directive.
    */
    location / {

      /* 
       `proxy_pass` will pass all the requests to the web server
       running on localhost:8080
     */
      proxy_pass http://localhost:8080;

      /*
         Include all the directives in the `proxy_params` configuration
         file, which are required for certain web servers when they are behind
         nginx.
      */
      include proxy_params;
    }

    /* 
      This `location` block applies directives to requests whose URIs that start with
      /images /js or /css, ie. static files.
    */
    location ~ ^(/images|/js|/css) {

      /* 
       `root` specifies the directory for these files, which in this case
       is “html”
      */
      root html;

      /*
        `expires` sets the “Expires” HTTP header for these static files to be 30 days.
      */
      expires 30d;
    }
  }
}
```






