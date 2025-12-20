# Simple Notes App (Docker Lab)

This is a simple notes app built with React (Frontend) and Django (Backend). The goal of this project is to containerize this application using Docker and orchestrate it using Docker Compose.

## Prerequisites

* [Git](https://git-scm.com/downloads)
* [Docker Desktop](https://www.docker.com/products/docker-desktop) (or Docker Engine + Compose)

---

## Installation & Setup Guide

Follow the steps below to set up the project. You will need to create the necessary Docker configuration files manually.

### Step 1: Clone the Repository

Clone the project source code to your local machine:

```bash
git clone [https://github.com/LondheShubham153/django-notes-app.git](https://github.com/SIIM21-force/dockerized-django-notes-app.git)
cd dockerized-django-notes-app
```

### Step 2: Configure Environment Variables

Create a `.env` file in the root directory. This file is required for the application to connect to the database securely.

Copy and paste the following content into your new `.env` file:

```env
DEBUG=True
SECRET_KEY=secret_key_for_testing_purposes
DATABASE_NAME=test_db
DATABASE_USER=root
DATABASE_PASSWORD=root
DATABASE_HOST=db
DATABASE_PORT=3306
```

### Step 3: Create the Dockerfile (Task)

**Goal:** Create a `Dockerfile` in the root directory to containerize the Django backend.

**Requirements for your Dockerfile:**
* Use `python:3.9` as the base image.
* Set the working directory to `/app/backend`.
* Install system dependencies required for `mysqlclient` (`gcc`, `default-libmysqlclient-dev`, `pkg-config`).
* Copy `requirements.txt` and install Python dependencies.
* Copy the application source code.
* Expose port `8000`.

### Step 4: Create Docker Compose (Task)

**Goal:** Create a `docker-compose.yml` file in the root directory to orchestrate the services.

**Requirements for your Compose file:**

* **Service: `nginx`**
    * Build context: `./nginx`
    * Map Port: `80` to host `80`.
    * Depends on: `django`.

* **Service: `db`**
    * Image: `mysql:latest` (or specific version).
    * Map Port: `3306` to `3306`.
    * Volume: Map `./mysql-data` to `/var/lib/mysql` (for data persistence).
    * Environment: Use root password matching your `.env` file.
    * Healthcheck: Configure a ping check to ensure DB is ready before Django starts.

* **Service: `django`**
    * Build context: Current directory (`.`).
    * Map Port: `8000` to `8000`.
    * Environment File: Link the `.env` file created in Step 2.
    * Command: Run migrations and start Gunicorn on `0.0.0.0:8000`.
    * Depends on: `db`.
    
### Step 5: Build and Run

Once you have created your `Dockerfile` and `docker-compose.yml`, run the following commands to build the images and start the application:

**1. Build and Start Containers**
```bash
docker-compose up -d --build
```

**2. Verify Containers are Running
```bash
docker ps
```

**3. Check Application Logs (Debug)
```bash
docker-compose logs -f
```

### Step 6: Verify Deployment

* **Frontend:** Visit `http://localhost`
* **Backend API:** Visit `http://localhost:8000/admin`

### Troubleshooting

If you encounter permission issues with the entrypoint scripts or build failures, ensure:
* You have run `sudo apt-get update` if you are modifying system packages.
* Your `requirements.txt` is in the correct location relative to the `COPY` command in your `Dockerfile`.
