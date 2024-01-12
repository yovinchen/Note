```shell
docker run -p 9000:9000 -p 9090:9090 --name minio \
     -d --restart=always \
     -e "MINIO_ACCESS_KEY=minio" \
     -e "MINIO_SECRET_KEY=password" \
     -v database:/data \
     minio/minio server \
     /data --console-address ":9090" -address ":9000"
```