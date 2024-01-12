ELK Stack

https://www.51cto.com/article/707776.html

这次先讲解 ELK Stack 的方式，这种方式对我们的代码无侵入，核心思想就是收集磁盘的日志文件，然后导入到 Elasticsearch。

比如我们的应用系统通过 logback 把日志写入到磁盘文件，然后通过这一套组合的中间件就能把日志采集起来供我们查询使用了。

流程如下：

- 先使用 Filebeat 把日志收集起来，然后把数据再传给 Logstash。
- 通过 Logstash 强大的数据清洗功能。
- 最终把数据写入到 Elasticsearch 中。
- 并由 Kibana 进行可视化。

温馨提示：以下案例都在一台 ubuntu 虚拟机上完成，内存分配了 6G。

### 一、部署 Elasticsearch

数据库获取 elasticsearch 镜像：

```
docker pull elasticsearch:7.7.1
```

创建挂载目录：

```
mkdir -p /data/elk/es/{config,data,logs}
```

赋予权限：

```
chown -R 1000:1000 /data/elk/es
```

创建配置文件：

```
cd /data/elk/es/config
touch elasticsearch.yml
-----------------------配置内容----------------------------------
cluster.name: "my-es"
network.host: 0.0.0.0
http.port: 9200
```

启动 elasticsearch 容器：

```
docker run -it  -d -p 9200:9200 -p 9300:9300 --name es -e ES_JAVA_OPTS="-Xms1g -Xmx1g" -e "discovery.type=single-node" --restart=always -v /data/elk/es/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml -v /data/elk/es/data:/usr/share/elasticsearch/data -v /data/elk/es/logs:/usr/share/elasticsearch/logs elasticsearch:7.7.1
```

验证 elasticsearch 是否启动成功：

```
curl http://localhost:9200
```

### 二、部署 Kibana 可视化工具

#### 2.1 安装 Kibana

获取 kibana 镜像：

```
docker pull kibana:7.7.1
```

获取elasticsearch容器 ip：

```
docker inspect --format '{{ .NetworkSettings.IPAddress }}' es
```

结果：172.17.0.2；

创建 kibana 配置文件：

```
mkdir -p /data/elk/kibana/
vim /data/elk/kibana/kibana.yml
```

配置内容：

```
#Default Kibana configuration for docker target
server.name: kibana
server.host: "0"
elasticsearch.hosts: ["http://172.17.0.2:9200"]
xpack.monitoring.ui.container.elasticsearch.enabled: true
```

#### 2.2 运行 kibana

```
docker run -d --restart=always --log-driver json-file --log-opt max-size=100m --log-opt max-file=2 --name kibana -p 5601:5601 -v /data/elk/kibana/kibana.yml:/usr/share/kibana/config/kibana.yml kibana:7.7.11.
```

访问 http://192.168.56.10:5601。这个 IP 是服务器的 IP。Kibana 控制台的界面如下所示，打开 kibana 时，首页会提示让你选择加入一些测试数据，点击 try our sample data 按钮就可以了。

### 三、部署 logstash 日志过滤、转换工具

#### 3.1 安装 Java JDK

```
yum install java-1.8.0-openjdk.x86_64
```

修改 /etc/profile 文件：

```
sudo vim /etc/profile
```

添加如下的内容到你的 .profile 文件中：

```
# JAVA
JAVA_HOME="/usr/lib/jdk/jdk-12"
PATH="$PATH:$JAVA_HOME/bin"
```

再在命令行中打入如下的命令：

```
source /etc/profile1.
```

查看 java 是否配置成功：

```
java -version
```

#### 3.2 安装 logstash

下载 logstash 安装包：

```
curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-7.7.1.tar.gz
```

解压安装：

```
tar -xzvf logstash-7.7.1.tar.gz
```

要测试 Logstash 安装，请运行最基本的 Logstash 管道。例如：

```
cd logstash-7.7.1
bin/logstash -e 'input { stdin { } } output { stdout {} }'
```

等 Logstash 完成启动后，我们在 stdin 里输入以下文字，我们可以看到如下的输出：

当我们打入一行字符然后回车，那么我们马上可以在 stdout 上看到输出的信息。如果我们能看到这个输出，说明我们的 Logstash 的安装是成功的。

我们进入到 Logstash 安装目录，并修改 config/logstash.yml 文件。我们把 config.reload.automatic 设置为 true。

```yaml
config.reload.automatic: true
```

另外一种运行 Logstash 的方式，也是一种最为常见的运行方式，运行时指定 logstash 配置文件。



#### 3.3 配置 logstash

Logstash 配置文件有两个必需元素，输入(inputs)和输出(ouputs)，以及一个可选元素 filters。输入插件配置来源数据，过滤器插件在你指定时修改数据，输出插件将数据写入目标。

创建 kibana 配置文件 weblog.conf：



```
mkdir -p /logstash-7.7.1/streamconf
vim /logstash-7.7.1/streamconf/weblog.conf
```

配置内容如下：



```
input {
  tcp {
    port => 9900
  }
}
 
filter {
  grok {
    match => { "message" => "%{COMBINEDAPACHELOG}" }
  }
 
  mutate {
    convert => {
      "bytes" => "integer"
    }
  }
 
  geoip {
    source => "clientip"
  }
 
  useragent {
    source => "agent"
    target => "useragent"
  }
 
  date {
    match => ["timestamp", "dd/MMM/yyyy:HH:mm:ss Z"]
  }
}
 
output {
  stdout { }
 
  elasticsearch {
    hosts => ["localhost:9200"]
  }
}
```

在上面，我们同时保留两个输出：stdout 及 elasticsearch。事实上，我们可以定义很多个的输出。stdout 输出对于我们初期的调试是非常有帮助的。等我们完善了所有的调试，我们可以把上面的 stdout 输出关掉。

等更新完这个配置文件后，我们在另外一个 console 中发送第一个 log：



```
head -n 1 weblog-sample.log | nc localhost 9900
```

这个命令的意思：我们使用 nc 应用读取第一行数据，然后发送到 TCP 端口号 9900，并查看 console 的输出。

这里的 weblog-sample.log 为样例数据，内容如下，把它放到本地作为日志文件。



```
14.49.42.25 - - [12/May/2019:01:24:44 +0000] "GET /articles/ppp-over-ssh/ 
HTTP/1.1" 200 18586 "-" "Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US; 
rv:1.9.2b1) Gecko/20091014 Firefox/3.6b1 GTB5"
```

logstash 控制台打印出了 weblog-samle.log 中的内容：

![image-20240104174304131](https://lsky.hhdxw.top/imghub/2024/01/image-202401041704361384.png)

这一次，我们打开 Kibana，执行命令，成功看到 es 中的这条记录。

```
GET logstash/_search
```

![image-20240104174326473](https://lsky.hhdxw.top/imghub/2024/01/image-202401041704361406.png)

### 四、部署 Filebeat 日志收集工具

#### 4.1 安装 Filebeat

```
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.7.1-linux-x86_64.tar.gz
tar xzvf filebeat-7.7.1-linux-x86_64.tar.gz
```

请注意：由于 ELK 迭代比较快，我们可以把上面的版本 7.7.1 替换成我们需要的版本即可。我们先不要运行 Filebeat。

#### 4.2 配置 Filebeat

我们在 Filebeat 的安装目录下，可以创建一个这样的 filebeat_apache.yml 文件，它的内容如下，首先先让 filebeat 直接将日志文件导入到 elasticsearch，来确认 filebeat 是否正常工作。

```
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /home/vagrant/logs/*.log

output.elasticsearch:
  hosts: ["192.168.56.10:9200"]
```

paths 对应你的日志文件夹路径，我配置的是这个：/home/vagrant/logs/*.log，之前配置成 /home/vagrant/logs 不能正常收集。另外这里可以放入多个日志路径。

#### 4.3 测试 Filebeat

在使用时，你先要启动 Logstash，然后再启动 Filebeat。

```
bin/logstash -f weblog.conf
```

然后，再运行 Filebeat， -c 表示运行指定的配置文件，这里是 filebeat_apache.yml。

```
./filebeat -e -c filebeat_apache.yml
```

运行结果如下所示，一定要确认下控制台中是否打印了加载和监控了我们指定的日志。如下图所示，有三个日志文件被监控到了：error.log、info.log、debug.log

我们可以通过这个命令查看 filebeat 的日志是否导入成功了：

```
curl http://localhost:9200/_cat/indices?v
```

这个命令会查询 Elasticsearch 中所有的索引，如下图所示，filebeat-7.7.1-* 索引创建成功了。因为我没有配置索引的名字，所以这个索引的名字是默认的。

在 kibana 中搜索日志，可以看到导入的 error 的日志了。不过我们先得在 kibana 中创建 filebeat 的索引(点击 create index pattern 按钮，然后输入 filebeat 关键字，添加这个索引)，然后才能在 kibana 的 Discover 控制台查询日志。

#### 4.4 Filebeat + Logstash

接下来我们配置 filebeat 收集日志后，输出到 logstash，然后由 logstash 转换数据后输出到 elasticsearch。

```yaml
filebeat.inputs:

- type: log
  enabled: true
  paths:
    - /home/vagrant/logs/*.log

output.logstash:
  hosts: ["localhost:9900"]
```

修改 logstash 配置文件：

```
vim /logstash-7.7.1/streamconf/weblog.conf
```

配置了 input 为 beats，修改了 useragent：

```
input {  
  beats {
    port => "9900"
  }
}
 
filter {
  grok {
    match => { "message" => "%{COMBINEDAPACHELOG}" }
  }
 
  mutate {
    convert => {
      "bytes" => "integer"
    }
  }
 
  geoip {
    source => "clientip"
  }
 
  useragent {
    source => "user_agent"
    target => "useragent"
  }
 
  date {
    match => ["timestamp", "dd/MMM/yyyy:HH:mm:ss Z"]
  }
}
 
output {
  stdout {
    codec => dots {}
  }
 
  elasticsearch {
    hosts=>["192.168.56.10:9200"]
    index => "apache_elastic_example"
  }
}
```

然后重新启动 logstash 和 filebeat。有个问题，这次启动 filebeat 的时候，只监测到了一个 info.log 文件，而 error.log 和 debug.log 没有监测到，导致只有 info.log 导入到了 Elasticsearch 中。filebeat 只监测到了 info.log 文件

logstash 输出结果如下，会有格式化后的日志：

我们在 Kibana dev tools 中可以看到索引 apache_elastic_example，说明索引创建成功，日志也导入到了 elasticsearch 中。

另外注意下 logstash 中的 grok 过滤器，指定的 message 的格式需要和自己的日志的格式相匹配，这样才能将我们的日志内容正确映射到 message 字段上。

例如我的 logback 的配置信息如下：



而我的 logstash 配置如下，和 logback 的 pettern 是一致的。

```yaml
grok {
    match => { "message" => "%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger -%msg%n" }
  }
```

然后我们在 es 中就能看到日志文件中的信息了。如下图所示：



至此，Elasticsearch + Logstash + Kibana + Filebeat 部署成功，可以愉快地查询日志了~

后续升级方案：

- 加上 Kafka。
- 加上 Grafana 监控。
- 链路追踪。