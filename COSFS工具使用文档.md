# COSFS工具

## 功能说明

COSFS 工具支持将 COS 存储桶挂载到本地，像使用本地文件系统一样直接操作建行云对象存储。COSFS 的主要功能包括：

- 支持 POSIX 文件系统的大部分功能，如：文件读写、目录操作、链接操作、权限管理等功能；
- 大文件传输功能；
- MD5 数据校验功能。

## 使用环境

### 系统环境

主流 Linux 系统。

### 软件环境

本工具编译需要 C++ 编译环境。依赖于 automake、git 、libcurl-devel、libxml2-devel、fuse-devel、make、openssl-devel 等软件，安装方法参考环境安装。 

### 环境安装

#### Ubuntu 系统下安装环境依赖包方法：

```
sudo apt-get install automake autotools-dev g++ git libcurl4-gnutls-dev libfuse-dev libssl-dev libxml2-dev make pkg-config fuse
```

#### CentOS 系统下安装环境依赖包方法：

```
sudo yum install automake gcc-c++ git libcurl-devel libxml2-devel fuse-devel make openssl-devel
```

注意在 centos6.5 及较低版本，可能会提示 fuse 版本太低，在安装过程的 configure 操作时返回 

```
checking for common_lib_checking... configure: error: Package requirements (fuse >= 2.8.4 libcurl >= 7.0 libxml-2.0 >=    2.6) were not met:
  Requested 'fuse >= 2.8.4' but version of fuse is 2.8.3
```

此时，您需要来手动安装 fuse 版本，具体命令如下： 

```
 # yum remove -y fuse-devel
  # wget https://github.com/libfuse/libfuse/releases/download/fuse_2_9_4/fuse-2.8.4.tar.gz
  # tar -zxvf fuse-2.8.4.tar.gz
  # cd fuse-2.8.4
  # ./configure
  # make
  # make install
  # export PKG_CONFIG_PATH=/usr/lib/pkgconfig:/usr/lib64/pkgconfig/:/usr/local/lib/pkgconfig
  # modprobe fuse
  # echo "/usr/local/lib" >> /etc/ld.so.conf
  # ldconfig
  # pkg-config --modversion fuse   
  2.8.4   //看到版本表示安装成功
```

## 使用方法

### 1. 获取工具

Github 下载地址： [COSFS 工具](https://github.com/tencentyun/cosfs)。

### 2. 安装工具

您可以直接将下载的源码上传至指定目录，也可以使用 GitHub 下载到指定目录，下面以使用 GitHub 将源码目录下载到 `/usr/cosfs` 为例：

```
git clone https://github.com/tencentyun/cosfs /usr/cosfs
```

进入到该目录，编译安装： 

```
cd /usr/cosfs
./autogen.sh
./configure
make
sudo make install
```

### 3. 配置文件

在 `/etc/passwd-cosfs`文件中，配置您的存储桶的名称，以及该存储桶对应的 SecretId 和 SecretKey 可以通过点击控制台右上方账号 -> 访问管理 -> 云API密钥 -> API密钥管理 获取。使用冒号隔开，注意冒号为半角符号。 并为 `/etc/passwd-cosfs` 设置可读权限。命令格式如下：

```
echo <bucketname>:<SecretId>:<SecretKey> > /etc/passwd-cosfs
```

```
chmod 640 /etc/passwd-cosfs
```

其中：
bucketname/ SecretId/ SecretKey 需要替换为用户的真实信息。

bucketname 形如 bucketprefix-123456789, 更多关于 bucketname 的命名规范，请参见 [存储桶命名规范](https://github.com/ccbcloud/cos-api#基本信息)。

范例：

```
echo a-1234000008:ABCDEabcBMAbeOovjDtI42h3mCJ7dsnQwkSq:ABCs73g01j8DzTdU2HDqBDzpLbABCDzF > /etc/passwd-cosfs
```

```
chmod 640 /etc/passwd-cosfs
```

### 4. 运行工具

将配置好的存储桶挂载到指定目录，命令行如下：

```
cosfs your-bucketname your-mount-point -ourl=cos-domain-name -odbglevel=info -oxmlns
```

范例：

```
cosfs a-1234000008 /mnt -ourl=cos.wh.yun.ccb.com -odbglevel=dbg -oxmlns
```

其中：

- your-bucketname 需要替换为用户真实的信息；
- your-mount-point 替换为本地需要挂载的目录（如 /mnt）；
- cos-domain-name 为存储桶对应的访问域名
- -odbglevel 参数表示信息级别，可选 info、dbg，建议参照示例设置为“info”。
- -oxmlns 参数表示忽略 xml namespace 检查 



### 卸载存储桶示例：

```
fusermount -u /mnt
```

或者

```
umount -l /mnt
```

## 常用挂载选项

1. -omultipart_size=[size]
   `multipart_size` 用来指定分块上传时，每个分块的大小，默认是 10 MB。 由于分块上传对块的数目有最大限制（10000 块），所以对于大文件，例如超出 10 MB * 10000 (100 GB) 大小的文件，需要根据具体情况调整该参数。该参数单位是 MB。
2. -oallow_other
   如果要允许其他用户访问挂载文件夹，可以在运行 COSFS 的时候指定 `allow_other` 参数。
3. -odel_cache
   默认情况下，cosfs 为了优化性能，在 umount 后，不会清除本地的缓存数据。 如果需要在 COSFS 退出时，自动清除缓存，可以在挂载时加入该选项。
4. -onoxattr
   禁用 get/setxattr 功能， 当前版本的 cosfs 不支持该功能，如果本地文件所在磁盘在挂载的时候使用了 use_xattr 选项，可能会导致 mv 文件到 bucket 失败。

## 局限性

COSFS 提供的功能、性能和本地文件系统相比，存在一些局限性。例如：

- 随机或者追加写文件会导致整个文件的重写。
- 多个客户端挂载同一个 COS 存储桶时，依赖用户自行协调各个客户端的行为。例如避免多个客户端写同一个文件等。
- 不支持 hard link，不适合高并发读/写的场景。
- 不可以同时在一个挂载点上挂载、和卸载文件。您可以先 cd 切换到其他目录，再对挂载点进行挂载、卸载操作。
