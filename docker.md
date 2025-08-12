# Docker tips

## SELinux permission issue

For Fedora or any SELinux-enabled systems, adding `:Z` to the volume mount tells Docker to set the correct SELinux context on the folder so the container has permission to write to it.

Example:

```shell
docker run -d -p 9000:9000 -p 9001:9001 \
  -e "MINIO_ROOT_USER=minioadmin" \
  -e "MINIO_ROOT_PASSWORD=minioadmin" \
  -v /home/qichen/cbim-minio:/data:Z \
  --name minio \
  quay.io/minio/minio server /data --console-address ":9001"
```

Or in `docker-compose.yml`:

```shell
  keycloak:
    image: quay.io/keycloak/keycloak:24.0.2
    environment:
      KEYCLOAK_ADMIN: ${KEYCLOAK_ADMIN}
      KEYCLOAK_ADMIN_PASSWORD: ${KEYCLOAK_ADMIN_PASSWORD}
      # KC_DB: postgres
      # KC_DB_URL: jdbc:postgresql://postgres:5432/${POSTGRES_DB}
      # KC_DB_USERNAME: ${KEYCLOAK_DB_USERNAME}
      # KC_DB_PASSWORD: ${KEYCLOAK_DB_PASSWORD}
    ports:
      - "8080:8080"
    # depends_on:
    #   - postgres
    command: ["start-dev", "--import-realm"]
    volumes:
      - keycloak_data:/opt/keycloak/data
      - ./keycloak:/opt/keycloak/data/import:Z
```

The `:Z` applies to the host directory you’re mounting (`/home/qichen/cbim-minio`)
