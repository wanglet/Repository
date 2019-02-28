## pypi

* 启动容器

    ```
    $ docker run -it --rm -v /var/www/html/repository/:/repository/ wanglet/repository bash
    ```

* 准备源包:

    ```
    方式一:
        将提前下载完成的包拷贝到/sources/目录下
    方式二:
        $ pip2tgz /sources/ -i https://mirrors.aliyun.com/pypi/simple/ docker
        $ pip2tgz /sources/ -i https://mirrors.aliyun.com/pypi/simple/ -r packages_list.txt
    ```

* 创建索引

    ```
    $ dir2pi -S /sources/
    ```

* 拷贝仓库

    ```
    $ mv /sources/simple/ /repository/mirrors/pypi/
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

## centos


* 启动容器:

    ```
    $ docker run -it --rm -v /var/www/html/repository:/repository/ wanglet/repository bash
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
