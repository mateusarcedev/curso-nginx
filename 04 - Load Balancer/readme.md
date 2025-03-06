# Módulo: Load Balancer com Nginx

Este módulo tem como foco o uso do **Nginx como Load Balancer**, distribuindo requisições entre múltiplos servidores para garantir maior disponibilidade e escalabilidade das aplicações.

## Objetivo
Configurar o **Nginx** para atuar como um balanceador de carga (**Load Balancer**) utilizando o módulo `upstream`, distribuindo o tráfego entre diferentes serviços backend.

## Configuração Prática

### 1. Definição dos Servidores Backend (Microservices)

Os serviços backend estão configurados para rodar nas portas **8001** e **8002**, servindo conteúdo estático e recebendo requisições do Load Balancer.

#### **Arquivo: `services.conf`**
```nginx
server {
    listen 8001;
    server_name localhost;
    access_log /home/mateusarcedev/dev/nginx/logs/servico1.log main;

    location / {
        root /home/mateusarcedev/dev/nginx/servico1;
        index index.html;
    }

    error_page 404 400 401 /erro.html;
}

server {
    listen 8002;
    server_name localhost;
    access_log /home/mateusarcedev/dev/nginx/logs/servico2.log main;

    location / {
        root /home/mateusarcedev/dev/nginx/servico2;
        index index.html;
    }

    error_page 404 400 401 /erro.html;
}
```

### 2. Configuração do Load Balancer

O arquivo abaixo configura um **Load Balancer** rodando na porta **8003**, distribuindo as requisições entre `localhost:8001` e `localhost:8002`.

#### **Arquivo: `load-balancer.conf`**
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

### 3. Configuração Geral do Nginx

No arquivo principal do **Nginx**, configuramos a estrutura para carregar os microservices e o load balancer.

#### **Arquivo: `nginx.conf`**
```nginx
user www-data;
worker_processes auto;
pid /run/nginx.pid;
error_log /var/log/nginx/error.log;
include /etc/nginx/modules-enabled/*.conf;
include /etc/nginx/microservices/*.conf;

events {
    worker_connections 768;
}

http {
    sendfile on;
    tcp_nopush on;
    types_hash_max_size 2048;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;

    log_format main 'Remote Addr: $http_x_real_ip, Time: [$time_local], Request: "$request" '
                    'Status: $status';

    gzip on;

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

## Explicação

- **`upstream servicos {}`**: Define um grupo de servidores backend (porta **8001** e **8002**).
- **`proxy_pass http://servicos;`**: Direciona as requisições para o grupo de servidores definido em `upstream`.
- **`proxy_set_header X-Real-IP $remote_addr;`**: Passa o IP real do cliente para os servidores backend, evitando que seja registrado apenas o IP do proxy reverso.
- **Arquivos de log por serviço**: Cada serviço tem um **arquivo de log** separado para melhor monitoramento.
- **Gzip ativado**: Melhor desempenho ao comprimir respostas.

## Testando o Load Balancer
**Simule várias requisições** e veja se elas são distribuídas entre os servidores backend:
   ```bash
   for i in {1..10}; do curl -s http://localhost:8003; done
   ```

## Aprendizados
- **Configuração de Load Balancer**: Aprendi a distribuir requisições entre diferentes servidores backend com `upstream`.
- **Monitoramento de Logs**: Cada serviço pode ter seus próprios logs para facilitar depuração e análise.
- **Uso de Proxy Pass**: O Nginx atua como intermediário entre clientes e servidores backend.
- **Balanceamento de carga**: As requisições são distribuídas dinamicamente entre servidores disponíveis.

---

Este estudo serviu como base para aprender sobre **Load Balancing com Nginx** e pode ser expandido para incluir recursos como **round-robin, least connections** e **failover**! 🚀

