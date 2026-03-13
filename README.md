# Forgejo Docker Compose Setup

This repository contains a ready-to-use Docker Compose setup for [Forgejo](https://forgejo.org/), a self-hosted lightweight software forge. The setup includes built-in integration for [Traefik](https://traefik.io/) as a reverse proxy, and pre-configured labels for OAuth2 protection.

## Prerequisites

Before starting, ensure you have the following installed and configured on your host system:

- **Docker** and **Docker Compose**
- **Existing Traefik Proxy:** A running Traefik instance configured to route traffic on a shared Docker network (default: `traefik_default`).
- **OAuth2 Middlewares:** This configuration expects Traefik middlewares named `auth-errors@docker` and `forward-auth@docker` to be defined (e.g., provided by an OAuth2-proxy container on the same network).

## Setup Instructions

1. **Clone the repository** (if you haven't already) and navigate to the directory:
   ```bash
   cd path/to/repo
   ```

2. **Prepare the environment variables file:**
   Copy the example environment file to `.env`:
   ```bash
   cp .env.example .env
   ```

3. **Generate security secrets:**
   You must generate secure random strings for `SECRET_KEY` and `INTERNAL_TOKEN`. You can do this using OpenSSL:
   ```bash
   openssl rand -hex 32
   ```
   Paste the generated values into your `.env` file.

4. **Configure your environment:**
   Open the `.env` file in your preferred text editor and adjust the variables as needed, particularly `FORGEJO_HOST`, `TRAEFIK_CERTRESOLVER`, and the Traefik network name.

5. **Start the service:**
   Run the following command to start Forgejo in the background:
   ```bash
   docker compose up -d
   ```

## Environment Variables

The `.env` file allows you to customize various aspects of the Forgejo instance. Below is a detailed list of the available variables:

| Variable | Default Value | Description |
|----------|---------------|-------------|
| `FORGEJO_HOST` | `forgejo.wandergeek.org` | The hostname for your Forgejo instance. Used by both Traefik and Forgejo config. |
| `FORGEJO_IMAGE` | `codeberg.org/forgejo/forgejo:14` | The Forgejo Docker image to use. It is recommended to pin to a specific digest for reproducibility. |
| `FORGEJO_CONTAINER_NAME` | `forgejo` | The name of the Forgejo Docker container. |
| `FORGEJO_VOLUME_NAME` | `forgejo_data` | The name of the Docker volume used to persist Forgejo data. |
| `USER_UID` | `1000` | The UID for the forgejo process inside the container. |
| `USER_GID` | `1000` | The GID for the forgejo process inside the container. |
| `SSH_EXPOSE_PORT` | `22` | The host port to expose for Forgejo's SSH server. Change this if port 22 is already in use on your host (e.g., `2222`). |
| `DB_TYPE` | `sqlite3` | The database backend to use. Options are `sqlite3` (default), `postgres`, or `mysql`. |
| `SECRET_KEY` | `changeme` | A secret key used by Forgejo for security. **Must be changed.** |
| `INTERNAL_TOKEN` | `changeme` | An internal token used by Forgejo for secure communication. **Must be changed.** |
| `DISABLE_REGISTRATION` | `true` | Whether to disable public user registration. |
| `REQUIRE_SIGNIN_VIEW` | `true` | Whether to require users to sign in to view any pages. |
| `TRAEFIK_NETWORK_NAME` | `traefik_default` | The name of the external Docker network that Traefik uses. |
| `TRAEFIK_CERTRESOLVER` | `myresolver` | The name of the Traefik certificate resolver to use for generating TLS certificates (e.g., Let's Encrypt). |
| `MAILER_ENABLED` | `false` | Set to `true` to enable email notifications. Requires additional mailer configuration if enabled. |

## Data Persistence

Forgejo data (including repositories, the SQLite database if used, and configuration) is persisted in a Docker volume named `forgejo_data` (configurable via `FORGEJO_VOLUME_NAME`).

## Accessing Forgejo

Once the container is running and Traefik has routed the domain and obtained certificates, you can access your Forgejo instance by navigating to `https://<FORGEJO_HOST>`.
