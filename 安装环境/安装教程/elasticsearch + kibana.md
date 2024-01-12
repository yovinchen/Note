```
docker run -p 9200:9200 -p 9300:9300 --name elasticsearch \
-e "discovery.type=single-node" \
-e ES_JAVA_OPTS="-Xms512m -Xmx512m" \
-d elasticsearch:7.8.0
```

```
docker exec -it elasticsearch /bin/bash
```

```
./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.17.5/elasticsearch-analysis-ik-7.17.5.zip
```

```
docker restart elasticsearch
```

```
docker run -d --name kibana --link elasticsearch:elasticsearch -p 5601:5601 kibana:7.8.0
```
