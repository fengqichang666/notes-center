


### 常用 Windows 命令行工具及选项表格

| **命令**     | **选项**                        | **描述**                                             | **示例**                              |
|--------------|---------------------------------|------------------------------------------------------|---------------------------------------|
| `dir`        | `/A`                            | 显示具有指定属性的文件                                | `dir /A`                             |
|              | `/P`                            | 分页显示结果                                         | `dir /P`                             |
|              | `/Q`                            | 显示文件的所有者                                     | `dir /Q`                             |
|              | `/S`                            | 显示当前目录及其子目录中的文件                       | `dir /S`                             |
|              | `/T:<C|A|W>`                    | 使用指定时间属性进行排序（创建、访问、写入）         | `dir /T:C`                           |
|              | `/O:<N|S|E|D|G>`                | 按指定顺序排序（名称、大小、扩展名、日期、组目录）   | `dir /O:N`                           |
| `cd`         | `/D`                            | 切换驱动器并更改目录                                 | `cd /D D:\path`                      |
| `copy`       | `/V`                            | 验证新文件是否写入正确                               | `copy /V source.txt destination.txt` |
|              | `/Y`                            | 禁止覆盖文件时的确认提示                             | `copy /Y source.txt destination.txt` |
|              | `/A`                            | 复制 ASCII 文件                                      | `copy /A source.txt destination.txt` |
|              | `/B`                            | 复制二进制文件                                       | `copy /B source.bin destination.bin` |
| `xcopy`      | `/E`                            | 复制所有子目录，包括空目录                           | `xcopy /E source_dir destination_dir`|
|              | `/S`                            | 复制所有子目录，不包括空目录                         | `xcopy /S source_dir destination_dir`|
|              | `/H`                            | 复制隐藏文件和系统文件                               | `xcopy /H source_dir destination_dir`|
|              | `/Y`                            | 禁止覆盖文件时的确认提示                             | `xcopy /Y source_dir destination_dir`|
|              | `/C`                            | 即使出现错误也继续复制                               | `xcopy /C source_dir destination_dir`|
| `del`        | `/P`                            | 删除前提示确认                                       | `del /P file.txt`                    |
|              | `/F`                            | 强制删除只读文件                                     | `del /F file.txt`                    |
|              | `/S`                            | 从所有子目录中删除指定文件                           | `del /S file.txt`                    |
|              | `/Q`                            | 安静模式，不提示确认                                 | `del /Q file.txt`                    |
| `move`       | `/Y`                            | 禁止覆盖文件时的确认提示                             | `move /Y source.txt destination.txt` |
|              | `/N`                            | 使用短文件名                                         | `move /N source.txt destination.txt` |
| `attrib`     | `+R/-R`                         | 设置/清除只读属性                                    | `attrib +R file.txt`                 |
|              | `+H/-H`                         | 设置/清除隐藏属性                                    | `attrib +H file.txt`                 |
|              | `+S/-S`                         | 设置/清除系统文件属性                                | `attrib +S file.txt`                 |
|              | `+A/-A`                         | 设置/清除存档属性                                    | `attrib +A file.txt`                 |
| `tasklist`   | `/SVC`                          | 显示每个进程的服务                                    | `tasklist /SVC`                      |
|              | `/V`                            | 显示详细信息                                         | `tasklist /V`                        |
|              | `/FI <filter>`                  | 根据条件过滤进程                                     | `tasklist /FI "STATUS eq RUNNING"`   |
| `taskkill`   | `/PID <pid>`                    | 通过进程 ID 终止进程                                 | `taskkill /PID 1234`                 |
|              | `/IM <imagename>`               | 通过进程名称终止进程                                 | `taskkill /IM notepad.exe`           |
|              | `/F`                            | 强制终止进程                                         | `taskkill /F /IM notepad.exe`        |
|              | `/T`                            | 终止指定进程及其子进程                               | `taskkill /T /IM notepad.exe`        |
| `ping`       | `-t`                            | 持续 ping 目标                                       | `ping -t 8.8.8.8`                    |
|              | `-n <count>`                    | 指定发送的回显请求数                                 | `ping -n 4 8.8.8.8`                  |
|              | `-l <size>`                     | 指定数据包大小                                       | `ping -l 1024 8.8.8.8`               |
|              | `-a`                            | 解析地址到主机名                                     | `ping -a 8.8.8.8`                    |
| `ipconfig`   | `/all`                          | 显示详细的网络适配器信息                             | `ipconfig /all`                      |
|              | `/release`                      | 释放 DHCP 分配的 IP 地址                             | `ipconfig /release`                  |
|              | `/renew`                        | 更新 DHCP 分配的 IP 地址                             | `ipconfig /renew`                    |
|              | `/flushdns`                     | 清除 DNS 缓存                                        | `ipconfig /flushdns`                 |
| `netstat`    | `-a`                            | 显示所有连接和监听端口                               | `netstat -a`                         |
|              | `-n`                            | 使用数字格式显示地址和端口                           | `netstat -n`                         |
|              | `-o`                            | 显示每个连接的进程 ID                                | `netstat -o`                         |
|              | `-r`                            | 显示路由表                                           | `netstat -r`                         |
| `systeminfo` | `/FO <format>`                  | 指定输出格式（TABLE、LIST、CSV）                     | `systeminfo /FO TABLE`               |
|              | `/NH`                           | 不显示列标题                                         | `systeminfo /FO TABLE /NH`           |
| `shutdown`   | `/s`                            | 关闭计算机                                           | `shutdown /s`                        |
|              | `/r`                            | 关闭并重新启动计算机                                 | `shutdown /r`                        |
|              | `/t <seconds>`                  | 设置关闭前的等待时间                                 | `shutdown /s /t 60`                  |
|              | `/f`                            | 强制关闭应用程序                                     | `shutdown /s /f`                     |

## Linux

### Linux 常用命令表格

| **类别**         | **命令**                        | **描述**                                             | **示例**                              |
|------------------|---------------------------------|------------------------------------------------------|---------------------------------------|
| **文件操作**     | `ls`                            | 列出目录内容                                         | `ls -l`                              |
|                  | `cd <directory>`                | 切换目录                                             | `cd /home/user`                      |
|                  | `pwd`                           | 显示当前目录路径                                     | `pwd`                                |
|                  | `cp <source> <destination>`     | 复制文件或目录                                       | `cp file.txt /backup/`               |
|                  | `mv <source> <destination>`     | 移动或重命名文件或目录                               | `mv oldname.txt newname.txt`         |
|                  | `rm <file>`                     | 删除文件                                             | `rm file.txt`                        |
|                  | `mkdir <directory>`             | 创建目录                                             | `mkdir newfolder`                    |
|                  | `rmdir <directory>`             | 删除空目录                                           | `rmdir emptyfolder`                  |
|                  | `touch <file>`                  | 创建空文件或更新文件时间戳                           | `touch newfile.txt`                  |
|                  | `cat <file>`                    | 显示文件内容                                         | `cat file.txt`                       |
|                  | `nano <file>`                   | 使用 nano 编辑文件                                   | `nano file.txt`                      |
|                  | `find <directory> -name <name>` | 查找文件或目录                                       | `find /home -name "*.txt"`           |
| **权限管理**     | `chmod <mode> <file>`           | 更改文件权限                                         | `chmod 755 script.sh`                |
|                  | `chown <owner>:<group> <file>`  | 更改文件所有者                                       | `chown user:group file.txt`          |
| **系统信息**     | `uname -a`                      | 显示系统信息                                         | `uname -a`                           |
|                  | `df -h`                         | 显示磁盘使用情况                                     | `df -h`                              |
|                  | `free -h`                       | 显示内存使用情况                                     | `free -h`                            |
|                  | `top`                           | 实时显示系统进程信息                                 | `top`                                |
|                  | `htop`                          | 更友好的实时系统进程信息显示工具                     | `htop`                               |
|                  | `ps aux`                        | 显示所有进程                                         | `ps aux`                             |
|                  | `uptime`                        | 显示系统运行时间                                     | `uptime`                             |
|                  | `who`                           | 显示当前登录用户                                     | `who`                                |
| **网络管理**     | `ifconfig`                      | 显示或配置网络接口                                   | `ifconfig`                           |
|                  | `ping <host>`                   | 测试网络连通性                                       | `ping google.com`                    |
|                  | `netstat -tuln`                 | 显示网络连接状态                                     | `netstat -tuln`                      |
|                  | `ss -tuln`                      | 更现代的网络连接状态显示工具                         | `ss -tuln`                           |
|                  | `curl <url>`                    | 发送 HTTP 请求并显示响应                             | `curl http://example.com`            |
|                  | `wget <url>`                    | 下载文件                                             | `wget http://example.com/file.zip`   |
| **进程管理**     | `kill <pid>`                    | 终止指定进程                                         | `kill 1234`                          |
|                  | `killall <process_name>`        | 终止所有指定名称的进程                               | `killall firefox`                    |
|                  | `pkill <pattern>`               | 根据模式终止进程                                     | `pkill -f myscript`                  |
| **压缩解压**     | `tar -czvf <archive.tar.gz> <file>` | 创建压缩归档文件                                    | `tar -czvf archive.tar.gz myfolder`  |
|                  | `tar -xzvf <archive.tar.gz>`    | 解压缩归档文件                                       | `tar -xzvf archive.tar.gz`           |
|                  | `zip <archive.zip> <file>`      | 创建 ZIP 压缩文件                                    | `zip archive.zip file.txt`           |
|                  | `unzip <archive.zip>`           | 解压 ZIP 文件                                        | `unzip archive.zip`                  |
| **包管理**       | `apt-get update`                | 更新包列表（Debian/Ubuntu 系）                        | `sudo apt-get update`                |
|                  | `apt-get install <package>`     | 安装软件包（Debian/Ubuntu 系）                        | `sudo apt-get install nginx`         |
|                  | `yum update`                    | 更新包列表（CentOS/RHEL 系）                          | `sudo yum update`                    |
|                  | `yum install <package>`         | 安装软件包（CentOS/RHEL 系）                          | `sudo yum install nginx`             |
| **用户管理**     | `adduser <username>`            | 添加新用户                                           | `sudo adduser newuser`               |
|                  | `passwd <username>`             | 修改用户密码                                         | `sudo passwd newuser`                |
|                  | `usermod -aG <group> <username>`| 将用户添加到组                                       | `sudo usermod -aG sudo newuser`      |
|                  | `deluser <username>`            | 删除用户                                             | `sudo deluser olduser`               |
|                  | `groupadd <group>`              | 创建新组                                             | `sudo groupadd newgroup`             |
|                  | `groupdel <group>`              | 删除组                                               | `sudo groupdel oldgroup`             |


### 常用 Linux 命令及选项表格

| **命令** | **选项** | **描述**                                   | **示例**                        |
|----------|----------|--------------------------------------------|---------------------------------|
| `ls`     | `-l`     | 使用长格式列出文件详细信息                 | `ls -l`                         |
|          | `-a`     | 显示所有文件，包括隐藏文件                 | `ls -a`                         |
|          | `-h`     | 以人类可读的格式显示文件大小               | `ls -lh`                        |
|          | `-R`     | 递归列出目录及其子目录中的所有文件         | `ls -R`                         |
|          | `-t`     | 按时间排序列出文件                         | `ls -t`                         |
|          | `-r`     | 逆序排列                                   | `ls -r`                         |
| `cd`     | `-P`     | 使用物理目录结构（忽略符号链接）           | `cd -P /path`                   |
|          | `-L`     | 使用逻辑目录结构（默认行为）               | `cd -L /path`                   |
| `cp`     | `-r`     | 递归复制目录及其内容                       | `cp -r source/ destination/`    |
|          | `-i`     | 在覆盖文件之前提示确认                     | `cp -i file1 file2`             |
|          | `-u`     | 仅在源文件较新或目标文件不存在时复制       | `cp -u file1 file2`             |
|          | `-v`     | 显示详细的复制过程                         | `cp -v file1 file2`             |
| `mv`     | `-i`     | 在覆盖文件之前提示确认                     | `mv -i oldname newname`         |
|          | `-u`     | 仅在源文件较新或目标文件不存在时移动       | `mv -u oldname newname`         |
|          | `-v`     | 显示详细的移动过程                         | `mv -v oldname newname`         |
| `rm`     | `-r`     | 递归删除目录及其内容                       | `rm -r directory`               |
|          | `-f`     | 强制删除，不提示确认                       | `rm -f file`                    |
|          | `-i`     | 删除前提示确认                             | `rm -i file`                    |
| `find`   | `-name`  | 按名称查找文件                             | `find /home -name "*.txt"`      |
|          | `-type`  | 按文件类型查找                             | `find /home -type d`            |
|          | `-size`  | 按文件大小查找                             | `find /home -size +100M`        |
|          | `-exec`  | 对找到的每个文件执行指定命令               | `find /home -name "*.txt" -exec cat {} \;` |
| `chmod`  | `-R`     | 递归更改目录及其内容的权限                 | `chmod -R 755 directory`        |
| `chown`  | `-R`     | 递归更改目录及其内容的所有者               | `chown -R user:group directory` |
| `ps`     | `-e`     | 显示所有进程                               | `ps -e`                         |
|          | `-f`     | 使用完整格式显示进程信息                   | `ps -f`                         |
|          | `-u`     | 显示特定用户的进程                         | `ps -u username`                |
|          | `-aux`   | 显示所有进程，包括其他用户的进程           | `ps aux`                        |
| `kill`   | `-9`     | 强制终止进程                               | `kill -9 1234`                  |
|          | `-15`    | 优雅地终止进程（默认信号）                 | `kill -15 1234`                 |
| `tar`    | `-c`     | 创建新的归档文件                           | `tar -cvf archive.tar directory`|
|          | `-x`     | 解压归档文件                               | `tar -xvf archive.tar`          |
|          | `-v`     | 显示详细信息                               | `tar -cvf archive.tar directory`|
|          | `-f`     | 指定归档文件名                             | `tar -cvf archive.tar directory`|
|          | `-z`     | 使用 gzip 压缩或解压                       | `tar -czvf archive.tar.gz directory`|
| `grep`   | `-i`     | 忽略大小写                                 | `grep -i "pattern" file`        |
|          | `-r`     | 递归搜索目录                               | `grep -r "pattern" directory`   |
|          | `-v`     | 反转匹配，显示不匹配的行                   | `grep -v "pattern" file`        |
|          | `-n`     | 显示匹配的行号                             | `grep -n "pattern" file`        |
| `curl`   | `-O`     | 将下载的文件保存到本地文件系统             | `curl -O http://example.com/file` |
|          | `-o`     | 将下载的文件保存为指定名称                 | `curl -o filename http://example.com/file` |
|          | `-I`     | 仅获取 HTTP 响应头                         | `curl -I http://example.com`    |
|          | `-X`     | 指定 HTTP 请求方法                         | `curl -X POST http://example.com` |
| `wget`   | `-O`     | 将下载的文件保存为指定名称                 | `wget -O filename http://example.com/file` |
|          | `-c`     | 继续下载中断的文件                         | `wget -c http://example.com/file` |
|          | `-r`     | 递归下载                                   | `wget -r http://example.com`    |



## Docker

### Docker 常用命令表格

| **类别**         | **命令**                                                     | **描述**                         | **示例**                                          |
| ---------------- | ------------------------------------------------------------ | -------------------------------- | ------------------------------------------------- |
| **基本命令**     | `sudo systemctl start docker`                                | 启动 Docker 服务                 | `sudo systemctl start docker`                     |
|                  | `sudo systemctl stop docker`                                 | 停止 Docker 服务                 | `sudo systemctl stop docker`                      |
|                  | `sudo systemctl restart docker`                              | 重启 Docker 服务                 | `sudo systemctl restart docker`                   |
|                  | `sudo systemctl status docker`                               | 查看 Docker 服务状态             | `sudo systemctl status docker`                    |
|                  | `systemctl enable docker`                                    | Docker开机自启                   |                                                   |
|                  | `docker update --restart=always [容器名/容器id]`             | Docker容器开机自启               |                                                   |
| **镜像管理**     | `docker pull <image_name>`                                   | 拉取镜像                         | `docker pull ubuntu:latest`                       |
|                  | `docker images`                                              | 列出本地镜像                     | `docker images`                                   |
|                  | `docker rmi <image_id>`                                      | 删除镜像                         | `docker rmi d1b55f0f4f54`                         |
|                  | `docker build -t <image_name>:<tag> <path_to_dockerfile>`    | 构建镜像                         | `docker build -t myapp:latest .`                  |
| **容器管理**     | `docker run -it --name <container_name> <image_name>`        | 运行容器                         | `docker run -it --name mycontainer ubuntu`        |
|                  | `docker ps`                                                  | 列出运行中的容器                 | `docker ps`                                       |
|                  | `docker ps -a`                                               | 列出所有容器（包括已停止的容器） | `docker ps -a`                                    |
|                  | `docker stop <container_id>`                                 | 停止容器                         | `docker stop 1a2b3c4d5e6f`                        |
|                  | `docker start <container_id>`                                | 启动已停止的容器                 | `docker start 1a2b3c4d5e6f`                       |
|                  | `docker rm <container_id>`                                   | 删除容器                         | `docker rm 1a2b3c4d5e6f`                          |
|                  | `docker logs <container_id>`                                 | 查看容器日志                     | `docker logs 1a2b3c4d5e6f`                        |
|                  | `docker exec -it <container_id> /bin/bash`                   | 进入容器的交互式终端             | `docker exec -it 1a2b3c4d5e6f /bin/bash`          |
| **网络管理**     | `docker network ls`                                          | 列出 Docker 网络                 | `docker network ls`                               |
|                  | `docker network create <network_name>`                       | 创建网络                         | `docker network create mynetwork`                 |
|                  | `docker network rm <network_name>`                           | 删除网络                         | `docker network rm mynetwork`                     |
|                  | `docker network connect <network_name> <container_name>`     | 连接容器到网络                   | `docker network connect mynetwork mycontainer`    |
|                  | `docker network disconnect <network_name> <container_name>`  | 断开容器与网络的连接             | `docker network disconnect mynetwork mycontainer` |
| **数据卷管理**   | `docker volume create <volume_name>`                         | 创建数据卷                       | `docker volume create myvolume`                   |
|                  | `docker volume ls`                                           | 列出数据卷                       | `docker volume ls`                                |
|                  | `docker volume rm <volume_name>`                             | 删除数据卷                       | `docker volume rm myvolume`                       |
|                  | `docker run -it -v <volume_name>:/path/in/container <image_name>` | 挂载数据卷到容器                 | `docker run -it -v myvolume:/data ubuntu`         |
| **其他常用命令** | `docker --version`                                           | 查看 Docker 版本                 | `docker --version`                                |
|                  | `docker info`                                                | 查看 Docker 信息                 | `docker info`                                     |




### 常用 Docker 命令及选项表格

| **命令**          | **选项**                        | **描述**                                             | **示例**                              |
|-------------------|---------------------------------|------------------------------------------------------|---------------------------------------|
| `docker run`      | `-d`                            | 后台运行容器                                         | `docker run -d nginx`                |
|                   | `-p <host_port>:<container_port>`| 映射端口                                             | `docker run -p 80:80 nginx`          |
|                   | `--name <name>`                 | 指定容器名称                                         | `docker run --name mynginx nginx`    |
|                   | `-v <host_dir>:<container_dir>` | 挂载卷                                               | `docker run -v /host/data:/data nginx`|
|                   | `--rm`                          | 容器退出时自动删除                                   | `docker run --rm nginx`              |
|                   | `-e <env_var>=<value>`          | 设置环境变量                                         | `docker run -e MY_VAR=value nginx`   |
|                   | `-it`                           | 交互模式运行容器                                     | `docker run -it ubuntu /bin/bash`    |
| |  | 完整示例 | `docker run -d \  --name mysql \   -p 3306:3306 \   -e TZ=Asia/Shanghai \   -e MYSQL_ROOT_PASSWORD=123 \   mysql` |
| `docker ps`       | `-a`                            | 列出所有容器                                         | `docker ps -a`                       |
|                   | `-q`                            | 仅显示容器 ID                                        | `docker ps -q`                       |
|                   | `--filter <filter>`             | 根据条件过滤容器                                     | `docker ps --filter "status=running"` |
| | `--format` | 格式化 | `docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"` |
| `docker images`  | `-a`                            | 列出所有镜像                                         | `docker images -a`                   |
|                   | `-q`                            | 仅显示镜像 ID                                        | `docker images -q`                   |
|                   | `--filter <filter>`             | 根据条件过滤镜像                                     | `docker images --filter "dangling=true"` |
| `docker pull`     | `--all-tags`                    | 拉取所有标签的镜像                                   | `docker pull --all-tags nginx`       |
|                   | `-q`                            | 安静模式，不显示进度条                               | `docker pull -q nginx`               |
| `docker build`    | `-t <name:tag>`                 | 为构建的镜像指定名称和标签                           | `docker build -t myimage:latest .`   |
|                   | `-f <Dockerfile>`               | 指定 Dockerfile 文件路径                             | `docker build -f /path/to/Dockerfile .` |
|                   | `--no-cache`                    | 不使用缓存构建镜像                                   | `docker build --no-cache -t myimage .` |
| `docker exec`     | `-it`                           | 以交互模式在容器中执行命令                           | `docker exec -it mycontainer /bin/bash` |
|                   | `-d`                            | 在后台执行命令                                       | `docker exec -d mycontainer command` |
| `docker stop`     | `-t <seconds>`                  | 在指定时间后强制停止容器                             | `docker stop -t 10 mycontainer`      |
| `docker rm`       | `-f`                            | 强制删除运行中的容器                                 | `docker rm -f mycontainer`           |
|                   | `-v`                            | 删除与容器关联的卷                                   | `docker rm -v mycontainer`           |
| `docker rmi`      | `-f`                            | 强制删除镜像                                         | `docker rmi -f myimage`              |
|                   | `--no-prune`                    | 不删除未被使用的父镜像                               | `docker rmi --no-prune myimage`      |
| `docker logs`     | `-f`                            | 实时跟踪日志输出                                     | `docker logs -f mycontainer`         |
|                   | `--tail <lines>`                | 仅显示最后若干行日志                                 | `docker logs --tail 100 mycontainer` |
| `docker-compose`  | `up`                            | 启动服务                                             | `docker-compose up`                  |
|                   | `-d`                            | 后台运行服务                                         | `docker-compose up -d`               |
|                   | `--build`                       | 构建服务镜像                                         | `docker-compose up --build`          |
|                   | `down`                          | 停止并删除服务                                       | `docker-compose down`                |
|                   | `-v`                            | 删除与服务关联的卷                                   | `docker-compose down -v`             |
|                   | `logs`                          | 查看服务日志                                         | `docker-compose logs`                |
|                   | `-f`                            | 实时跟踪日志输出                                     | `docker-compose logs -f`             |

给常用Docker命令起别名

```bash
# 修改/root/.bashrc文件
vi /root/.bashrc
内容如下：
# .bashrc

# User specific aliases and functions

alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
alias dps='docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"'
alias dis='docker images'

# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi
```

执行命令使别名生效

```bash
source /root/.bashrc
```

### 数据卷命令

| **命令**              | **说明**             |
| :-------------------- | :------------------- |
| docker volume create  | 创建数据卷           |
| docker volume ls      | 查看所有数据卷       |
| docker volume rm      | 删除指定数据卷       |
| docker volume inspect | 查看某个数据卷的详情 |
| docker volume prune   | 清除数据卷           |