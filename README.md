# Bieszczady Infrastructure

Docker Compose configurations for services deployed on Coolify for the **bieszczady.plus** domain.

## Services

| Service | Description | Domain |
|---------|-------------|--------|
| **Shlink** | URL shortener with analytics | `links.bieszczady.plus` |
| **Shlink Web** | Web UI for managing short URLs | `shlink.bieszczady.plus` |

## Deployment Instructions (Coolify)

### 1. Create a New Docker Compose Resource

In Coolify, create a new resource and select **Docker Compose**.

### 2. Connect to GitHub Repository

Connect to this GitHub repository when prompted.

### 3. Configure Compose File

Set the compose file path to: `docker-compose.yml`

### 4. Add Environment Variables

Add the following environment variables in the Coolify UI (you can copy from `.env.example`):

```bash
DEFAULT_DOMAIN=links.bieszczady.plus
DB_PASSWORD=<generate-a-strong-password>
GEOLITE_LICENSE_KEY=<optional-see-below>
```

> **Note:** You can get a free GeoLite2 license key from [MaxMind](https://dev.maxmind.com/geoip/geolite2-free-geolocation-data) for visitor geolocation.

### 5. Configure Domains

In Coolify, configure domains for each service:

| Service | Domain | Port |
|---------|--------|------|
| `shlink` | `links.bieszczady.plus` | 8080 |
| `shlink-web` | `shlink.bieszczady.plus` | 80 |

### 6. Deploy

Click the Deploy button and wait for the services to start.

## Post-Deployment Steps

### Generate API Key

Once deployed, generate an API key for the Shlink web client:

```bash
docker exec -it <shlink-container-id> shlink api-key:generate
```

### Configure the Web Client

1. Visit `https://shlink.bieszczady.plus`
2. Enter the Shlink server URL: `https://links.bieszczady.plus`
3. Paste the API key generated above
4. Start creating short URLs!

## Security Reminders

- Never commit `.env` files with real secrets
- Generate strong passwords for `DB_PASSWORD`
- Store API keys securely in Coolify's environment variables

## License

MIT
