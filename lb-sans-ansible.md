# Exemple de load balancer sans Ansible
---

## 🧱 Architecture

```plaintext
[Docker Compose]
   ├── loadbalancer (Nginx en reverse proxy)
   ├── server1 (Nginx avec page personnalisée)
   └── server2 (Nginx avec page personnalisée)
```

---

## 📁 Structure du projet

```plaintext
project/
├── docker-compose.yml
├── loadbalancer/
│   └── nginx.conf
├── server1/
│   ├── Dockerfile
│   └── index.html
├── server2/
│   ├── Dockerfile
│   └── index.html
```

---

## 📦 `docker-compose.yml`

```yaml
version: '3.8'

services:
  loadbalancer:
    image: nginx:alpine
    container_name: loadbalancer
    ports:
      - "8080:80"
    volumes:
      - ./loadbalancer/nginx.conf:/etc/nginx/nginx.conf:ro
    depends_on:
      - server1
      - server2
    networks:
      - lbnet

  server1:
    build: ./server1
    container_name: server1
    networks:
      - lbnet

  server2:
    build: ./server2
    container_name: server2
    networks:
      - lbnet

networks:
  lbnet:
    driver: bridge
```

---

## ⚙️ `loadbalancer/nginx.conf`

```nginx
events {}

http {
  upstream backend {
    server server1:80;
    server server2:80;
  }

  server {
    listen 80;

    location / {
      proxy_pass http://backend;
    }
  }
}
```

---

## 📝 `server1/index.html`

```html
<!DOCTYPE html>
<html>
<head><title>Serveur 1</title></head>
<body><h1>Bienvenue sur le Serveur 1</h1></body>
</html>
```

## 📝 `server2/index.html`

```html
<!DOCTYPE html>
<html>
<head><title>Serveur 2</title></head>
<body><h1>Bienvenue sur le Serveur 2</h1></body>
</html>
```

---

## 🛠️ `server1/Dockerfile` et `server2/Dockerfile`

```Dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
```

---

## 🚀 Lancement

Dans le dossier `project/`, exécute :

```bash
docker-compose up --build -d
```

---

## ✅ Test du Load Balancer

```bash
curl http://localhost:8080
curl http://localhost:8080
curl http://localhost:8080
```

