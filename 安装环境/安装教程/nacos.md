```
docker run -d --name nacos-server -p 8848:8848 -p 9848:9848 \
-e MODE=standalone \
-e JVM_XMS=512m \
-e JVM_XMX=512m \
-v /logs/nacos:/home/nacos/logs \
nacos/nacos-server:v2.2.3 
```