# Módulo: API Gateway com Nginx

Este módulo foca no uso do Nginx como um API Gateway, permitindo autenticar, rotear e balancear requisições para diferentes microserviços. Abaixo estão os principais aprendizados e uma explicação dos exemplos práticos realizados.

## Objetivo
Configurar o Nginx para atuar como um API Gateway, encaminhando requisições para diferentes microserviços backend, servindo arquivos estáticos e gerenciando erros de forma centralizada.

## Configurações Práticas

### 1. API Gateway com Roteamento para Microserviços
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

#### Explicação
- **`include /etc/nginx/microservices/*.conf;`**  
  Permite incluir configurações adicionais de microserviços de forma modular.
- **`location / { ... }`**  
  Serve arquivos estáticos do diretório `/home/mateusarcedev/dev/nginx`, com listagem automática (`autoindex on`).
- **`location /servico1` e `location /servico2`**  
  Encaminha requisições para os microserviços rodando nas portas `8001` e `8002`, respectivamente.
- **`error_page 404 400 401 /error.html;`**  
  Define uma página de erro personalizada para os códigos `404`, `400` e `401`.

---

### 2. Configuração dos Microserviços
```nginx
server {
    listen 8001;
    server_name localhost;

    location / {
        root /home/mateusarcedev/dev/nginx/servico1;
        index index.html;
    }

    error_page 404 400 401 /erro.html;
}

server {
    listen 8002;
    server_name localhost;

    location / {
        root /home/mateusarcedev/dev/nginx/servico2;
        index index.html;
    }

    error_page 404 400 401 /erro.html;
}
```

#### Explicação
- **Cada microserviço tem um bloco `server` separado**  
  Isso permite isolar serviços e escutá-los em portas diferentes (`8001`, `8002`).
- **Servem arquivos estáticos próprios**  
  Cada microserviço possui seu próprio diretório de arquivos.
- **Páginas de erro personalizadas**  
  Cada serviço pode retornar sua própria página de erro.

## Aprendizados
- **API Gateway como ponto único de entrada**: O Nginx pode centralizar e gerenciar requisições para diferentes microserviços.
- **Roteamento baseado em URL**: Utilizando `location /servico1` e `location /servico2`, podemos distribuir chamadas para diferentes backends.
- **Modularização da Configuração**: O uso do `include` permite adicionar ou remover serviços sem alterar diretamente o arquivo principal.
- **Geração de Erros Centralizada**: Podemos configurar páginas de erro customizadas para oferecer melhores respostas aos usuários.

Essa configuração básica pode ser expandida para incluir autenticação, rate limiting e balanceamento de carga.

