# performance-test-learning
JMeter 性能测试学习笔记与脚本，包含 Docker+Nginx 测试环境搭建过程
# 性能测试学习记录

# 📌 项目简介
自学 JMeter 性能测试，搭建 Docker + Nginx 测试环境，记录完整的踩坑与学习过程。

# 🛠️ 技术栈
- JMeter 5.6.3
- Docker Desktop (WSL 2 后端)
- Nginx
- Windows 11

## 🖥️ 测试环境详情

| 配置项 | 参数 |
| :--- | :--- |
| CPU | Intel/AMD 12 核 (Docker 分配) |
| 内存 | 7.47 GB (Docker 分配) |
| 网络 | 本地回环 localhost:8888 |
| Nginx 版本 | latest (Docker 镜像) |

## ⚙️ 测试配置

| 配置项 | 参数 |
| :--- | :--- |
| 线程数（并发用户） | 10 |
| Ramp-Up 时间 | 1 秒 |
| 循环次数 | 1 |
| 总请求数 | 10 |
| 协议 | HTTP |
| 请求路径 | / |

### 结果解读

| 结论 | 说明 |
| :--- | :--- |
| ✅ 错误率为 0 | 服务稳定，所有请求成功 |
| ✅ 平均响应时间 7ms | 本地回环网络，响应很快 |
| ⚠️ 最大响应时间 22ms | 偶发波动，在可接受范围内 |
| ✅ 吞吐量 11 req/s | 低并发下表现正常 |

## 📈 详细报告

👉 [查看 HTML 可视化报告](./index.html)

### 踩坑记录

## 2026-05-15：Docker 镜像拉取失败

### 问题现象
运行 `docker pull nginx` 报错：
- `no such host`
- `403 Forbidden`

### 尝试过的方案（失败）
- 更换多个镜像源（阿里云、百度云、中科大等）
- 修改 Docker 配置文件
- 配置 DNS

### 最终解决方案
- 1.重新设置镜像源，去掉百度云镜像源，最终使用的镜像源：
"registry-mirrors": [
    "https://docker.m.daocloud.io",
    "https://docker.mirrors.ustc.edu.cn",
    "https://hub-mirror.c.163.com"
  ]
- 2. 重启 Docker Desktop
- 3. 重新运行 `docker pull nginx` 成功
     
### 输出测试报告网页版
- 1.命令行进入对应脚本目录
- 2.输入命令上传成功
- <img width="1562" height="512" alt="image" src="https://github.com/user-attachments/assets/a47fd72f-0bbb-47d4-a2ee-dfc9ea590631" />
