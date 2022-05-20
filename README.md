# Nginx Cheat Sheet
## Installation
### Docker
```sh
docker run --name some-nginx -d -p 80:80 nginx
# With nginx.conf
docker run --name my-custom-nginx-container -v /host/path/nginx.conf:/etc/nginx/nginx.conf:ro -d nginx
```

## Serving Static Files
Example: Serve /usr/path/index.html if a user visits port 80
```nginx
# nginx.conf
http {
    server {
        listen 80;
        root /usr/path/index.html; # root /usr/path;
    }
}

events {}
```
*Reload: nginx -s reload*

## Mime Types
Served files should be in the correct format. Ex. for css it should be text/css not just text
```nginx
http {
    include mime.types;
    server {
        listen 80;
        root /usr/path/index.html; # root /usr/path;
    }
}

events {}
```

## Location Context
```nginx
http {
    include mime.types;
    server {
        listen 80;
        root /usr/path; # same as root /usr/path/index.html;
        
        # Serves /usr/path/fruits/index.html
        location /fruits {
            root /usr/path; # same as /usr/path/fruits;
        }
        
        # Serves /usr/path/vegetables/veggies.html
        location vegetables {
            root /usr/path
            try_files /usr/path/vegetables/veggies.html /index.html=404
        }
        
        # With regex
        location ~* /count/[0-9] {}
    }
}

events {}
```

## Redirects and Rewrites
### Redirect
```nginx
http {
    include mime.types;
    server {
        listen 80;
        root /usr/path/index.html; # root /usr/path;
        
         # Serves /usr/path/fruits/index.html
        location /fruits {
            root /usr/path; # same as /usr/path/fruits;
        }
        
        # Redirect to fruits
        location /crops {
            return 307 /fruits
        }
    }
}

events {}
```
### Rewrites
```nginx
http {
    include mime.types;
    server {
        listen 80;
        root /usr/path/index.html; # root /usr/path;
        
        location ~* /count/[0-9] {
            root /usr/path; # same as /usr/path/fruits;
        }
        
        # Rewrite to /count/[0-9]
        new ^/number/(w+) /count/$1
    }
}

events {}
```

## Load Balance
```nginx
http {
    include mime.type
    
    upstream backend {
            server 127.0.0.1:1111
            server 127.0.0.1:2222
    }
        
    server {
        listen 80;
        
        # round robin to "backend" servers
        location / {
            proxy_pass http://backend/;
        }
    }
}

events {}
```

## API Gateway
```nginx
http {
    upstream shipping {
	    server 162.243.144.211:8081;
    }

    upstream reporting {
	    server 162.243.144.211:8082;
    }

    server {
	    listen 80;
	
	    location /shipping/ {
		    proxy_pass http://shipping/;
	    }

	    location /reporting/ {
		    proxy_pass http://reporting/;
	    }
    }
}

events {}
```
