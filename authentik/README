# Authentik Docker Compose Setup

This repository contains a Docker Compose setup for deploying Authentik, an open-source identity and access management solution. The setup includes several services, each of which plays a specific role in the system. Below, you'll find a detailed description of each service, how they interact, and additional information about configuration practices.

## Services Overview

### 1. **PostgreSQL**

**Image:** `docker.io/library/postgres:12-alpine`

**Container Name:** `myapp-psql`

**Ports:** `5432`

**Description:**
PostgreSQL is a powerful, open-source relational database system. In this setup, PostgreSQL is used as the primary database for Authentik to store user data, configurations, and other necessary information.

**Configuration:**
- `POSTGRES_PASSWORD`: The password for the PostgreSQL superuser.
- `POSTGRES_USER`: The username for PostgreSQL (set to `authentik`).
- `POSTGRES_DB`: The database name (set to `authentik`).

**Volumes:**
- `database`: Persist data across container restarts and upgrades.

**Health Check:**
- Ensures that the PostgreSQL server is ready to accept connections.

### 2. **Redis**

**Image:** `docker.io/library/redis:7.0-alpine`

**Container Name:** `myapp-redis`

**Ports:** `6379`

**Description:**
Redis is an in-memory data structure store used as a database, cache, and message broker. In this setup, Redis is utilized by Authentik for caching and session management.

**Configuration:**
- `command`: Configures Redis to save data and set log level.

**Volumes:**
- `redis`: Persist Redis data across container restarts.

**Health Check:**
- Ensures that the Redis server is running and responsive.

### 3. **NGINX**

**Image:** `nginx:1.21-alpine`

**Container Name:** `myapp-nginx`

**Ports:**
- `8080` for HTTP
- `4430` for HTTPS

**Description:**
NGINX is a high-performance web server and reverse proxy. In this setup, NGINX is used to serve the Authentik application, handle SSL/TLS termination, and route traffic to the backend services.

**Configuration:**
- `volumes`: Mounts the NGINX configuration file.
- `depends_on`: Ensures that Redis and PostgreSQL are started before NGINX.

**Health Check:**
- Ensures that NGINX is up and serving requests.

### 4. **Authentik Server**

**Image:** `${AUTHENTIK_IMAGE:-ghcr.io/goauthentik/server}:${AUTHENTIK_TAG:-2023.10.7}`

**Container Name:** `myapp-server`

**Ports:**
- `9000` for HTTP
- `9443` for HTTPS

**Description:**
The Authentik server is the core application responsible for identity and access management. It handles authentication, authorization, and user management.

**Configuration:**
- `AUTHENTIK_REDIS__HOST`: Points to the Redis service.
- `AUTHENTIK_POSTGRESQL__HOST`: Points to the PostgreSQL service.
- `AUTHENTIK_POSTGRESQL__USER`, `AUTHENTIK_POSTGRESQL__NAME`, `AUTHENTIK_POSTGRESQL__PASSWORD`: Credentials for accessing the PostgreSQL database.

**Volumes:**
- `./media`: For media storage.
- `./custom-templates`: For custom templates.

**Health Check:**
- Ensures that the Authentik server is responsive and healthy.

### 5. **Authentik Worker**

**Image:** `${AUTHENTIK_IMAGE:-ghcr.io/goauthentik/server}:${AUTHENTIK_TAG:-2023.10.7}`

**Container Name:** `myapp-worker`

**Ports:**
- `9090` (example port for worker)

**Description:**
The Authentik worker performs background tasks and jobs such as processing authentication requests and handling scheduled tasks.

**Configuration:**
- `AUTHENTIK_REDIS__HOST`, `AUTHENTIK_POSTGRESQL__HOST`, `AUTHENTIK_POSTGRESQL__USER`, `AUTHENTIK_POSTGRESQL__NAME`, `AUTHENTIK_POSTGRESQL__PASSWORD`: Configuration details similar to the Authentik server.

**Volumes:**
- `/var/run/docker.sock`: For Docker socket access.
- `./media`: For media storage.
- `./certs`: For certificate storage.
- `./custom-templates`: For custom templates.

**Health Check:**
- Ensures that the worker is functioning and processing tasks.

## NGINX Configuration (`nginx.conf`)

The `nginx.conf` file configures the NGINX service and plays a crucial role in this setup. Here’s what it does:

- **Reverse Proxy:** NGINX is set up as a reverse proxy to forward requests to the Authentik server. This allows NGINX to handle incoming traffic and route it to the appropriate backend service.
- **SSL/TLS Termination:** NGINX manages SSL/TLS termination, meaning it handles the encryption and decryption of HTTPS traffic. This ensures secure communication between clients and the Authentik application.
- **Load Balancing and Caching:** Although not explicitly detailed in this configuration, NGINX can be extended to include load balancing and caching features to improve performance and reliability.

The configuration file is mounted into the NGINX container in read-only mode to prevent accidental modifications and ensure consistency.

## Using `.env` File

The `.env` file is used to manage environment variables for the Docker Compose setup. Here’s why it’s a best practice:

- **Separation of Concerns:** It separates configuration from code, allowing you to manage environment-specific settings without modifying the Docker Compose file directly.
- **Security:** Sensitive information such as database passwords and API keys are stored in a separate file. This helps prevent accidental exposure and simplifies secure management.
- **Flexibility:** Environment variables can be easily updated without altering the Docker Compose file. This makes it easier to change configurations for different environments (e.g., development, testing, production).

The `.env` file provides a convenient way to define and manage variables used throughout the Docker Compose setup, enhancing maintainability and security.

## Service Collaboration

- **NGINX** acts as a reverse proxy, routing incoming requests to the Authentik server. It handles SSL/TLS termination, providing a secure connection to the Authentik application.
- **Authentik Server** communicates with **PostgreSQL** for data storage and with **Redis** for caching and session management.
- **Authentik Worker** performs background tasks and relies on the **PostgreSQL** database and **Redis** for data access and caching.
- **PostgreSQL** and **Redis** are crucial components, providing the necessary data storage and caching capabilities for Authentik.

## Setting up

1. **Start the Services:**

   ```sh
   docker-compose up -d
   ```

2. **Access Authentik:**

   Navigate to `http://localhost:8080` or `https://localhost:4430` in your browser to access the Authentik application.

. **Stopping the Services:**

   ```sh
   docker-compose down
   ```

## Troubleshooting

- **Logs:** Check logs for each service using `docker-compose logs <service-name>` to diagnose issues.
- **Database Issues:** Ensure PostgreSQL and Redis are running correctly. Use `docker-compose exec <service-name> psql` or `redis-cli` to interact with the databases.
- **Configuration Issues:** Verify the configuration files and environment variables.

