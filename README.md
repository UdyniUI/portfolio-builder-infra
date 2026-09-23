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

To enable wireframe interpretation, set `OPENAI_API_KEY` in `.env` before starting the
API. The default vision-capable model is `gpt-4o-mini`; override
`OPENAI_WIREFRAME_MODEL` only when needed.

Fresh databases include the full `pending → processing → completed/failed` extraction
status constraint. To update an existing development volume once, run:

```bash
docker compose exec -T postgres psql -U portfoliosos -d portfoliosos_dev \
  -f /dev/stdin < ../portfolio-builder-api/scripts/migrate_resume_status.sql
docker compose exec -T api psql postgresql://portfoliosos:dev_password_123@postgres:5432/portfoliosos_dev \
  -f scripts/migrate_wireframe_import.sql
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
