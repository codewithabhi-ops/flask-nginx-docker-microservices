# NGINX Reverse Proxy with Flask Backends

This project sets up a reverse proxy using NGINX to route traffic to two different Flask backend services using Docker Compose.

## Routes
- `http://localhost/app1/` → Backend 1
- `http://localhost/app2/` → Backend 2

## How to Run

```bash
docker-compose up --build

