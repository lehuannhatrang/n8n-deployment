# Project Setup

Follow these steps to set up and run the project:

1. **Copy the environment file:**
   ```bash
   cp .env.sample .env
   ```

2. **Edit the `.env` file:**
   Open `.env` in your favorite editor and update the environment variables as needed.
   ```bash
   nano .env
   # or use vim, code, etc.
   ```

3. **Load the environment variables:**
   ```bash
   source .env
   ```

4. **Create the data directory:**
   This directory will be used to store local files and persistent data.
   ```bash
   mkdir -p local-files
   ```

5. **Start the services using Docker Compose:**
   ```bash
   docker-compose up -d
   ```

This will start all the services defined in the `docker-compose.yaml` file in detached mode.

---

**Accessing n8n Front End**

Once the services are running, you can access the n8n front end via the following link:
```
https://{SUBDOMAIN}.{DOMAIN_NAME}
```