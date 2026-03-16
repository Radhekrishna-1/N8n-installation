# n8n Docker Commands and Data Directory Setup Guide

This document lists all the necessary commands to set up the secure data volume directory for n8n and the essential Docker Compose commands for operational management.

## 1. Create and Secure the Data Directory (Volume Mount)

n8n uses the `data` directory to store its local databases, workflow executions, and user credentials. The n8n Docker container runs internally as a non-root user (specifically, `node` with User ID `1000`).

To ensure the container can read and write to this local directory—and to protect it from other users on your EC2 host—you must create the folder and set strict permissions.

Run these commands from within your `n8n` installation directory (e.g., `/home/username/n8n-installation`):

```bash
# 1. Create the persistent data directory for the Docker volume
mkdir -p data

# 2. Change ownership to UID 1000 and GID 1000
# (This matches the 'node' user inside the n8n container)
sudo chown -R 1000:1000 data/

# 3. Restrict permissions so only the owner (UID 1000) can read/write/execute
# (This prevents other users on the host from reading your n8n credentials)
sudo chmod -R 700 data/

# 4. Verify the directory was created with the correct permissions
ls -ld data/
# Expect output similar to: drwx------ 2 1000 1000 4096 Mar 16 12:00 data/
```

---

## 2. Docker Compose Operational Commands

Ensure you are inside the directory containing your `docker-compose.yml` and `.env` files before running these commands.

### Starting and Stopping the Service

**Start the n8n container in the background (Detached mode):**
```bash
sudo docker compose up -d
```

**Stop the n8n container gracefully:**
```bash
sudo docker compose down
```

**Stop the container AND remove volumes (WARNING: This deletes local non-database data!):**
```bash
sudo docker compose down -v
```

### Monitoring and Troubleshooting

**View real-time logs (tailing):**
*(Crucial for checking database connection errors on startup)*
```bash
sudo docker compose logs -f
```
*(Press `Ctrl+C` to exit the log view)*

**View the last 100 lines of logs:**
```bash
sudo docker compose logs --tail=100
```

**Check the status of running containers:**
```bash
sudo docker compose ps
```

### Applying Configuration Changes

**Restart the container to apply changes made to your `.env` file:**
```bash
sudo docker compose restart
```

**Force recreate the container (if you changed the `docker-compose.yml` file):**
```bash
sudo docker compose up -d --force-recreate
```

### Updating n8n

To safely update your n8n instance to the latest version specified by the `latest` tag in your compose file:

```bash
# 1. Pull the newest image from the Docker registry
sudo docker compose pull n8n

# 2. Stop the current running container
sudo docker compose down

# 3. Start the newly downloaded container
sudo docker compose up -d
```
