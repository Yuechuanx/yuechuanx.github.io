---
title: GitLab 迁移与升级
tags: 
- gitlab
---



# 关于 GItlab 数据迁移与升级

## 背景

公司内部的 SDK 版本是通过 GItlab 进行版本管理，而在 SDK 中存在着大量的二进制文件，在进行多次版本发布之后，`.git` 文件越来越大，pull 新版本的时间也越来越长。

Git LFS 工具可以很好的解决这个问题，通过将二进制文件直接上传的方式，git 只需要保存字符链接，关于 git LFS 的原理可以看这里：

![image-20191101162007033](/Users/Xiaoy/Library/Application Support/typora-user-images/image-20191101162007033.png)

那么问题来了，由于 gitlab 的开始对 LFS 的版本高于目前公司使用的版本，那么就开始升级采坑之旅吧。

## 问题

之前的 gitlab 的部署是通过 bitnami 的 VM 部署到 host 机器上的，想转到用 docker 来进行部署。

首先我预想的方式是，将之前的 bitnami VM 的备份导入到 dockerhub 对应版本的镜像，在 Restore 之后换成更高版本的 gitlab image。不过很遗憾，这个方法失败了，gitlab 的升级需要递进的升级，在这里给出官方推荐的升级路线：

pic



那么换一种思路，先把之前的备份先恢复到对应的 gitlab image 上，在容器内部进行升级，之后再做一个备份，迁移到更高版本的 gitlab image。结论是，这个方法是可行的。

## 踩坑之旅

1. 前期准备：

- gitlab 8.5.1 备份
- Linux server with docker 

了解关于与备份恢复相关的一些命令：

~~~bash
# 查看 gitlab 版本
gitlab-rake gitlab:env:info

# 创建备份
gitlab-rake gitlab:backup:create

# 恢复备份
# 停止相关数据连接服务
gitlab-ctl stop unicorn
gitlab-ctl stop sidekiq

# 从备份中恢复，通过备份文件名前缀选择
gitlab-rake gitlab:backup:restore BACKUP=1572508219_2019_10_31_11.3.4
~~~

创建备份后得到一个文件 `1572508219_2019_10_31_11.3.4_gitlab_backup.tar`

根据查看的 gitlab 版本为 8.5.1

`docker pull gitlab/gitlab-ce:8.5.1-ce.0`

运行：

~~~bash
#!/bin/bash

sudo docker run --detach \
  --hostname gitlab.qa \
  --publish 443:443 --publish 80:80 --publish 22:22 \
  --name gitlab \
  --restart always \
  --volume /srv/gitlab/config:/etc/gitlab \
  --volume /srv/gitlab/logs:/var/log/gitlab \
  --volume /srv/gitlab/data:/var/opt/gitlab \
  --volume /srv/gitlab/logs/reconfigure:/var/log/gitlab/reconfigure \
  --env GITLAB_OMNIBUS_CONFIG="external_url 'http://192.168.205.236';" \
  gitlab/gitlab-ce:8.5.1-ce.0

~~~

关于`--volume /srv/gitlab/logs/reconfigure:/var/log/gitlab/reconfigure \`：

如果去掉会有如下错误

![image-20191101120624086](/Users/Xiaoy/Library/Application Support/typora-user-images/image-20191101120624086.png)

`docker exec -ti gitlab /bin/bash` 进入容器，

首先进行恢复
~~~bash
sudo gitlab-ctl stop unicorn
sudo gitlab-ctl stop sidekiq
gitlab-rake gitlab:backup:restore BACKUP=1572508219_xxxx
~~~
如果在恢复前对 gitlab 进行过设置，恢复时候将会覆盖掉，所以最好是在一个干净的 server 上进行恢复

恢复后按照官方推荐的方式逐个版本升级 

<img src="/Users/Xiaoy/Documents/MarkDown Files/imgs/gitlab-upgrade-version-path.png" alt="image-20191101161240814" style="zoom:;" />

[GitLab Release and Maintenance Policy](https://docs.gitlab.com/ee/policy/maintenance.html#upgrade-recommendations)

## 升级过程：
Tips:由于某ZZ的墙的原因,使用Gitlab的官方apt源会出现下载不了，可以选择清华的镜像，你也可以使用apt-mirror来自建本地的apt源(`https://packages.gitlab.com/gitlab/gitlab-ce/mirror`)但是版本可能会跟不上官方的更新。

1. 先进行数据的备份,这一步可以省略，因为gitlab在升级的时候会自动为你备份
   `gitlab-rake gitlab :backup :create RAILS_ENV=production`

2. 下载官方的提供的apt源自动更新脚本
   我的是ubuntu系统，所以选择的是apt源官方的package list 地址:
   手动更新apt源，最终我采用的是官方提供的这个文档：`https://packages.gitlab.com/gitlab/gitlab-ce/install#manual`

- refreshing package cache :
  `sudo apt-get update`

- Ensure the required tools are installed before proceeding 

  `sudo apt-get install curl gnupg apt-transport-https`

- install GPG Key:
  `curl -L https://packages.gitlab.com/gitlab/gitlab-ce/gpgkey | sudo apt-key add –`

- create apt file: /etc/apt/sources.list.d/gitlab_gitlab_ce.list ,please include repository configuration below :
  如果是ubuntu 系统如下:

  ```bash
  deb https://packages.gitlab.com/gitlab/gitlab-ce/ubuntu/ trusty main
  deb-src https://packages.gitlab.com/gitlab/gitlab-ce/ubuntu/ trusty main
  ```

  如果是别的Linux Distribution and version ，参考如下的文档:
  `https://packagecloud.io/docs#os_distro_version`

- `sudo apt-get update`

3. apt源配置完毕，下面可是执行上面的安装命令:
   先升级到9.5.0
   `sudo apt-get install gitlab-ce=9.5.1-ce.0`
   完毕后没有问题,要重启gitlab-ctl restart
   升级到10.8.7
   `sudo apt-get install gitlab-ce=10.8.7-ce.0`
   没有问题，重启sudo gitlab-ctl restart
   升级到最新版:
   `sudo apt-get install gitlab-ce`
   最后一部，如果没有大的版本发布，直接执行install 就行
   没有问题，重启`sudo gitlab-ctl restart`

4. 至此，gitlab的升级完成

   在升级好的 gitlab 上进行备份`gitlab-rake gitlab:backup:create` 再次迁移，即可



## 关于常见报错

### 访问 500 错误



## Reference

[Gitlab 升级总结](https://www.darrykinger.com/index.php/archives/27/)

[Gitlab 无损升级](https://www.jianshu.com/p/4b9a22d67466)

[Git 迁移到 Git LFS 实践]([https://networm.me/2018/05/13/migrate-to-gitlfs/#%E8%BD%AC%E6%8D%A2%E5%B7%A5%E5%85%B7](https://networm.me/2018/05/13/migrate-to-gitlfs/#转换工具))

[GitLab数据备份与恢复](https://www.jianshu.com/p/a2600f8dffc2)



[Migrate a Git repo into Git LFS with BFG](https://docs.gitlab.com/ee/topics/git/migrate_to_git_lfs/index.html)

[化繁为简的企业级 Git 管理实战（五）：二进制大文件的版本控制](https://www.hahack.com/work/enterprise-class-git-version-control-5/)

