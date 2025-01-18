# MkDocs Material with Docker and Nginx

This project sets up a documentation site using MkDocs Material, builds it with Docker, and serves it using Nginx. It's designed to make documentation deployment simple and efficient.

> **Important Security Note:** This connection is not protected by an SSL certificate. For enhanced security, please implement HTTPS using a certificate from a provider like Let's Encrypt.

## Project structure

``` 
├── compose.yml
├── mkdocs-project
│   ├── docs
│   │   └── index.md
│   └── mkdocs.yml
├── nginx
│   └── nginx.conf
```

## Deployment with Docker, Nginx Reverse Proxy and SSL

For secure publishing on the internet, it's recommended to use a reverse proxy with SSL/TLS encryption. Here's a general approach:

1. Set up a reverse proxy server in my case I'm using Nginx on the same server.
2. Configure the reverse proxy to forward requests to your Docker container's port 8080.
3. Obtain an SSL certificate from a provider like Let's Encrypt.
4. Configure the reverse proxy to use the SSL certificate and handle HTTPS connections.
5. Allow in your firewall incoming traffic 443 (HTTPS).

This setup will provide a secure, encrypted connection between users and your documentation site when accessed over the internet.

Example Nginx reverse proxy configuration (place this on your host machine, not inside the Docker container):

```nginx
server {
    listen 80;
    server_name your-domain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl;
    server_name your-domain.com;

    ssl_certificate /path/to/your/fullchain.pem;
    ssl_certificate_key /path/to/your/privkey.pem;

    location / {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Remember to replace 'your-domain.com' with your actual domain name and update the SSL certificate paths accordingly.

This addition provides guidance on how to securely deploy the documentation site on the internet using a reverse proxy with SSL, including a sample Nginx configuration for reference.
