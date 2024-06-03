# **Create a container entypoint init script for MinIO**


This `entrypoint.sh` scripts allows to initialize the MinIO container with credentials fetched from the enviornment variables.


<br><br>


---
**Contents**
- [**Create a container entypoint init script for MinIO**](#create-a-container-entypoint-init-script-for-minio)
  - [**The Entrypoint script**](#the-entrypoint-script)
  - [**Docker-Compose**](#docker-compose)


---

## **The Entrypoint script**

Create a new file `entrypoint.sh`

```bash
#!/bin/sh

# Starting MinIO server
minio server /data --console-address ':9001' &

# Delay to ensure MinIO server starts up
sleep 3

# Setting up MinIO client alias for admin
mc alias set myminio http://localhost:9000 ${MINIO_ROOT_USER} ${MINIO_ROOT_PASSWORD}

# Creating bucket
mc mb myminio/${MINIO_BUCKET}

# Adding non-admin user and setting permissions
mc admin user add myminio ${AWS_ACCESS_KEY_ID} ${AWS_SECRET_ACCESS_KEY}

# Ensure the policy exists and then attach it
mc admin policy add myminio readwrite /etc/minio/policies/readwrite.json
mc admin policy attach myminio readwrite --user ${AWS_ACCESS_KEY_ID}

# Keeping the container running
tail -f /dev/null
```

## **Docker-Compose**

- Grant permissions to the file
```sh
chmod +x entrypoint.sh
```

- Mount it as volume in the `docker-compose.yml` and make sure to pass the env variables to the container.



```yaml
  minioserver:
    # MinIO: Lightweight AWS S3 local file storage
    image: minio/minio
    ports:
      - "9000:9000"
      - "9013:9001"
    container_name: minio
    command: server /data --console-address ":9001"
    volumes:
      - ./minio-entrypoint.sh:/minio_entrypoint.sh 
      - minio-data:/data
    networks:
      - backend
    entrypoint: /minio_entrypoint.sh
    restart: always
    environment:
      - MINIO_ROOT_USER=${MINIO_ROOT_USER}
      - MINIO_ROOT_PASSWORD=${MINIO_ROOT_PASSWORD}
      - MINIO_BUCKET=${MINIO_BUCKET}
      - AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
      - AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
```

This path
```yaml
    volumes:
      - ./minio-entrypoint.sh:/minio_entrypoint.sh 
```

Is only valid if the entrypoint is in the same directory as the docker-compose file.
Ensure to specify the right path to the entrypoint file if it is somewhere else.  
