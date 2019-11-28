#### 安装docker

Docker 分为 CE 和 EE 两大版本。CE 即社区版（免费，支持周期 7 个月），EE
即企业版，强调安全，付费使用，支持周期 24 个月。

Docker CE 分为 stable, test, 和 nightly 三个更新频道。每六个月发布一个 stable
版本 (18.09, 19.03, 19.09...)。

> The Docker Engine - Community package is now called `docker-ce`.

#### [Centos 安装docker-ce](https://docs.docker.com/install/linux/docker-ce/centos/)

You can install Docker Engine - Community in different ways, depending on your needs:

- Most users [set up Docker’s repositories](https://docs.docker.com/install/linux/docker-ce/centos/#install-using-the-repository) and install from them, for ease of installation and upgrade tasks. **This is the recommended approach.**
- Some users download the RPM package and [install it manually](https://docs.docker.com/install/linux/docker-ce/centos/#install-from-a-package) and manage upgrades completely manually. This is useful in situations such as installing Docker on air-gapped systems with no access to the internet.
- In testing and development environments, some users choose to use automated [convenience scripts](https://docs.docker.com/install/linux/docker-ce/centos/#install-using-the-convenience-script) to install Docker.

### Install using the repository

Before you install Docker Engine - Community for the first time on a new host machine, **you need to set up the Docker repository. **Afterward, you can install and update Docker from the repository.

#### SET UP THE REPOSITORY

1. Install required packages. `yum-utils` pro*v*ides the `yum-config-manager` utility, and `device-mapper-persistent-data` and `lvm2` are required by the `devicemapper` storage driver.

   ```shell
   $ sudo yum install -y yum-utils \
     device-mapper-persistent-data \
     lvm2
   ```

2. Use the following command to set up the **stable** repository.

   ```shell
   $ sudo yum-config-manager \
   --add-repo \
   https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo
   # 官方源
   # $ sudo yum-config-manager \
   # --add-repo \
   # https://download.docker.com/linux/centos/docker-ce.repo
   ```

#### INSTALL DOCKER ENGINE - COMMUNITY

1. Install the *latest version* of Docker Engine - Community and containerd

   ```
   $ sudo yum install docker-ce docker-ce-cli containerd.io
   ```

#### Start Docker.

```shell
# 启动 Docker CE
$ sudo systemctl enable docker
$ sudo systemctl start docker
```