## 使用步骤

### 1. 克隆【必需】
```bash
$ git clone git@github.com:youshengyouse/docker-nginx-php-mysql-redis.git
# $ git clone git@gitee.com:advance/docker-nginx-php-mysql-redis.git # 中国大陆用户下载这个
```
### 2. mysql【必需】
为了安全，建议修改.env中

```
MYSQL5_ROOT_PASSWORD=新密码
```

### 3. redis【必需】
为了安全，建议修改redis配置文件，

```
requirepass 新密码
```

最好将port,bind也做一个修改，其中bind与docker-compose.yml中的php容器的ip相关

 

### 4. php
- 默认使用php7.4版本，可切换为7.2，如果要使用其它版本，请访问[docker hub](https://hub.docker.com/_/php?tab=tags)
- 如果php代码中使用curl,file_get_contents等命令读取url内容时，得设置一个extra_hosts

### 5. 修改或增加映射目录
默认已有映射为

- tutorials映射到/999-tutorials
- packages映射到/000-packages-private

用户可以根据情况映射目录

### 6. 修改nginx配置
可以根据实际情况进行虚拟主机的配置，建议`services/nginx/conf/all_websites`下新建一个文件，然后在`nginx.conf`中导入,如

```nginx
# 网站不多时，放在一个文件里
include all_websites/localhost.conf; 
include all_websites/tutorial.conf;
include all_websites/study.conf; # 这是加上的，也可以使用通配置符导入多个配置文件

# 网站多时，分开管理，尽量文件名与主机名一致
# include all_websites/*.conf;
```

如`study.conf`，这是一个简单的例子，只做参考

```nginx
server {
    listen       80;   
    server_name ~^(?<SITE>[\w-]+).study$;
    set $root /study/$SITE/public;
    root $root;
    if ($time_iso8601 ~ "^(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})") {}
    access_log /var/log/nginx/study__${SITE}____$year-$month-${day}____access.log;  
    error_log /var/log/nginx/study____error.log;
    index  index.php index.html index.htm;  
    location ~ \.php$ {
        fastcgi_pass   _php;
        include        fastcgi-php.conf;
        include        fastcgi_params;     
    }
    location / {
        try_files $uri $uri/ /index.php?$query_string;
        index index.php index.html;
    }
}
```

在 `C:\Windows\System32\drivers\etc\HOSTS`中添加

```ini
# 这只是例子，请根据自己的情况写
127.0.0.1 jigsaw.study
127.0.0.1 tailwind.study
```



### 7. 别名定义
将一些常用的命令定义成alias，提高工作效率


## 🔥重要提示！！！！！！

- mysql密码设置后，得删除data目录下全部内容才会生效，在生产环境不要使用默认的值，请修改为一个复杂的密码
- redis一定要修改配置文件，修改bind,port,requirepass几个地方，在生产环境不要使用默认的值，否则黑客易注入挖矿病毒，有很多人中招。

## 目录说明

- tmp，composer缓存，可随意删除
- logs，日志目录，包括nginx和php日志，可随意删除，不影响功能，但它是用来排错用的
- data，mysql数据库，redis数据等

## ✍常用命令
```bash
docker ps        # 查看容器
docker images    # 查看镜像
```



## 待添加功能
*OpenResty*


## alias配置

###  win10中的powershell中
> 配置文件 `C:\Users\用户名\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1`
```bash
# 请先配置好当前项目中docker目录，这是我的目录，你的根据实际情况修改
$dockerPath = "F:\www\docker-nginx-php-mysql-redis"

function _up{
  cd $dockerPath
  docker-compose -f docker-compose.yml up -d a1 a2 a3 a4 a5
}

# 如果同时管理多个服务器，每个服务器的配置不一样时，使用-f指定其对应配置文件
function _hongkong{
  cd $dockerPath
  docker-compose -f __docker-compose_hongkong.yml up -d a1 a2 a3 a4 a5
}

function _shanghai{
  cd $dockerPath
  docker-compose -f __docker-compose_shanghai.yml up -d a1 a2 a3 a4 a5
}

function _down{
  cd $dockerPath
  docker-compose down
}

function _b1{
  cd $dockerPath
  docker exec -it b1 sh
}

function _b2{
  cd $dockerPath
  docker exec -it b2 bash
}

function _b3{
  cd $dockerPath
  docker exec -it b3 sh
}

function _b4{
  cd $dockerPath
  docker exec -it b4 sh
}

function _b5{
  cd $dockerPath
  docker exec -it b5 sh
}

set-alias up _up         # 启动本地所有的容器
set-alias hk _hongkong   # 启动香港服务器所有的容器
set-alias sh _shanghai   # 启动上海服务器所有的容器
set-alias down _down     # 关闭所有的容器
set-alias b1 _b1         # nginx
set-alias b2 _b2         # php
set-alias b3 _b3         # mysql
set-alias b4 _b4         # phpmyadmin
set-alias b5 _b5         # redis
```

### win10的git bash中配置

```bash

# win10下别名步骤
# 打开 git bash终端
# cd ~
# vi .bashrc
# 输入 i 进入编辑模式,输入别名.，按esc并:wq(save and exit)
# 关闭git bash终端，重新开启git bash
# 在同目录下建文件 .bash_profile，输入 if [ -f ~/.bashrc ]; then . ~/.bashrc; fi


DOCKERPATH=/f/www/docker-nginx-php-mysql-redis                                               # 指定dnmp目录

alias up="cd ${DOCKERPATH} && docker-compose up -d a1 a2 a3 a4 a5"                                     # 启动本地所有docker容器
alias hk="cd ${DOCKERPATH} && docker-compose -f __docker-compose_hongkong.yml up -d a1 a2 a3 a4 a5"    # 启动香港服务器所有docker容器
alias sh="cd ${DOCKERPATH} && docker-compose -f __docker-compose_shanghai.yml up -d a1 a2 a3 a4 a5"    # 启动上海服务器所有docker容器
alias down="cd ${DOCKERPATH} && docker-compose down"

alias delc='cd ${DOCKERPATH} && docker rm -f `docker ps -aq`'       # 删除所有容器
alias deli='cd ${DOCKERPATH} && docker rmi -f `docker images -aq`'  # 删除所有镜像

alias b1='cd ${DOCKERPATH} && winpty docker exec -it b1 sh'
alias b2='cd ${DOCKERPATH} && winpty docker exec -it b2 bash'
alias b3='cd ${DOCKERPATH} && winpty docker exec -it b3 bash'
alias b4='cd ${DOCKERPATH} && winpty docker exec -it b4 bash'
alias b5='cd ${DOCKERPATH} && winpty docker exec -it b5 sh'
```

### ubuntu中定义

> 配置文件 .bash_aliases，尽量不要修改.bashrc文件
```bash
# 用户根据自己实际情况，可以定义多个别名，也可以直接修改这里
DOCKERPATH=/000/dnmp_on_gitee_github    # 指定dnmp目录
alias up="cd ${DOCKERPATH} && docker-compose up -d a1 a2 a3 a4 a5"                                     # 启动本地所有docker容器
alias down="cd ${DOCKERPATH} && docker-compose down"
alias delc='cd ${DOCKERPATH} && docker rm -f `docker ps -aq`'       # 删除所有容器
alias deli='cd ${DOCKERPATH} && docker rmi -f `docker images -aq`'  # 删除所有镜像
alias b1='cd ${DOCKERPATH} && docker exec -it b1 bash'
alias b2='cd ${DOCKERPATH} && docker exec -it b2 bash'
alias b3='cd ${DOCKERPATH} && docker exec -it b3 bash'
alias b4='cd ${DOCKERPATH} && docker exec -it b4 bash'
alias b5='cd ${DOCKERPATH} && docker exec -it b5 bash'

```

## 填坑
错误描述
```bash
error during connect: Get http://%2F%2F.%2Fpipe%2Fdocker_engine/v1.40/containers/json: open //./pipe/docker_engine: The system cannot find the file specified. In the default daemon configuration on Windows, the docker client must be run elevated to connect. This error may also indicate that the docker daemon is not running.
```

表示`docker desktop`没有正常启动，请重新启动

```bash
ERROR: Pool overlaps with other one on this address space
```
原因是已有一个网络占用了subnet网段地址，`docker network ls`，查看，删除除了 `bridge`，`host `，`none`之外的网络