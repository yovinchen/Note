docker镜像配置
```

{
  "builder": {
    "gc": {
      "defaultKeepStorage": "20GB",
      "enabled": true
    }
  },
  "experimental": false,
  "features": {
    "buildkit": true
  },
  "registry-mirrors": [
    "https://9kkc1zdn.mirror.aliyuncs.com"
  ]
}
```

恢复docekr数据
```
docker load -i all_images.tar

```