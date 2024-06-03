
# **Register a DB in PGAdmin without having to use the UI**

A config file mounted in the PGAdmin container to register a datbase connection.


<br><br>


---
**Contents**
- [**Register a DB in PGAdmin without having to use the UI**](#register-a-db-in-pgadmin-without-having-to-use-the-ui)
  - [**The JSON file**](#the-json-file)
  - [**Docker-Compose**](#docker-compose)


---

## **The JSON file**

Example of an `servers.json` file for a database named `postgresdb`

```json
{
  "Servers": {
    "1": {
      "Name": "Postgresdb",
      "Group": "Servers",
      "Host": "user-postgresdb",
      "Port": 5432,
      "MaintenanceDB": "postgresdb",
      "Username": "postgresdb_user",
      "Password": "postgresdb_password",
      "SSLMode": "prefer"
    }
  }
}
```



## **Docker-Compose**

- The file must be available at `/pgadmin4` in the container

- Mount it as volume in the `docker-compose.yml` and make sure to pass the env variables to the container.



```yaml
  pgadmin:
    image: dpage/pgadmin4
    volumes:
      - ./servers.json:/pgadmin4/servers.json
    ports:
      - "5050:80"
    depends_on:
      - user-postgres
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@example.com
      PGADMIN_DEFAULT_PASSWORD: password
      PGADMIN_CONFIG_SERVER_MODE: "False"
    networks:
      - backend

```

This path
```yaml
    volumes:
      - ./servers.json:/pgadmin4/servers.json
```

Is only valid if the `servers.json` is in the same directory as the docker-compose file.
Ensure to specify the right path to the entrypoint file if it is somewhere else.  
