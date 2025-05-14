# Flask + NGINX + Docker Compose Project

## Overview

This project demonstrates how to deploy two Flask-based backend services behind an NGINX reverse proxy using Docker Compose. It showcases container orchestration, inter-container networking, and load balancing using NGINX.

---

## Architecture

```
             ┌────────────┐
             │   Client   │
             └────┬───────┘
                  │  HTTP Request (http://localhost:8080)
                  ▼
            ┌────────────┐
            │   NGINX    │  <── Reverse Proxy (listens on port 8080)
            └────┬───────┘
        ┌────────┴────────┐
        ▼                 ▼
 ┌────────────┐     ┌────────────┐
 │  Backend 1 │     │  Backend 2 │
 │ (Flask)    │     │ (Flask)    │
 │ Port 5001  │     │ Port 5002  │
 └────────────┘     └────────────┘
```

---

## Components

### 1. Flask Backends

* Two simple Python Flask applications
* Each listens on a different port (5001 and 5002)
* Dockerized using a `Dockerfile`:

  ```Dockerfile
  FROM python:3.10
  WORKDIR /app
  COPY app.py .
  RUN pip install flask
  CMD ["python", "app.py"]
  ```

### 2. NGINX Reverse Proxy

* Routes incoming traffic on port 8080 to backend services
* Configured to load balance between the two backends:

  ```nginx
  http {
    upstream flask_servers {
      server backend1:5001;
      server backend2:5002;
    }

    server {
      listen 8080;

      location / {
        proxy_pass http://flask_servers;
      }
    }
  }
  ```

### 3. Docker Compose

Manages and connects the containers:

```yaml
services:
  backend1:
    build: ./backend1
    ports:
      - "5001:5001"

  backend2:
    build: ./backend2
    ports:
      - "5002:5002"

  nginx:
    image: nginx
    ports:
      - "8080:8080"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    depends_on:
      - backend1
      - backend2
```

---

## Running the Project

1. Clone the repository and navigate to the project directory:

   ```bash
   git clone <repo-url>
   cd nginx_project
   ```
2. Run the application:

   ```bash
   docker-compose up --build
   ```
3. Access the app at [http://localhost:8080](http://localhost:8080)

---

## What I Learned

* Building Docker images for Python apps
* Running Flask apps inside containers
* Setting up NGINX as a reverse proxy for load balancing
* Orchestrating containers with Docker Compose
* Container-to-container communication via Docker network

---

## Future Enhancements

* Use Gunicorn instead of Flask's dev server for production
* Add HTTPS with Let's Encrypt
* Set up logging and monitoring
* Scale backend replicas with Docker Compose or Kubernetes

---

## Credits

Created by Abhishek as part of DevOps learning journey.

