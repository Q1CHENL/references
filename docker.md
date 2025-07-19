# Docker tips

## SELinux permission issue

Adding `:Z` to the volume mount tells Docker to set the correct SELinux context on the folder so the container has permission to write to it.

Example:

```shell
docker run -d -p 9000:9000 -p 9001:9001 \
  -e "MINIO_ROOT_USER=minioadmin" \
  -e "MINIO_ROOT_PASSWORD=minioadmin" \
  -v /home/qichen/cbim-minio:/data:Z \
  --name minio \
  quay.io/minio/minio server /data --console-address ":9001"
```

The `:Z` applies to the host directory you’re mounting (`/home/qichen/cbim-minio`)
