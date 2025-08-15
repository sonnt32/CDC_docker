# Project Setup Guide

This guide provides step-by-step instructions to set up and run the project using Docker Compose and REST API. Please follow exactly to avoid issues.

## 1. Prerequisites
Before starting, ensure you have:
- Docker (latest stable version)
- Docker Compose (bundled with Docker Desktop)
- Git
- cURL or Postman for API testing

Check installation:
docker --version
docker compose version
git --version

## 2. Directory Structure
project-root/
├── docker-compose.yml       # Docker Compose configuration
├── README.md                 # This setup guide
├── restapi/                  # REST API source code
│   ├── app.py
│   ├── requirements.txt
│   └── ...
└── other-components/         # Additional services or configs

## 3. Setup Instructions
1. Clone the repository:
git clone <your-repo-url>
cd project-root

2. Build and start services:
docker compose up -d --build

3. Verify containers are running:
docker compose ps

4. Check logs (optional):
docker compose logs -f

## 4. REST API Usage

Once all containers are up and running, you can test the REST API using **Postman**.

1. **Open Postman**.
2. Create a new request.
3. Select the appropriate HTTP method (e.g., GET, POST, PUT, DELETE).
4. Run the connector using **create-connector-oracle.json** to connect Oracle to Kafka, and **create-connector-sql-server.json** to connect MS SQL Server to Kafka.

## 5. Stopping the Services
To stop and remove containers:
docker compose down

To stop but keep containers:
docker compose stop

## 6. Additional Notes
- Update `.env` file before starting services if needed.
- Make sure ports in `docker-compose.yml` are not used by other applications.
- If any container fails to start, check its logs with: docker compose logs <service-name>
