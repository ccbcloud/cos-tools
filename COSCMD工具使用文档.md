# COSCMD工具

## 功能说明

使用 COSCMD 工具，用户可通过简单的命令行指令实现对对象（Object）的批量上传、下载、删除等操作。

## 使用环境

### 系统环境

Windows 或 Linux 系统
(请保证本地字符格式为 utf-8，否则操作中文文件会出现异常)

### 软件依赖

- Python 2.6/2.7/3.5/3.6
- 装载最新版本的 pip

### 安装及配置

环境安装与配置详细操作，请参考 [Python 安装与配置](https://wiki.python.org/moin/BeginnersGuide/Download)。

## 下载安装

1. 检查系统环境是否是 Windows 或 Linux 系统。

2. 检查是否装载 Python，若没有安装，具体参考 [Python 安装与配置](https://wiki.python.org/moin/BeginnersGuide/Download) 。

3. 检查是否装载最新版本 pip，若没有安装，请前往 [PyPA pip 文档](https://pip.pypa.io/en/stable/installing/) 按照教程安装。

4. 下载 [COSCMD 安装包](https://github.com/tencentyun/coscmd)。

5. 进入 Terminal 开始 pip 安装，执行命令如下：
   ```
   pip install coscmd
   ```

6. 更新COSCMD版本，执行命令如下：

   ```
   pip install coscmd -U
   ```
- 离线安装
   ```
    # 在有外网的机器下运行如下命令
	mkdir coscmd-packages
	pip download coscmd -d coscmd-packages
	tar -czvf coscmd-packages.tar.gz coscmd-packages
   ```	
  注意： 请确保两台机器的 python 版本保持一致，否则会出现安装失败的情况

   ```
    # 将安装包拷贝到没有外网的机器后运行如下命令
	tar -xzvf coscmd-packages.tar.gz
	pip install coscmd --no-index -f coscmd-packages
   ```




## 使用方法

### 查看help

用户可以通过-h命令来查看工具的help信息

```
coscmd -h
```

除此之外，用户还可以在命令后，输入-h查看该命令的具体用法

```
coscmd upload -h
```



### 配置参数

COSCMD 工具在使用前需要进行参数配置。用户可以通过如下命令来配置： 

```
coscmd config -a <secret_id> -s <secret_key> -b <bucket> -e <end_point> --do-not-use-ssl
```

范例：

```
coscmd config -a ABCDEuxhBMAbeOovjDtI42h3mCJ7dsnQwkSq -s ABCs73g01j8DzTdU2HDqBDzpLbYBSOzF -b a-1255000008 -e cos.wh.yun.ccb.com
--do-not-use-ssl
```



| 名称             | 描述                                                         | 有效值 |
| ---------------- | :----------------------------------------------------------- | ------ |
| secret_id        | 必选参数，secret_id可以通过点击控制台右上方账号 -> 访问管理 -> 云API密钥 -> API密钥管理 获取 | 字符串 |
| secret_key       | 必选参数，secret_key可以通过点击控制台右上方账号 -> 访问管理 -> 云API密钥 -> API密钥管理 获取 | 字符串 |
| bucket           | 必选参数，指定的存储桶名称，bucket 的命名规则为{name}-{appid} ，可以通过存储桶列表 获取 | 字符串 |
| end_point        | 必选参数，存储桶所在域名                                     | 字符串 |
| --do-not-use-ssl | 可选参数， 开启了就是http访问，默认是走https                 | 字符串 |



### 创建文件或文件夹

上传文件命令如下： 

```
coscmd upload <localpath> <cospath>  //命令格式
coscmd upload /home/aaa/123.txt bbb/123.txt  //操作示例
coscmd upload /home/aaa/123.txt bbb/  //操作示例
```

范例：

```
coscmd upload ccbCloudLogo.png cloudlogo.png
```



上传文件夹命令如下：

```
coscmd download -r <cospath> <localpath> //命令格式
coscmd download -r /home/aaa/ bbb/aaa  //操作示例
coscmd download -r /home/aaa/ bbb/  //操作示例
coscmd download -rf / bbb/aaa  //覆盖下载当前bucket根目录下所有的文件
coscmd download -rs / bbb/aaa  //同步下载当前bucket根目录下所有的文件，跳过md5校验相同的文件
coscmd download -rs / bbb/aaa --ignore *.txt,*.doc //忽略.txt和.doc的后缀文件
```

范例

```
coscmd upload -r corrytest \file1
```



### 下载文件或文件夹

下载文件命令如下： 

```
coscmd download <cospath> <localpath>  //命令格式
coscmd download bbb/123.txt /home/aaa/111.txt  //操作示例
coscmd download bbb/123.txt /home/aaa/  //操作示例
```

范例：

```
coscmd download cloudlogo.png corrytest\cloudlogo2.png
```



下载文件夹命令如下：

```
coscmd download -r <cospath> <localpath> //命令格式
coscmd download -r /home/aaa/ bbb/aaa  //操作示例
coscmd download -r /home/aaa/ bbb/  //操作示例
coscmd download -rf / bbb/aaa  //覆盖下载当前bucket根目录下所有的文件
coscmd download -rs / bbb/aaa  //同步下载当前bucket根目录下所有的文件，跳过md5校验相同的文件
coscmd download -rs / bbb/aaa --ignore *.txt,*.doc //忽略.txt和.doc的后缀文件
```

范例：

```
coscmd download -r \file1 \corrytest
```



### 删除文件或文件夹

删除文件命令如下：

```
coscmd delete <cospath>  //命令格式
coscmd delete bbb/123.txt  //操作示例
```

这里有问题，删不了，需要加-f

范例：

```
coscmd delete -f file1/cloudlogo2.png
```

删除文件夹命令如下：

```
coscmd delete -r <cospath>  //命令格式
coscmd delete -r bbb/  //操作示例
coscmd delete -r /  //操作示例
```



### 获取带签名的下载URL

```
coscmd sigurl <cospath>  //命令格式
coscmd signurl bbb/123.txt //操作示例
coscmd signurl bbb/123.txt -t 100//操作示例
```

范例：

```
coscmd signurl file1/cloudlogo2.png
```



### 设置访问控制（ACL）

```
coscmd putbucketacl [--grant-read GRANT_READ]  [--grant-write GRANT_WRITE] [--grant-full-control GRANT_FULL_CONTROL] //命令格式
coscmd putbucketacl --grant-read 12345678,12345678/11111 --grant-write anyone --grant-full-control 12345678/22222 //操作示例
```

范例：

```
coscmd putbucketacl --grant-full-control 100003261464
```

### 获取访问控制（ACL）

使用如下命令获取 Bucket 的访问控制： 

```
coscmd getbucketacl //命令格式
coscmd getbucketacl //操作示例
```

使用如下命令获取 Object 的访问控制： 

```
coscmd getobjectacl <cospath> //命令格式
coscmd getobjectacl aaa/aaa.txt //操作示例
```

范例：

```
coscmd getobjectacl a.png
```

### 用子账号对Bucket进行访问

获取Bucket 的访问控制后可以用子账号对Bucket进行访问

需重新配置config文件，填入参数如下： 

```
coscmd config -u <主账号APPID> -a <子账号SecretId> -s <子账号SecretKey>  -b <主账号bucketname> -r <主账号bucketname地域>
```

范例：

```
coscmd config -u 1234320008 -a ABCDEFG4g7q1azCD2sQBZmaKuJXZUEmBbQtC -s ABC3eN2Vo050kIj3AB9pEFtPj1OaaO1S -b a-1234320008 -e cos.wh.yun.ccb.com
```

### Debug 模式执行命令

在各命令前加上`-d`或者`-debug`，在命令执行的过程中，会显示详细的操作信息 。示例如下： 

```
//显示upload的详细操作信息
coscmd -d upload <localpath> <cospath>  //命令格式
coscmd -d upload /home/aaa/123.txt bbb/123.txt  //操作示例
```
范例：

```
coscmd -d list -a
```



### 一些COSCMD的功能TCE不支持，例如 

```
coscmd restore /file1
```

运行过后不会有反应