# 关于jdbc连接mysql URL上的常见问题

## 编码问题

在url ?的后面添加如下参数：

```xml
useUnicode=true&amp;characterEncoding=UTF-8
```

## 时区问题

在url ? 的后面添加如下参数

```xml
serverTimezone=UTC 
```

```xml
serverTimezone=Asia/Shanghai
```

使用上海的时区，也可以使用Hongkong的时区

具体时区可以去/usr/share/zoneinfo里查询。

```xml
serverTimezone=GMT%2B8
```

表示设置MySQL服务器的时区为GMT+8，即东八区。

## 零值日期

在url ? 的后面添加如下的参数，会把零值日期转换为 null 值。

```xml
zeroDateTimeBehavior = convertToNull
```

## 密钥问题

在url ? 的后面添加如下的参数，允许从服务器检索公钥以进行密码验证。

```
allowPublicKeyRetrieval=true
```

## SSL协议问题

在url ? 的后面添加如下的参数，表示不使用SSL协议进行加密连接。

```xml
useSSL=false
```

##### *注意：参数间使用 & 连接*