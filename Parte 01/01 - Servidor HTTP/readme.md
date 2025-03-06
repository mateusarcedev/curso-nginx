
# Módulo: Servidor HTTP com Nginx

Este módulo do curso de Nginx abordou os fundamentos da configuração de um servidor HTTP básico utilizando o Nginx. Abaixo estão os principais aprendizados e uma explicação do exemplo prático realizado.

## Objetivo
Configurar um servidor HTTP simples que escuta na porta 8080, serve arquivos estáticos a partir de um diretório específico e lida com erros de forma personalizada.

## Configuração Básica
O arquivo de configuração criado foi o seguinte:

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

### Explicação dos Componentes
1. **`server { ... }`**  
   Define um bloco de servidor que especifica como o Nginx deve responder às requisições.

2. **`listen 8080;`**  
   Configura o servidor para escutar na porta 8080, permitindo que ele receba requisições HTTP nessa porta.

3. **`server_name localhost;`**  
   Define o nome do servidor como `localhost`, ou seja, ele responderá às requisições direcionadas a este endereço.

4. **`location / { ... }`**  
   Configura como o servidor lida com requisições para a raiz (`/`). Inclui:
   - **`root /home/mateusarcedev/dev/nginx;`**  
     Define o diretório base onde os arquivos estáticos serão buscados.
   - **`index index.html;`**  
     Especifica que o arquivo `index.html` será servido como padrão ao acessar o diretório raiz.
   - **`autoindex on;`**  
     Habilita a listagem automática de arquivos no diretório caso nenhum arquivo `index.html` esteja presente.
   - **`try_files $uri $uri/ =404;`**  
     Tenta encontrar o arquivo ou diretório solicitado (`$uri`), redirecionando para um erro 404 caso não exista.

5. **`error_page 404 400 401 /error.html;`**  
   Define uma página de erro personalizada (`error.html`) para os códigos de erro 404 (não encontrado), 400 (requisição inválida) e 401 (não autorizado).

## Aprendizados
- **Configuração de portas e nomes de servidor**: Entendi como especificar em qual porta o Nginx escuta e como associá-lo a um nome de domínio ou endereço.
- **Servindo arquivos estáticos**: Aprendi a configurar um diretório raiz para hospedar arquivos e a habilitar a listagem automática de conteúdos.
- **Gerenciamento de erros**: Descobri como redirecionar erros HTTP para uma página personalizada, melhorando a experiência do usuário.
- **Uso do `try_files`**: Compreendi como o Nginx pode tentar diferentes opções para resolver uma requisição antes de retornar um erro.