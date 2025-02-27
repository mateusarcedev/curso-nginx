# Módulo: Proxy Reverso com Nginx

Este módulo do curso de Nginx focou no uso do Nginx como um proxy reverso, permitindo redirecionar requisições para outros servidores ou portas. Abaixo estão os principais aprendizados e uma explicação dos exemplos práticos realizados.

## Objetivo
Configurar o Nginx para atuar como um proxy reverso, encaminhando requisições específicas para diferentes serviços backend, enquanto também serve arquivos estáticos e gerencia erros.

## Configurações Práticas

### 1. Proxy Reverso Básico com Suporte a PHP
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

#### Explicação
- **`listen 8080;` e `server_name localhost;`**  
  O servidor escuta na porta 8080 e responde como `localhost`.
- **`location / { ... }`**  
  Serve arquivos estáticos do diretório `/home/mateusarcedev/dev/nginx`, com listagem automática habilitada (`autoindex on`) e erro 404 para arquivos não encontrados.
- **`location ~ \.php$ { proxy_pass http://localhost:8000; }`**  
  Encaminha todas as requisições de arquivos PHP (ex.: `exemplo.php`) para um servidor backend rodando na porta 8000.
- **`error_page 404 400 401 /error.html;`**  
  Define uma página de erro personalizada para os códigos 404, 400 e 401.

---

### 2. Configuração Padrão com Proxy Reverso
```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;
    server_name _;

    location / {
        proxy_pass http://localhost:8080;
        try_files $uri $uri/ =404;
    }
}
```

#### Explicação
- **`listen 80 default_server;` e `listen [::]:80 default_server;`**  
  O servidor escuta na porta 80 (HTTP padrão) para IPv4 e IPv6, sendo o servidor padrão para requisições não associadas a outros `server_name`.
- **`root /var/www/html;`**  
  Define o diretório base para arquivos estáticos, embora neste caso o foco seja o proxy.
- **`server_name _;`**  
  Aceita requisições para qualquer nome de servidor (wildcard).
- **`location / { proxy_pass http://localhost:8080; ... }`**  
  Encaminha todas as requisições recebidas na porta 80 para o servidor configurado na porta 8080 (o exemplo anterior), com fallback para erro 404 se necessário.

## Aprendizados
- **Conceito de Proxy Reverso**: Entendi que o Nginx pode atuar como intermediário, redirecionando requisições para outros serviços (ex.: servidores PHP, APIs, etc.) enquanto esconde a estrutura interna do backend.
- **Direcionamento por Tipo de Arquivo**: Aprendi a usar `location ~ \.php$` para identificar e encaminhar apenas requisições de arquivos PHP a um servidor backend.
- **Integração de Múltiplos Servidores**: Compreendi como conectar diferentes instâncias do Nginx (ex.: porta 80 redirecionando para porta 8080), criando uma camada de abstração.
- **Flexibilidade de Configuração**: Descobri que o Nginx permite combinar o serviço de arquivos estáticos com proxy reverso no mesmo bloco `server`.