# Nginx Course Summary

This repository contains the configuration examples and key learnings from my Nginx course. The course covered fundamental to advanced Nginx features, including HTTP server setup, reverse proxy, API gateway, and load balancing.

---

## 1. HTTP Server Module

### Objective

Configure a basic HTTP server listening on port 8080, serving static files from a specified directory, with custom error handling.

### Key Configuration

```nginx
server {
    listen 8080;
    server_name localhost;

    location / {
        root /home/mateusarcedev/dev/nginx;
        index index.html;
        autoindex on;
        try_files $uri $uri/ =404;
    }
    error_page 404 400 401 /error.html;
}
```

### Learnings

* How to configure listening ports and server names.
* Serving static files with directory indexing enabled.
* Using `try_files` to handle request resolution.
* Custom error page redirection for improved UX.

---

## 2. Reverse Proxy Module

### Objective

Set up Nginx as a reverse proxy to forward requests to backend servers while serving static content and managing errors.

### Example: Basic Reverse Proxy with PHP Support

```nginx
server {
    listen 8080;
    server_name localhost;

    location / {
        root /home/mateusarcedev/dev/nginx;
        index index.html;
        autoindex on;
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        proxy_pass http://localhost:8000;
    }

    error_page 404 400 401 /error.html;
}
```

### Learnings

* Reverse proxy concept and usage to abstract backend services.
* Selective routing based on file types (e.g., PHP files).
* Combining static file serving with proxying in a single server block.

---

## 3. API Gateway Module

### Objective

Configure Nginx as an API Gateway to route, authenticate, and balance requests among multiple microservices.

### Configuration Snippet

```nginx
include /etc/nginx/microservices/*.conf;

server {
    listen 8080;
    server_name localhost;

    location / {
        root /home/mateusarcedev/dev/nginx;
        index index.html;
        autoindex on;
        try_files $uri $uri/ =404;
    }

    location /servico1 {
        proxy_pass http://localhost:8001/;
    }

    location /servico2 {
        proxy_pass http://localhost:8002/;
    }

    error_page 404 400 401 /error.html;
}
```

### Microservices Servers

Separate server blocks running on ports 8001 and 8002, each serving their own static content and error pages.

### Learnings

* Centralizing entry point for multiple microservices.
* URL-based routing to distinct backend services.
* Modular configuration using `include`.
* Centralized error handling for better user experience.

---

## 4. Load Balancer Module

### Objective

Use Nginx to distribute incoming requests across multiple backend servers to increase availability and scalability.

### Load Balancer Configuration

```nginx
upstream servicos {
    server localhost:8001;
    server localhost:8002;
}

server {
    listen 8003;
    server_name localhost;

    location / {
        proxy_pass http://servicos;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### Backend Services Configuration

Servers on ports 8001 and 8002 serve static content with separate logs for monitoring.

### Learnings

* Defining backend server pools with `upstream`.
* Proxying requests to backend pools for load distribution.
* Passing real client IP to backend services for accurate logging.
* Enabling gzip compression for performance.
* Monitoring backend activity via dedicated logs.
* Testing load balancing behavior via repeated requests.

---

## Overall Course Insights

This course provided a solid foundation in using Nginx as a versatile tool beyond simple HTTP serving: acting as reverse proxy, API gateway, and load balancer. It reinforced critical concepts such as modular configuration, error handling, traffic routing, and infrastructure scalability.

These skills prepare me to architect resilient and high-performance web infrastructures capable of supporting modern microservices and distributed applications.

---

MIT © Mateus Silva Andrade Arce
