# GitBook

### 下载

```
docker pull ryaning/gitbook
```

### 使用

#### gitbook init

命令说明：

- --rm 容器停止后自动删除容器
- -v /home/gitbook:/srv/gitbook :将主机中 /home/gitbook 挂载到容器的 /srv/gitbook

```
docker run --rm \
-v /home/gitbook:/srv/gitbook \
ryaning/gitbook \
gitbook init
```

#### gitbook serve

命令说明：

- -p 4000:4000 ：将容器的 4000 端口映射到主机的 4000 端口
- -v /home/gitbook:/srv/gitbook ：将主机中 /home/gitbook 挂载到容器的 /srv/gitbook

```
docker run \
-p 4000:4000 \
-v /home/gitbook:/srv/gitbook \
ryaning/gitbook \
gitbook serve
```

#### 其他

```
# 构建静态网站
docker run --rm \
-v /home/gitbook:/srv/gitbook \
-v /srv/html:/srv/html \
gitbook gitbook build . /srv/html

# 构建 PDF
docker run --rm \
-v /home/gitbook:/srv/gitbook \
-v /srv:/srv \
gitbook gitbook pdf . /srv/doc.pdf
```