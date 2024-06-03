
# **Initialize a Postgres container from an SQL script**

An SQL script is passed as entrypoint to the container to register the user and ensure the db/tables are created if they don't already exist. 


<br><br>


---
**Contents**
- [**Initialize a Postgres container from an SQL script**](#initialize-a-postgres-container-from-an-sql-script)
  - [**The SQL script**](#the-sql-script)
  - [**Docker-Compose**](#docker-compose)


---

## **The SQL script**

Example of an `init.sql` file for a database named `postgresdb`

```sql

-- Create the `postgresdb` database if it doesn't exist
SELECT 'CREATE DATABASE postgresdb' WHERE NOT EXISTS (SELECT FROM pg_database WHERE datname = 'postgresdb')\gexec

-- Connect to the `postgresdb` database
\c postgresdb;

-- Create the locations table if it doesn't exist
CREATE TABLE IF NOT EXISTS locations (
    id VARCHAR(255) PRIMARY KEY,
    name VARCHAR(255),
    address VARCHAR(255),
    latitude FLOAT,
    longitude FLOAT,
    city VARCHAR(255)
);

-- Create the postgresdb_user and grant privileges
DO
$$
BEGIN
    IF NOT EXISTS (
        SELECT
        FROM   pg_catalog.pg_roles
        WHERE  rolname = 'postgresdb_user') THEN

        CREATE ROLE postgresdb_user LOGIN PASSWORD 'postgresdb_password';
    END IF;
END
$$;

GRANT ALL PRIVILEGES ON DATABASE postgresdb TO postgresdb_user;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO postgresdb_user;
```



## **Docker-Compose**

- The file must be available at `/docker-entrypoint-initdb.d/` in the container

- Mount it as volume in the `docker-compose.yml` and make sure to pass the env variables to the container.



```yaml
  postgres:
    image: postgres:13
    environment:
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_DB: ${POSTGRES_DB}
    volumes:
      - ./mount/db:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "${POSTGRES_USER}", "-d", "${POSTGRES_DB}"]
      interval: 5s
      retries: 5
    restart: always
    networks:
      - backend
    ports:
      - "5432:5432"
```

This path
```yaml
    volumes:
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
```

Is only valid if the `init.sql` is in the same directory as the docker-compose file.
Ensure to specify the right path to the entrypoint file if it is somewhere else.  
