1. 安装VMBox

2. 安装vagrant 

3. cmd命令行输入 

   ```shell
    vagrant init centos/7
   ```

    生成 Vagrantfile文件

4. 添加box 

   ```shell
   vagrant box add --name centos/7 H:\开发软件\虚拟机搭建\CentOS-7-x86_64-Vagrant-1905_01.VirtualBox.box
   ```

5. 打开VMBox然后命令行输入 启动虚拟机

   ```shell
   vagrant up
   ```

6. 启动后登入虚拟机 

   ```shell
   vagrant ssh
   ```

7. 网络端口设置
   cmd命令输入 ipconfig 查看VMBox的ip地址 192.168.56.1
   修改Vagrantfile的 config.vm.network 的 private_network 为 192.168.56.10
   重启虚拟机 vagrant reload
   主机与虚拟机进行ping测试 虚拟机 ip addr查看ip，主机 ipconfig查看ip
   虚拟机ping 192.168.2.106
   主机ping 192.168.56.10

8. 虚拟机Docker安装

9. ```shell
      sudo yum remove docker \
          docker-client \
          docker-client-latest \
          docker-common \
          docker-latest \
          docker-latest-logrotate \
          docker-logrotate \
          docker-engine
      ```

10. ```shell
     sudo yum install -y yum-utils \
     device-mapper-persistent-data \
       lvm2
    ```

11. ```shell
     sudo yum-config-manager \
     --add-repo \
     https://download.docker.com/linux/centos/docker-ce.repo
    ```

12. ```shell
    sudo yum install docker-ce docker-ce-cli containerd.io
    ```

13. 启动docker

   ```shell
   sudo systemctl start docker
   ```

   查看docker

    ```shell
   docker -v
    ```

14. 开机自启动

   ```shell
   sudo systemctl enable docker
   ```

15. 配置镜像加速 - 阿里云 https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors  选择 centos

   ```shell
   sudo mkdir -p /etc/docker
   sudo tee /etc/docker/daemon.json <<-'EOF'
   {
     "registry-mirrors": ["https://ek4svdsm.mirror.aliyuncs.com"]
   }
   EOF
   sudo systemctl daemon-reload
   sudo systemctl restart docker
   ```

16. 安装mysql
      [可以切换用户 su root  密码是vagrant，后续就不用带 sudo了]	
       拉取镜像 

      ```shell
      sudo  docker pull mysql:5.7
      ```

       查看镜像 

      ```shell
      sudo docker images
      ```

       启动容器 

      ```shell
       sudo  docker run -p 3306:3306 --name mysql \
       -v /mydata/mysql/log:/var/log/mysql \
       -v /mydata/mysql/data:/var/lib/mysql \
       -v /mydata/mysql/conf:/etc/mysql \
       -e MYSQL_ROOT_PASSWORD=root \
       -d mysql:5.7
      ```

      查看容器

      ```shell
      docker ps 
      ```

      navicat连接测试

      ```shell
      ip:192.168.56.10
      user:root
      password:root
      ```

      mysql配置文件修改：/mydata/mysql/conf下创建my.cnf输入以下内容

      ```shell
      [client]
      default-character-set=utf8
      
      [mysql]
      default-character-set=utf8
      
      [mysqld]
      init_connect='SET collation_connection = utf8_unicode_ci'
      init_connect='SET NAMES utf8'
      character-set-server=utf8
      collation-server=utf8_unicode_ci
      skip-character-set-client-handshake
      skip-name-resolve
      ```

      重启mysql容器

      ```shell
      docker restart mysql
      ```

      进入容器查看配置文件

      ```shell
      docker exec -it mysql /bin/bash
      cat /etc/mysql/my.cnf
      ```

       退出容器

       ```shell
      exit
       ```

17. 安装 redis
      redis镜像拉取

   ```shell
docker pull redis
   ```

   创建实例并启动

   ```shell
mkdir -p /mydata/redis/conf
touch /mydata/redis/conf/redis.conf

docker run -p 6379:6379 --name redis \
-v /mydata/redis/data:/data \
-v /mydata/redis/conf/redis.conf:/etc/redis/redis.conf \
-d redis redis-server /etc/redis/redis.conf
   ```

   redis的client操作

```sheel
docker exec -it redis redis-cli
```

   修改redis.conf开启AOP存盘

   ```shell
appendonly yes
   ```

   重启redis

   ```shell
docker restart redis
   ```

18. 虚拟机启动自动启动容器
    
```shell
    sudo docker update mysql --restart=always
    sudo docker update redis --restart=always
    ```
    
19. maven配置
    1）阿里云镜像

    ```xml
    <mirror>
    	<id>nexus-aliyun</id>
    	<name>Nexus aliyun</name>
    	<url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    	<mirrorOf>central</mirrorOf>
    </mirror>
    ```

    2）编译配置

    ```xml
    <profile>
      <id>jdk-1.8</id>
    
      <activation>
        <activeByDefault>true</activeByDefault>
      </activation>
    
      <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
    	<maven.compiler.target>1.8</maven.compiler.target>
    	<maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
      </properties>
    </profile>
    ```

20. IDEA 配置，选中我们的maven及配置文件

21. 安装lombok，mybatisx

22. 下载 vscode前端开发工具及插件

    - auto close
    - auto rename
    - chinese
    - eslint
    - html css support
    - html snippets
    - javascript(ES6)
    - live server
    - open in browser
    - vetur

    