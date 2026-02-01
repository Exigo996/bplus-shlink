# Bieszczady Infrastructure Setup

## Project Goal

Create a `bieszczady-infra` repository to store Docker Compose configurations for services deployed on Coolify. Start with Shlink URL shortener for the `bieszczady.plus` domain.

## Repository Structure

```
bplus-shlink/
├── README.md
├── .gitignore
├── docker-compose.yml
└── .env.example
```

## Tasks

### 1. Initialize the repository

Create the folder structure as shown above.

### 2. Create `.gitignore`

```
.env
*.env.local
.DS_Store
```

### 3. Create `docker-compose.yml`

```yaml
services:
  shlink:
    image: shlinkio/shlink:stable
    restart: unless-stopped
    environment:
      - DEFAULT_DOMAIN=${DEFAULT_DOMAIN}
      - IS_HTTPS_ENABLED=true
      - GEOLITE_LICENSE_KEY=${GEOLITE_LICENSE_KEY:-}
      - DB_DRIVER=postgres
      - DB_HOST=shlink-db
      - DB_NAME=shlink
      - DB_USER=shlink
      - DB_PASSWORD=${DB_PASSWORD}
    depends_on:
      shlink-db:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/rest/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  shlink-db:
    image: postgres:16-alpine
    restart: unless-stopped
    environment:
      - POSTGRES_DB=shlink
      - POSTGRES_USER=shlink
      - POSTGRES_PASSWORD=${DB_PASSWORD}
    volumes:
      - shlink-db-data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U shlink -d shlink"]
      interval: 10s
      timeout: 5s
      retries: 5

  shlink-web:
    image: shlinkio/shlink-web-client:stable
    restart: unless-stopped
    depends_on:
      - shlink

volumes:
  shlink-db-data:
```

### 4. Create `.env.example`

```
# Domain for short URLs (without https://)
DEFAULT_DOMAIN=links.bieszczady.plus

# Database password - generate a strong one
DB_PASSWORD=change-me-to-a-strong-password

# Optional: GeoLite2 license key for visitor geolocation
# Get one free at: https://dev.maxmind.com/geoip/geolite2-free-geolocation-data
GEOLITE_LICENSE_KEY=
```

### 5. Create `README.md`

Write a README that includes:

- Project description (infrastructure configs for bieszczady.plus services on Coolify)
- List of services with brief descriptions
- Deployment instructions for Coolify:
  1. Create new Docker Compose resource
  2. Connect to this GitHub repo
  3. Set compose file path to `docker-compose.yml`
  4. Add environment variables in Coolify UI (copy from `.env.example`)
  5. Configure domains in Coolify:
     - `shlink` service → `links.bieszczady.plus` (port 8080)
     - `shlink-web` service → `shlink.bieszczady.plus` (port 80)
  6. Deploy
- Post-deployment steps:
  - Generate API key: `docker exec -it <container> shlink api-key:generate`
  - Configure the web client with the API key

## Deployment Notes for Coolify

When deploying in Coolify:

- **Shlink API/Shortener**: Expose on `links.bieszczady.plus`, port 8080
- **Shlink Web UI**: Expose on `shlink.bieszczady.plus`, port 80
- The web client needs to be configured after deployment with the Shlink server URL and API key (done through its UI on first visit)

## Security Reminders

- Never commit `.env` files with real secrets
- Generate strong passwords for `DB_PASSWORD`
- API keys should be stored securely in Coolify's environment variables
