CentOS  配置国内 yum 源

## CentOS 7 x86-64

```shell
CentOS 7 x86-64

//进入root，切换至yum.repos.d目录
cd /etc/yum.repos.d/

//创建新文件夹并将源文件备份为repo.bak
mkdir backup && mv *repo backup/

//下载国内yum源文件
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo

//163
##wget http://mirrors.163.com/.help/CentOS7-Base-163.repo

//生成缓存
yum clean all && yum makecache
```

## CentOS Stream 8 x86-64

````shell
CentOS Stream 8 x86-64

//进入root，切换至yum.repos.d目录
cd /etc/yum.repos.d/

//创建新文件夹并将源文件备份为repo.bak
mkdir backup && mv *repo backup/

//下载国内yum源文件
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-8.repo

//更新下载yum源地址
sed -i -e"s|mirrors.cloud.aliyuncs.com|mirrors.aliyun.com|g " /etc/yum.repos.d/CentOS-*
sed -i -e "s|releasever|releasever-stream|g" /etc/yum.repos.d/CentOS-*

//生成缓存
yum clean all && yum makecache
````

## CentOS Stream 9 x86-64

```shell
CentOS Stream 9 x86-64

//进入root，切换至yum.repos.d目录
cd /etc/yum.repos.d/

//创建新文件夹并将源文件备份为repo.bak
mkdir backup && mv *repo backup/	

//下载国内yum源文件
sed -i 's|metalink|#metalink|g' /etc/yum.repos.d/*.repo

sed -i '/name=CentOS Stream $releasever - BaseOS/a baseurl=https://mirrors.aliyun.com/centos-stream/$stream/BaseOS/$basearch/os/' /etc/yum.repos.d/*.repo

sed -i '/name=CentOS Stream $releasever - AppStream/a baseurl=https://mirrors.aliyun.com/centos-stream/$stream/AppStream/$basearch/os/' /etc/yum.repos.d/*.repo

sed -i '/name=CentOS Stream $releasever - Extras packages/a baseurl=https://mirrors.aliyun.com/centos-stream/SIGs/$stream/extras/$basearch/extras-common/' /etc/yum.repos.d/*.repo

//生成缓存
yum clean all && yum makecache
```



