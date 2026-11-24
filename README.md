# PayLink

PayLink is a digital bill payment and e-wallet backend system built with Python, FastAPI, and PostgreSQL.

## Requirements

- Docker
- Docker Compose

## Run the Project

Clone the repository:

```bash
git clone https://github.com/fadlmagdy/PayLink.git
cd PayLink
```

Build and start the application and database:

```bash
docker compose up --build
```

The API will be available at:

- http://localhost:8000
- API documentation: http://localhost:8000/docs

## Health Check

Open:

```text
http://localhost:8000/health
```

Expected response:

```json
{
  "status": "healthy"
}
```

## Stop the Project

```bash
docker compose down
```

To stop the project and remove the database volume:

```bash
docker compose down -v
```

**Note:** The last command permanently deletes the local PostgreSQL data volume.
