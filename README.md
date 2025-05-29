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
- **Windows 虚拟机监控程序平台**：用于支持 Docker 的虚拟化后端
- **WSL 2**：需安装 **WSL 2 功能**，版本要求 **1.1.3.0 或更高**


## 二、启用 WSL 2 和虚拟化功能  
### 方法 1：手动启用（适用于系统限制场景）  
1. 打开 **控制面板** > **程序** > **启用或关闭 Windows 功能**
2. 勾选以下选项：  
   - **Windows 虚拟机监控程序平台**  
   - **适用于 Linux 的 Windows 子系统**  
3. 点击 **确定**，等待安装完成后 **重启电脑**

### 验证 WSL 版本  
```powershell  
wsl -v  # 确保输出版本为 2（WSL 2）  
```  
若版本为 1，可通过以下命令升级或安装[WSL2 Linux 内核更新包](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi) 
```powershell  
wsl --set-default-version 2  
```  

### 方法 2：通过命令行快速启用  
以 **管理员身份** 打开 PowerShell，运行以下命令：  
```powershell  
wsl --install  # 启用 WSL 2 和虚拟机平台功能  
```  
- 该命令会自动安装 WSL 2、Linux 内核更新，并启用相关功能，**重启电脑** 后生效

## 三、安装 Docker Desktop  
### 步骤 1：下载安装包  
从 Docker 官方网站下载对应版本的安装程序：  
- [Docker Desktop for Windows](https://docs.docker.com/desktop/setup/install/windows-install/)  

### 步骤 2：运行安装程序  
双击下载的 `Docker Desktop Installer.exe`，按提示完成安装：  
- 保持默认设置即可（默认安装路径：`C:\Program Files\Docker\Docker Desktop`）
- 安装完成后，**启动 Docker Desktop**（首次启动需等待后台服务初始化）

### 步骤 3：汉化 Docker Desktop 
 - 从 [DockerDesktop-CN 仓库](https://github.com/asxez/DockerDesktop-CN) 下载对应版本的 `.asar` 文件（文件名格式如 `app-Windows-x86-v2beta.asar`）
 - 备份原文件：`C:\Program Files\Docker\Docker\frontend\resources\app.asar`
 - 将下载的 `.asar` 文件重命名为 `app.asar`，覆盖原路径文件。

### 步骤 4：验证安装  
打开终端 PowerShell 运行：  
```bash  
docker -v  # 输出 Docker 版本信息
docker run hello-world  # 运行测试容器，验证是否正常工作  
```  
若显示 `Hello from Docker!`，则安装成功


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
**描述**：轻量级工具，适合快速上手，界面简洁但功能较基础    
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
docker run -itd --name ubuntu-gui -v E:/VM:/shared -p 6080:80 -p 5900:5900 dorowu/ubuntu-desktop-lxde-vnc
```  

**访问方式**：  
- **浏览器访问**：打开 `http://localhost:6080`，输入密码 `vncpassword` 进入桌面
- **VNC 客户端访问**：使用 VNC 工具（如 RealVNC）连接 `localhost:5900`，密码同上  

**说明**：  
- 容器内预装 LXDE 桌面环境，支持文件管理器、终端等图形化操作。  
- 挂载的 Windows 路径建议使用 **正斜杠**（如 `E:/VM`）或双反斜杠（`E:\\VM`），避免转义问题。  
![dorowu-web](https://raw.githubusercontent.com/jarocheng0123/Docker-Desktop/refs/heads/main/PNG/web-localhost-6080.png)

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

### **一、镜像管理**
```bash
# 查看镜像
docker images               # 列出所有本地镜像
docker images -a            # 显示所有镜像（含中间层）
docker images -q            # 只显示镜像ID（用于批量操作）

# 拉取/删除镜像
docker pull [镜像名:标签]     # 拉取镜像（例：ubuntu:22.04）
docker rmi -f [镜像ID/名称]  # 强制删除镜像（-f：忽略依赖）
docker rmi -f $(docker images -q)  # 清空所有镜像（慎用！）

# 构建镜像
docker build -t [名称:标签] .  # 构建镜像（当前目录的Dockerfile）
docker build -f [文件路径] .   # 指定Dockerfile路径
```

### **二、容器操作**
```bash
# 查看容器
docker ps               # 查看运行中的容器
docker ps -a            # 查看所有容器（含已停止）
docker ps -aq           # 只显示容器ID
docker ps -f "status=exited"  # 筛选已退出的容器

# 容器生命周期
docker run -d -p [主机端口]:[容器端口] --name [名称] [镜像]  # 创建并启动容器
docker start/stop/restart [容器名/ID]  # 启动/停止/重启容器
docker kill [容器名/ID]      # 强制终止容器
docker rm -f [容器名/ID]     # 强制删除容器
docker rm -f $(docker ps -aq)  # 清空所有容器（慎用！）

# 进入容器
docker exec -it [容器名] bash  # 交互式终端进入容器（有bash时）
docker exec -it [容器名] sh    # 无bash时使用sh
docker attach [容器名]         # 附加到主进程（退出会停止容器）
```

### **三、容器信息与日志**
```bash
# 网络信息
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' [容器名]  # 获取IP
docker port [容器名]           # 查看端口映射

# 日志查看
docker logs [容器名]           # 查看标准输出日志
docker logs -f [容器名]        # 实时跟踪日志（类似tail -f）
docker logs --since=1h [容器名]  # 查看最近1小时日志
```

### **四、数据卷与文件操作**
```bash
# 数据卷挂载
# Windows：
docker run -d -v "E:/VM":/shared [镜像]
# Linux/macOS：
docker run -d -v ~/VM:/shared [镜像]
# 命名数据卷（推荐）：
docker run -d -v [卷名]:/app/data [镜像]

# 文件复制
docker cp [容器名]:/路径/文件 .   # 从容器复制到主机
docker cp ./文件 [容器名]:/路径/  # 从主机复制到容器
```

### **五、系统清理**
```bash
# 安全清理（推荐）
docker system prune           # 清理未使用的镜像、容器、网络
docker system prune -a        # 清理所有未使用镜像（含未引用的）
docker system prune --volumes  # 清理未使用的数据卷

# 彻底清理（慎用！）
docker stop $(docker ps -aq) && docker rm -f $(docker ps -aq)  # 停止并删除所有容器
docker rmi -f $(docker images -aq)                             # 删除所有镜像
docker volume rm -f $(docker volume ls -q)                    # 删除所有数据卷
```