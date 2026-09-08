# PortfolioOS Infrastructure

Local PostgreSQL, Redis, and API services for PortfolioOS.

## Start locally

Docker Desktop must be running. Then:

```bash
cp .env.example .env
docker compose up --build -d
docker compose ps
```

Verify the API at `http://localhost:8000/health` and GraphQL at
`http://localhost:8000/graphql`.

Local résumé files are kept in the `resume_uploads` Docker volume. Their internal
references and extraction status are stored in PostgreSQL.

Fresh databases include the full `pending → processing → completed/failed` extraction
status constraint. To update an existing development volume once, run:

```bash
docker compose exec -T postgres psql -U portfoliosos -d portfoliosos_dev \
  -f /dev/stdin < ../portfolio-builder-api/scripts/migrate_resume_status.sql
```

To start only the data services while running FastAPI from a local virtual environment:

```bash
docker compose up -d postgres redis
```

## Stop locally

```bash
docker compose down
```

Use `docker compose down --volumes` only when intentionally resetting local database data.
