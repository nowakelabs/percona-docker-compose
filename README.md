# Percona MySQL & PMM Docker Compose Stack

This repository provides a ready-to-use Docker Compose configuration for running a local Percona MySQL server with integrated Percona Monitoring and Management (PMM) for database monitoring and metrics.

---

## Features

- **Percona MySQL**: Production-grade MySQL server.
- **Percona PMM Server**: Full-featured monitoring and observability for MySQL.
- **Percona PMM Client**: Automatically registers and monitors the MySQL instance.
- **Persistent Data**: MySQL and PMM data are stored in local volumes for durability.
- **Easy Access**: PMM web UI available on your host at custom ports.
- **Automatic Setup**: No manual registration required.

---

## Quick Start

1. **Clone this Repository**  
   ```sh
   git clone <this-repo>
   cd <repo-directory>
   ```

2. **Start the Stack**  
   ```sh
   docker-compose up -d
   ```

3. **Access Services**
   - **MySQL**:  
     - Host: `localhost`
     - Port: `3306`
     - Username: `root`
     - Password: `root`
   - **PMM Web UI**:  
     - URL: [https://localhost:8443](https://localhost:8443)  
     - (Or HTTP: [http://localhost:8080](http://localhost:8080))
     - Default login:  
       - Username: `admin`
       - Password: `admin`

---

## File Structure

```
.
├── docker-compose.yaml
├── mysql-data/      # MySQL data (created automatically)
├── pmm-data/        # PMM server data (Docker volume)
└── README.md
```

---

## Configuration

- **MySQL root password**: Set in `docker-compose.yaml` under `MYSQL_ROOT_PASSWORD`.  
  Default: `root`
- **PMM admin credentials**: Default `admin`/`admin`
- **Ports**:
  - MySQL: `3306`
  - PMM Web UI: `8080` (HTTP), `8443` (HTTPS)

---

## Customization

- **Change MySQL Password**:  
  Edit the `MYSQL_ROOT_PASSWORD` value in `docker-compose.yaml` and update the corresponding password in the `pmm-client` service under `pmm-admin add mysql`.
- **Data Locations**:  
  MySQL data is stored in `./mysql-data` (relative to this directory). PMM data is stored in a Docker-managed volume (`pmm-data`).
- **Environment Variables**:  
  To use a `.env` file for credentials, adjust the compose file to reference variables as `${MYSQL_ROOT_PASSWORD}` etc.

---

## Troubleshooting

- **PMM Web UI not accessible?**
  - Make sure ports `8080` and `8443` are not used by other applications.
  - Check container logs:  
    ```sh
    docker-compose logs percona-pmm
    ```
- **MySQL connection issues?**
  - Ensure the `mysql-data` directory is writable by Docker.
  - Check MySQL logs:  
    ```sh
    docker-compose logs percona-mysql
    ```
- **PMM client not registering?**
  - The stack will retry registration on each start and force re-registration if needed.
  - Ensure both PMM server and MySQL are healthy.
- **FATAL: /srv is not writable for pmm user**
  - This means the `pmm-data` volume is not owned by UID 1000 (the `pmm` user in the container).
  - To fix, run:
    ```sh
    docker run --rm -v aparavi_docker_percona_pmm-data:/srv alpine chown -R 1000:1000 /srv
    ```
    Replace `aparavi_docker_percona_pmm-data` with your actual volume name if different (check with `docker volume ls`).
  - On Windows, this is the most reliable fix.

---

## Stopping and Resetting

- **Stop the stack**  
  ```sh
  docker-compose down
  ```
- **Remove all data (reset everything!)**  
  ```sh
  docker-compose down -v
  ```

---

## Security Notice

- **Do not use default passwords in production!**
- For production, use secure passwords and consider using Docker secrets or environment variables.

---

## Credits

- [Percona MySQL](https://www.percona.com/software/mysql-database/percona-server)
- [Percona PMM](https://www.percona.com/software/database-tools/percona-monitoring-and-management)
