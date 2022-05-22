# Docker introduce

## 绍

Docker Engine 是一种开源容器化技术，用于构建和容器化您的应用程序。

这里有一副图关于docker结构的图 这里**Client 是shell窗口 ，DOCKER\_HOST 是你的主机 ，Registry 是远端仓库 比如 docker hub（可以类比 github）**

![](https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F9ejIAiUCfKkbEnbExeOt%2Fuploads%2FtzQUSjRMLeW9PEExXHnO%2Fimage.png?alt=media\&token=c0aa18ea-82f8-4134-b982-1b7ee42e1037)

几个常用词

* volume 用于**主机** 与docker的 container 共享文件夹
* image 类似用于装系统的镜像，安装后就有配套的环境
* container 可以理解成虚拟机，但是没有虚拟机虚拟的彻底

## 安装（仅 有 sudo 权限的用户装一遍就行）

官方教程很简单 ，这里（仅 有 sudo 权限的用户装一遍就行）的意思是 ，为了配置docker的多用户使用，需要每个用户独立配置一些东西，但是不需要重新安装。

因此，此处是 docker 在 主机上的安装，仅需要管理员配置环境时安装一遍即可，教程链接如下

{% embed url="https://docs.docker.com/engine/install/ubuntu#install-using-the-repository" %}
Docker install
{% endembed %}

### NVIDIA-GPU 支持（仅 有 sudo 权限的用户装一遍就行）

为了docker 可以使用 GPU ，需要配置 Nvidia docker

这里是官方教程，但是因为DNS污染的问题，[nvidia.github.io](http://nvidia.github.io)  域名会指向 127.0.0.1导致安装失败

[Installation Guide - NVIDIA Cloud Native Technologies documentation](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#installing-on-ubuntu-and-debian)

这里提供一个第三方教程，这个流程很完整，在不翻墙的情况下解决了DNS污染的问题

[NVIDIA Docker 的安装和使用](https://fanjunyu.com/posts/bb9b6d25/)

## Docker的多用户使用

### 关于sudo 权限

正常的docker 使用需要 sudo权限，但是为了安全，我们没有给用户权限。在docker 设置中 不需要sudo 权限的办法有两个

1.  网络上最常见的是**设置docker用户组**，但是事实上这种办法为`docker`组授予与`root` 用户等效的权限，存在安全隐患，官方提供了方法

    [Post-installation steps for Linux](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user)
2. Rootless 的方法 在中文互联网上很难找，但事实上这是docker官方推荐的保证安全的方式，虽然会有一些限制，但是可以保障安全性，下面的教程均是Rootless的

### Docker 设置 rootless 无需 ROOT 权限（用户 每个用户需要单独设置一遍）

**用于 在无root权限时的 docker 使用**

**第一次使用时需要以下命令（注意：只有本用户会生效）**

```
# warning :联系管理员执行
sudo loginctl enable-linger $(whoami)

# 确保是ssh直接登录 ，不能通过其他用户跳转进行安装（不知道什么意思就不用管这句注释）
curl -fsSL <https://get.docker.com/rootless> | sh
systemctl --user start docker
systemctl --user enable docker
docker context use rootless
```

最后使用下面命令测试

```
docker run --rm --gpus all nvidia/cuda:11.0-base nvidia-smi
```

> 注意

> 1. 如果提示 systemd not detected, dockerd daemon needs to be started manually

> ，请确保自己是SSH登录的，没有从其他用户跳转

> 1. 如果docker的下载速度过慢可以尝试 换源，点此

#### 参考连接

[Run the Docker daemon as a non-root user (Rootless mode)](https://docs.docker.com/engine/security/rootless/)

[systemd not detected, dockerd daemon needs to be started manually](https://unix.stackexchange.com/questions/587674/systemd-not-detected-dockerd-daemon-needs-to-be-started-manually)

[新用户配置环境（22/3/4）](https://shimo.im/docs/8Nk6M899QQSRjNqL#anchor-Q85F)

当然配置的时候可能会遇到一些问题

## 可能遇到的问题，及解决方案

### 无 ROOT 权限 测试GPU 时报错 |--gpu标志在没有sudo命令的情况下无法工作

```
(base) wanghao@adept2080-X11DPG-OT:~$ docker run --rm --gpus all nvidia/cuda:11.0-base nvidia-smi
docker: Error response from daemon: OCI runtime create failed: container_linux.go:380: starting container process caused: process_linux.go:545: container init caused: Running hook #0:: error running hook: exit status 1, stdout: , stderr: nvidia-container-cli: mount error: failed to add device rules: open /sys/fs/cgroup/devices/user.slice/devices.allow: permission denied: unknown.
```

修改 **etc/nvidia-container-runtime/config.toml**的这两个部分 （需要管理员权限）

```
[nvidia-container-cli]
no-cgroups = true

[nvidia-container-runtime]
debug = "/tmp/nvidia-container-runtime.log"
```

### 挂载卷只能挂载在本地目录（\~）

由于没有sudo权限 用户能操作的空间仅为 用户home目录 即 cd \~

创建卷 （/home/wanghao/workspace 是宿主机路径 --name 是给名字这里给了code）

```
docker volume create --name code --opt type=none --opt device=/home/wanghao/workspace --opt o=bind
```

创建容器并挂载卷（容器名 pcdet，挂载卷 code 映射到 /root/code(千万不要)）

```
docker run -it --name pcdet --gpus=all -v code:/root/code 787cd5a1f5c1 /bin/bash
```

### rootless 的 limitations

[https://itnext.io/docker-running-in-rootless-mode-bdbcfc728b3a](https://itnext.io/docker-running-in-rootless-mode-bdbcfc728b3a)

[https://itnext.io/docker-running-in-rootless-mode-bdbcfc728b3a](https://itnext.io/docker-running-in-rootless-mode-bdbcfc728b3a)

### Port < 1024

主机 端口 < 1024 为特权端口 在 rootless 模式下不能直接影射

## 常用语句示例

这里pcdet 是我创建的容器名字

```
# 创建卷 名字 code 映射到本地目录 /home/wanghao/workspace
docker volume create --name code --opt type=none --opt device=/home/wanghao/workspace --opt o=bind # 启动一个容器 docker run -it --name pcdet1 --gpus=all -v code:/root/code 787cd5a1f5c1 /bin/bash
#  进入 环境 两种都可以
docker exec -it pcdet /bin/bash
docker attach pcdet
# 启动环境 类似开关虚拟机
docker start xxxx
docker stop xxxx
docker restart xxxx

# 把 xxx_container  打包成 名叫 xxx_image 的镜像
docker commit xxx_container xxx_image
# 镜像打包
docker save -o /root/xxx.tar <name>
# 镜像 加载 注意重定向方向 <
docker load < xxx.tar
```

## 使用技巧

### 通过跳板 的方式 使用VScode远程连接 docker 的ssh 容器

1. 在新建 container 的时候 讲容器22端口映射出来
2.  在容器中安装 openssh-server

    ```
    apt update
    apt install openssh-server
    apt install vim
    ```
3.  修改 sshd\_config

    ```
     vim /etc/ssh/sshd_config
    ```

    修改如下：

    ```
    PubkeyAuthentication yes #启用公钥私钥配对认证方式
    AuthorizedKeysFile .ssh/authorized_keys #公钥文件路径（和上面生成的文件同）
    PermitRootLogin yes #root能使用ssh登录
    ClientAliveInterval 60  #参数数值是秒 , 是指超时时间
    ClientAliveCountMax 3 #设置允许超时的次数
    ```
4.  启动ssh\_server

    ```
    service ssh start
    ```

    离开容器
5.  配置 **自己电脑** 的VScode(好像注释会报错 ，实际使用可以删掉)

    ```
    Host wanghao_2080_pcdet_docker  # 随便起
      HostName 118.178.187.157 # 训练服务器IP
      User wanghao # 训练服务器用户名
      Port 6001
      ProxyCommand ssh.exe -p 32200 root@127.0.0.1 nc %h %p  # 跳板机用户名和IP
    ```

#### 参考资料

[VSCode 穿越跳板机调试远程代码](https://zhuanlan.zhihu.com/p/149312534)

### docker 换源

个人电脑使用时可以选择 更改 daemon.json

[Docker 换源](https://cloud.tencent.com/developer/article/1769231)

但是，在rootless下 传统的 更改 daemon.json的方式 似乎没有用了，目前没有找到合适的加速方式，只能使用临时办法

例如：

```
# 假如你需要 pull ubuntu
docker pull ubuntu
# 上面这句话很慢 你可以用下面的方式加速
docker pull docker.mirrors.ustc.edu.cn/library/ubuntu
同理的 拉ubuntu
docker pull docker.mirrors.ustc.edu.cn/library/ubuntu:16.04
```
