# 🚀 JEECG BOOT Docker 环境搭建完整复盘
## 前情摘要 （git拉取了一个Jeecg的ERP项目，希望能在本地部署对其进行测试）
```powershell
git clone https://gitee.com/jeecg/jeecg-boot.git
```
## 一、问题起源

**原始错误**：Mave镜像的JDK版本不兼容，项目文档中指出需要JDK17

## 二、环境信息

| 项目 | 信息 |
|------|------|
| 项目名称 | JEECG BOOT 3.9.2 |
| 项目路径 | `D:\test-project\jeecg-boot\jeecg-boot` |
| 构建工具 | Maven |
| 所需 JDK | 17 |
| 操作系统 | Windows |
| Shell 环境 | PowerShell |

## 三、解决步骤

### 3.1 拉取正确的 Maven 镜像

```powershell
# 拉取支持 JDK 17 的 Maven 镜像（标准版）
docker pull maven:3.9.9-eclipse-temurin-17
```
### 3.2 编译命令执行

```powershell
# PowerShell 语法（注意使用 ${PWD} 而不是 $(pwd)）
docker run --rm -v ${PWD}:/app -w /app maven:3.9.9-eclipse-temurin-17 mvn clean compile
```
### 3.3 打包项目

```powershell
# 编译 + 打包（生成 JAR 文件）
docker run --rm -v ${PWD}:/app -w /app maven:3.9.9-eclipse-temurin-17 mvn clean package -DskipTests
```
### 3.4 启动

```powershell
docker-compose up -d
```
后端及MySQL启动成功，但依旧无网页出现，应该是前端也需手动部署（麻烦死了😭）
因为前面已经拉取了，在终端cd进入jeecgboot-vue3的文件夹，pnpm install下载依赖（耐心等待）
通过 pnpm dev命令启动前端

### 3.5前后端连接
由于前端的mock使用的是在线数据并没有连接到真正的后端及数据库，
- 更改配置C:\Windows\System32\drivers\etc\hosts的内容，在末尾加上
```bash
127.0.0.1   jeecg-boot-mysql
127.0.0.1   jeecg-boot-redis
127.0.0.1   jeecg-boot-system
```
- 修改前端配置文件 jeecg-boot-vue3/.env.development
```bash
VITE_USE_MOCK = false
VITE_GLOB_DOMAIN_URL=http://jeecg-boot-system:8080/jeecg-boot
VITE_PROXY = [["/jeecgboot","http://jeecg-boot-system:8080/jeecg-boot"],["/upload","http://localhost:3300/upload"]]
```
- 在文件中找到D:\test-project\jeecg-boot\jeecg-boot\jeecg-module-system\jeecg-system-start\src\main\resources\application-dev.yml
修改URL，目的是连接上数据库
```bash
url: jdbc:mysql://jeecg-boot-mysql:3306/jeecg-boot?....
# redis配置：
host: jeecg-boot-redis
```

### 3.6又遇到问题了，修改了配置文件，要再来一遍

- 修改 docker-compose.yml
给 jeecg-boot-system 服务添加 environment 配置，在post后，networks前加
```bash
environment:
      - SPRING_DATASOURCE_DYNAMIC_DATASOURCE_MASTER_URL=jdbc:mysql://jeecg-boot-mysql:3306/jeecg-boot?characterEncoding=UTF-8&useUnicode=true&useSSL=false&tinyInt1isBit=false&allowPublicKeyRetrieval=true&serverTimezone=Asia/Shanghai
      - SPRING_DATASOURCE_DYNAMIC_DATASOURCE_MASTER_USERNAME=root
      - SPRING_DATASOURCE_DYNAMIC_DATASOURCE_MASTER_PASSWORD=root
      - SPRING_DATA_REDIS_HOST=jeecg-boot-redis
```
- Dockerfile 里只复制了 jar 包，没有单独复制配置文件！这意味着：

  - 配置文件是在构建 jar 包时被打包进去的
  - 修改源代码后，需要先用 Maven 重新编译打包，然后再构建 Docker 镜像
- Docker启动后端
  ```powershell
  docker-compose up -d jeecg-boot-mysql jeecg-boot-redis jeecg-boot-system
  ```
- 然后浏览器进入http://localhost:8080/jeecg-boot  直接到达接口文档
- 本地单独启动前端（已安装node以及pnpm）
  ```bash
  cd D:\test-project\jeecg-boot\jeecgboot-vue3
  pnpm dev
  ```
  ## 四、总结
  前后端之间的连接要看配置文件，出现异常可以打印日志docker ps查看具体内容（容器是否启动）。通过这次的部署，对Docker拉取镜像有了更加深入的了解，不仅可以下载资源，还可以进行打包编译。希望此项目能帮助我深入学习测试的工作。
