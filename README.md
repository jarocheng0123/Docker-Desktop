# 在 Windows 上安装 Docker Desktop  

## 一、系统要求  
### 操作系统  
- **Windows 10 64位**：版本需为 **2004+（Build 19041+）**，推荐使用 **22H2（Build 19045+）**
- **Windows 11 64位**：推荐使用 **22H2 或更高版本**

### 硬件要求  
- **处理器**：64位 CPU，支持 **二级地址转换（SLAT）**
- **内存**：至少 **4GB RAM**  
- **虚拟化**：需在 BIOS/UEFI 中启用 **Intel VT-x/AMD-V**

### 软件依赖  
- **WSL 2**：需安装 **WSL 2 功能**，版本要求 **1.1.3.0 或更高**
- **Windows 虚拟机监控程序平台**：用于支持 Docker 的虚拟化后端


## 二、启用 WSL 2 和虚拟化功能  
### 方法 1：通过命令行快速启用（推荐）  
以 **管理员身份** 打开 PowerShell，运行以下命令：  
```powershell  
# 启用 WSL 2 和虚拟机平台功能  
wsl --install  
```  
- 该命令会自动安装 WSL 2、Linux 内核更新，并启用相关功能，**重启电脑** 后生效

### 方法 2：手动启用（适用于系统限制场景）  
1. 打开 **控制面板** > **程序** > **启用或关闭 Windows 功能**
2. 勾选以下选项：  
   - **适用于 Linux 的 Windows 子系统**  
   - **Windows 虚拟机监控程序平台**  
3. 点击 **确定**，等待安装完成后 **重启电脑**

### 验证 WSL 版本  
```powershell  
wsl -v  # 确保输出版本为 2（WSL 2）  
```  
若版本为 1，可通过以下命令升级（需先备份数据）：  
```powershell  
wsl --set-default-version 2  
```  


## 三、安装 Docker Desktop  
### 步骤 1：下载安装包  
从 Docker 官方网站下载对应版本的安装程序：  
- [Docker Desktop for Windows](https://docs.docker.com/desktop/setup/install/windows-install/)  

### 步骤 2：运行安装程序  
双击下载的 `Docker Desktop Installer.exe`，按提示完成安装：  
- 保持默认设置即可（默认安装路径：`C:\Program Files\Docker\Docker Desktop`）
- 安装完成后，**启动 Docker Desktop**（首次启动需等待后台服务初始化）

### 步骤 3：验证安装  
打开终端（PowerShell 或 Command Prompt），运行：  
```bash  
docker -v  # 输出 Docker 版本信息（如 Docker version 4.38.0）  
docker run hello-world  # 运行测试容器，验证是否正常工作  
```  
若显示 `Hello from Docker!`，则安装成功


## 四、汉化 Docker Desktop（可选）  
### 方法：使用汉化包替换语言文件  
1. **下载汉化包**：  
   从 [DockerDesktop-CN 仓库](https://github.com/asxez/DockerDesktop-CN) 下载对应版本的 `.asar` 文件（文件名格式如 `app-Windows-x86-v2beta.asar`）
   - **注意**：Windows Arm 用户需使用 [汉化脚本](https://github.com/asxez/DDCS)，仓库不提供 Arm 架构的汉化包

2. **替换文件**：  
   - 关闭 Docker Desktop。  
   - 备份原文件：`C:\Program Files\Docker\Docker\frontend\resources\app.asar`
   - 将下载的 `.asar` 文件重命名为 `app.asar`，覆盖原路径文件。  

3. **重启 Docker Desktop**，界面将显示中文
### 注意事项  
- 请确保汉化包版本与 Docker Desktop 版本一致
- 若替换后出现异常，恢复备份的 `app.asar` 文件即可


# 二、推荐镜像与使用示例  
## 1. 镜像获取建议  
推荐从 **[Docker Hub 官方镜像库](https://hub.docker.com/search)** 下载镜像，确保安全性和兼容性  

## 2. 可视化管理工具镜像  
### （1）Portainer CE（推荐）  
**描述**：功能全面的 Docker 可视化管理工具，支持集群管理、权限控制和日志监控
**镜像名**：`portainer/portainer-ce:latest`  
**运行命令**：  
```bash  
docker run -d --name portainer-manager \  
  -p 9000:9000 \          # 映射 Web 端口（浏览器访问 http://localhost:9000）  
  -v /var/run/docker.sock:/var/run/docker.sock \  # 挂载 Docker 守护进程套接字（关键权限）  
  portainer/portainer-ce:latest  
```  
```bash  
docker run -d --name portainer-manager -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock portainer/portainer-ce:latest
```  

**使用说明**：  
- 首次访问需设置管理员密码，后续通过 Web 界面管理 Docker 环境
- 支持本地和远程 Docker 主机连接，适合开发/运维场景。  
![portainer](https://raw.githubusercontent.com/jarocheng0123/Docker-Desktop/refs/heads/main/PNG/portainer.png)

### （2）Dpanel  
**描述**：轻量级工具，适合快速上手，界面简洁但功能较基础。  
**镜像名**：`donknap/dpanel:latest`  
**运行命令**：  
```bash  
docker run -d --name dpanel-server \  
  -p 8080:8080 \          # 映射 Web 端口（浏览器访问 http://localhost:8080）  
  -v /var/run/docker.sock:/var/run/docker.sock \  # 允许工具调用 Docker API  
  donknap/dpanel:latest  
```  

```bash  
docker run -d --name dpanel-server -p 8080:8080 -v /var/run/docker.sock:/var/run/docker.sock donknap/dpanel:latest
```  

**注意**：该项目维护频率较低，适合轻量级临时需求，生产环境建议优先选择 Portainer。  
![donknap](https://raw.githubusercontent.com/jarocheng0123/Docker-Desktop/refs/heads/main/PNG/dpanel.png)

## 3. 带图形界面的容器镜像（VNC 访问）  
**场景**：需要在容器内运行桌面环境（如 Ubuntu GUI），通过 VNC 或浏览器远程访问
**推荐镜像**：`dorowu/ubuntu-desktop-lxde-vnc:latest`  
**运行命令**：  
```bash  
docker run -itd --name ubuntu-gui \  
  -p 6080:80 \           # 浏览器访问（http://localhost:6080，无需 VNC 客户端）  
  -p 5900:5900 \         # VNC 端口（默认密码：vncpassword）  
  -v E:/VM:/shared \     # 挂载 Windows 本地目录到容器内的 /shared（注意路径格式）  
  dorowu/ubuntu-desktop-lxde-vnc:latest  
```  

```bash  
docker run -itd --name ubuntu-gui -p 6080:80 -p 5900:5900 dorowu/ubuntu-desktop-lxde-vnc
```  

```bash  
docker run -itd   --name ubuntu-gui  -v E:/VM:/shared   -p 6080:80   -p 5900:5900   dorowu/ubuntu-desktop-lxde-vnc
```  

**访问方式**：  
- **浏览器访问**：打开 `http://localhost:6080`，输入密码 `vncpassword` 进入桌面
- **VNC 客户端访问**：使用 VNC 工具（如 RealVNC）连接 `localhost:5900`，密码同上
**说明**：  
- 容器内预装 LXDE 桌面环境，支持文件管理器、终端等图形化操作。  
- 挂载的 Windows 路径建议使用 **正斜杠**（如 `E:/VM`）或双反斜杠（`E:\\VM`），避免转义问题。  
![dorowu-web](https://raw.githubusercontent.com/jarocheng0123/Docker-Desktop/refs/heads/main/PNG/web-localhost-6080.png)
![dorowu-vnc](https://raw.githubusercontent.com/jarocheng0123/Docker-Desktop/refs/heads/main/PNG/vnc-127.0.0.1-5900.png)

## 4. 基础操作系统镜像  
### Ubuntu 官方镜像  
**描述**：最常用的 Linux 基础镜像，适用于开发、测试和构建自定义镜像
**镜像名**：`ubuntu:latest`（推荐指定具体版本，如 `ubuntu:22.04`）  
**运行命令**：  
```bash  
docker run -it --name ubuntu-container \  
  ubuntu:latest bash  # 进入容器内的交互式终端（bash 环境）  
```  

```bash  
docker run -it --name ubuntu-container ubuntu:latest bash
```  

**用途**：  
- 直接运行命令：`docker run ubuntu:latest apt-get update`  
- 作为自定义镜像的基础层（Dockerfile 中使用 `FROM ubuntu`）。  


# 三、Docker 基本指令与操作指南  


## 1. 镜像管理  
### 查看本地镜像  
```bash  
docker images  # 列出所有本地镜像（包含 REPOSITORY、TAG、IMAGE ID 等信息）  
docker images -a  # 显示所有镜像（包括中间层镜像）  
docker images -q  # 只显示镜像 ID（用于批量操作）  
```  

### 拉取与删除镜像  
```bash  
docker pull ubuntu:22.04  # 从 Docker Hub 拉取指定版本的 Ubuntu 镜像  
docker rmi ubuntu:22.04  # 删除指定镜像（需确保无容器依赖）  
docker rmi $(docker images -q)  # 删除所有本地镜像（慎用！）  
```  

### 构建自定义镜像  
```bash  
docker build -t myapp:v1 .  # 在当前目录（.）下根据 Dockerfile 构建名为 myapp:v1 的镜像  
docker build -t myapp:v1 -f ./docker/Dockerfile.prod .  # 指定 Dockerfile 路径  
```  

## 2. 容器操作  
### 查看容器状态  
```bash  
docker ps  # 查看运行中的容器  
docker ps -a  # 查看所有容器（包括已停止的）  
docker ps -aq  # 只显示容器 ID（用于批量操作）  
docker ps -f "status=exited"  # 筛选已退出的容器  
```  

### 容器生命周期管理  
```bash  
# 创建并启动容器（以 ubuntu-gui 镜像为例）  
docker run -d --name ubuntu-gui -p 6080:80 dorowu/ubuntu-desktop-lxde-vnc:latest  

# 启动/停止/重启容器  
docker start ubuntu-gui  # 启动已停止的容器  
docker stop ubuntu-gui   # 优雅停止容器（发送 SIGTERM 信号）  
docker restart ubuntu-gui  # 重启容器  

# 强制终止容器（相当于 kill -9）  
docker kill ubuntu-gui  

# 删除容器（需先停止容器，或使用 -f 强制删除）  
docker rm ubuntu-gui  
docker rm -f $(docker ps -aq)  # 强制删除所有容器（慎用！）  
```  

### 进入容器内部  
```bash  
docker exec -it ubuntu-gui bash  # 进入运行中的容器（交互式终端）  
docker exec -it ubuntu-gui sh  # 若容器没有 bash，使用 sh  
docker attach ubuntu-gui  # 附加到容器的主进程（退出会导致容器停止）  
```  

## 3. 容器信息与日志  
### 获取容器 IP 地址  
```bash  
# PowerShell 语法（Windows）  
$containerIP = docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' ubuntu-gui  
Write-Host "容器 IP：$containerIP"  
```  

### 查看端口映射  
```bash  
docker port ubuntu-gui  # 显示容器的端口映射关系（如 0.0.0.0:6080 -> 80/tcp）  
```  

### 查看容器日志  
```bash  
docker logs ubuntu-gui  # 查看容器的标准输出日志  
docker logs -f ubuntu-gui  # 实时跟踪日志（类似 tail -f）  
docker logs --since=1h ubuntu-gui  # 查看最近 1 小时的日志  
```  

## 4. 数据卷与文件操作  
### 挂载数据卷  
```bash  
# 挂载 Windows 本地目录到容器（以 E:/VM 为例）  
docker run -d -v "E:/VM":/shared --name ubuntu-gui dorowu/ubuntu-desktop-lxde-vnc:latest  
# 创建命名数据卷（推荐方式）  
docker run -d -v mydata:/app/data --name myapp myapp:v1  
```  

### 在容器与主机间复制文件  
```bash  
# 从容器复制到主机  
docker cp ubuntu-gui:/etc/hosts .  # 复制容器内的 hosts 文件到当前目录  
# 从主机复制到容器  
docker cp ./config.ini ubuntu-gui:/app/config.ini  
```  

## 5. 系统资源清理  
### 安全清理命令（推荐）  
```bash  
docker system prune  # 清理未使用的镜像、容器、网络和构建缓存  
docker system prune -a  # 清理所有未使用的镜像（包括未被引用的）  
docker system prune --volumes  # 清理未使用的数据卷  
```  

### 彻底清理（慎用！）  
```bash  
# 停止并删除所有容器  
docker stop $(docker ps -aq) 2>/dev/null && docker rm -f $(docker ps -aq) 2>/dev/null  
# 删除所有镜像（含未被引用的）  
docker rmi -f $(docker images -aq) 2>/dev/null  
# 删除所有数据卷  
docker volume rm -f $(docker volume ls -q) 2>/dev/null  
```  