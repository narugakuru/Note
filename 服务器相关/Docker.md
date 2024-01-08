[Docker更新容器镜像的三种方法-腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/2086860)

# 基础操作

```
查看日志
docker logs -f ChuanhuChat
根据dockerfile构建镜像
docker build -t chuanhuchatgpt:latest .
```
# portainer
默认一代

首先下载Portainer的Docker镜像；
```bash
docker pull portainer/portainer
```
然后再使用如下命令运行Portainer容器；
```bash
docker run -p 9000:9000 --name portainer \
--restart=always \
-v /var/run/docker.sock:/var/run/docker.sock \
-v /mnt/sata2-1/Configs/portainer/data:/data \
-d portainer/portainer
```

# 使用volume
```shell
docker volume create portainer_data
docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
cd /usr/libexec/docker/
sudo ln -s docker-runc-current docker-runc
```
