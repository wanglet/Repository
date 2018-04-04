# 功能

提供制作rpm deb pypi仓库的环境

# 目录结构

类型 操作系统 软件名称 版本

* 类型: source | mirrors

    * source 存放源包
    * mirrors 存放制作完成的仓库

* 操作系统: centos | ubuntu | pypi | docker

    * pypi 直接存放python包,没有子目录
    * docker 直接存放镜像

* 软件名称: ceph | zabbix | saltstack | ...

* 版本: 10.2.10.-2 | ...

example:

```
source/docker/
source/centos/ceph/10.2.10-2/
source/pypi/
source/ubuntu/ceph/10.2.10-2/
mirrors/centos/ceph/10.2.10-2/
mirrors/pypi/
mirrors/ubuntu/ceph/10.2.10-2/
```

# 使用

## pypi

* 启动容器

    ```
    $ docker run -it --rm -w /repository -v /var/www/html/repository:/repository/ wanglet/repository bash
    ```

* 准备源包:

    ```
    方式一:
        将提前下载完成的包拷贝到source/pypi/目录下
    方式二:
        $ pip2tgz source/pypi/ pip
        $ pip2tgz source/pypi/ -r packages_list.txt
    ```

* 创建索引

    ```
    $ dir2pi -S source/pypi/
    ```

* 拷贝仓库

    ```
    $ mv source/pypi/simple/ mirrors/pypi/
    ```

* pip.conf

    ```
    $ cat example.conf
    [global]
    index-url = http://127.0.0.1/repository/mirrors/pypi/simple/
    timeout = 20
    [install]
    trusted-host =  127.0.0.1
    ```

##ubuntu

制作ubuntu仓库需提前准备好源包和配置文件(distributions options).

* 配置文件存放位置:

    ```
    $ ls source/ubuntu/ceph/10.2.10-2/
    ceph.deb conf/
    $ ls source/ubuntu/ceph/10.2.10-2/conf/
    distributions options
    ```

* 配置文件参考:

    ```
    $ cat distributions
    Origin:     ceph               # 你的名字
    Label:      ceph               # 库的名字
    Suite:      jewel              # 版本名称
    Codename:   jewel
    Version:   10.2.10-2          # 版本号
    Architectures:  amd64   i386         # 软件包所支持的架构， 比如 i386 或 amd64
    Components: main               # 包含的部件，比如 main restricted universe  multiverse
    Description:    private main deb repository
    SignWith:   default

    $ cat options
    verbose
    basedir  .
    ask-passphrase
    distdir  /srv/mirrors/ubuntu/ceph/10.2.10-2/dists
    outdir   /srv/mirrors/ubuntu/ceph/10.2.10-2         # 制作好的仓库存放目录
    ```

* 启动容器:

    ```
    $ docker run -it --rm -w /repository -v /var/www/html/repository:/repository/ wanglet/repository bash
    ```

* 制作仓库:

    ```
    $ reprepro --ask-passphrase -Vb source/ubuntu/ceph/10.2.10-2/conf/ export
    $ reprepro -b source/ubuntu/ceph/10.2.10-2/conf/ -C main includedeb jewel source/ubuntu/ceph/10.2.10-2/*.deb
    $ cp /keys/public.key mirrors/ubuntu/ceph/10.2.10-2/public.key
    ```

* 提供example.list:

    ```
    $ cat mirrors/ubuntu/ceph/10.2.10-2/example.list
    deb http://127.0.0.1/repository/mirrors/ubuntu/ceph/10.2.10-2/ jewel main
    ```

## centos


* 启动容器:

    ```
    $ docker run -v /var/www/html/:/srv/ -it repository bash
    ```

* 拷贝目录:

    ```
    $ docker run -it --rm -w /repository -v /var/www/html/repository:/repository/ wanglet/repository bash
    ```

* 制作仓库:

    ```
    $ createrepo mirrors/centos/ceph/10.2.10-2/
    ```

* 提供example.repo

    ```
    $ cat mirrors/centos/ceph/10.2.10-2/example.repo
    [ceph-10.2.10-2]
    name=ceph-10.2.10-2
    baseurl=http://127.0.0.1/repository/mirrors/centos/ceph/10.2.10-2/
    enabled=1
    gpgcheck=0
    ```
