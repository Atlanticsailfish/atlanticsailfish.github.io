---
title: 容器重启报错：冲突 容器名已被使用
published: 2025-08-13
description: '解决容器报错：Error response from daemon: Conflict. The container name "/redis" is already in use by container "b3b8fcc2345..." You have to remove (or rename) that container to be able to reuse that name.'
image: './posts-images/65764244_p0_master1200.jpg'
tags: [Docker,Docker-compose,Ubuntu]
category: 'Work'
draft: false 
lang: ''
---

> Cover image source: [Source](https://www.pixiv.net/users/16943368)

## 情景重现

项目上线生产环境的第一天，数据库持续飙高，访问越来越慢，此时我决定重启生产环境
写下`docker-compose up -d`并重启后令人疑惑的现象发生了：
```bash
Error response from daemon: Conflict. The container name "/redis" is already in use by container "b3b8fcc2345..." You have to remove (or rename) that container to be able to reuse that name.'
```

我冷静下来，决定使用 `docker-compose down` 先解决这些Conflict，完成后
我再次使用 `docker-compose up -d`，接下来我不淡定了：
 ```bash
Error response from daemon: Conflict. The container name "/gateway" is already in use by container "a3c7d1342..." You have to remove (or rename) that container to be able to reuse that name.'
```

这我哪能受得了这气，依次`stop` `rm` 了所有冲突容器（没有做好持久化的小伙伴**千万别学**）
```bash
docker stop gateway
docker rm gateway
#其他stop和rm指令
docker-compose up -d
```

这下使用`docker ps`看到所有容器都起来了，露出了老师傅欣慰的笑容
但是前端却显示拒绝访问，what?!
苦苦思索，无法战胜，时间宝贵，马上摇人！

## 解决过程

摇来的人是**G师傅**，只二三条指令`docker logs -f gateway` `docker inspect gateway`，便看出了端倪：
```bash
"Mounts": [
            {
                "Type": "bind",
                "Source": "/home/user/conf.d",
                "Destination": "/etc/nginx/conf.d",
                "Mode": "rw",
                "RW": true,
                "Propagation": "rprivate"
            }
        ]
```

看到挂载路径`/home/user/conf.d`的我马上就反应出了不对劲，正确路径应该是`/opt/project/docker-compose-installer/nginx/conf.d/default.conf`
仔细一想，自己确实是在`/home/user`路径下启动过项目！再仔细一想，为什么能启动成功？
> 之前曾经移动过配置文件到`/home/user/`再移动回本机进行备份！
> 在某一次对`/home/user/`路径的清理，将`default.conf`移除了，导致了后续nginx的罢工
至此，令人疑惑的点：为什么容器会冲突；为什么容器正常启动了却无法访问。都有了合理的解释

## 事后总结

对于Linux系统的路径认识不足，混淆了启动路径，又侥幸因为某段时间内配置文件的齐全而运行了一段时间
措施：在`/home/user/`新建一个`dont_touch_me`文件夹用于存放配置文件（G师傅给的tips）