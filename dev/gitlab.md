# gitlab

### 安装

`docker pull gitlab`

```shell
docker run --detach \
    --hostname gitlab.uxin.com \
    --publish 443:443 --publish 80:80 --publish 32:22 \
    --name gitlab \
    --restart always \
    --volume /opt/docker/gitlab/config:/etc/gitlab \
    --volume /opt/docker/gitlab/logs:/var/log/gitlab \
    --volume /opt/docker/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce:latest
```
