# 测试环境配置

## 部署信息
- 部署日期：2026-5-23
- JeecgBoot版本：v3.9.2
- 部署方式：Docker Compose 单体模式
- 宿主机：Windows WSL2
- 宿主机配置: 内存 8GB

## 容器状态
| 容器名称 | 镜像版本 | 端口映射 | 状态 |
|---------|---------|---------|------|
| jeecg-boot-mysql | jeecg-boot-mysql:latest | 3306->3306 | Up |
| jeecg-boot-redis | redis:5.0 | 6379->6379 | Up |
| jeecg-boot-system | jeecg-boot-system:latest | 8080->8080 | Up |
<img width="2150" height="1173" alt="image" src="https://github.com/user-attachments/assets/e385455e-5391-4e53-ae88-bda36d17d6c2" />
<img width="2553" height="1453" alt="image" src="https://github.com/user-attachments/assets/0ccc0e39-1fe4-4172-9d97-eea072b9c267" />
<img width="2559" height="1466" alt="image" src="https://github.com/user-attachments/assets/9ff0f270-cc17-40e4-b0fa-a1eeb763c8f6" />

## 登录验证
- 登录接口：POST /jeecg-boot/sys/login
- 账号/密码：admin / 123456
- 首次登录返回Token示例：eyJhbGciOiJIUzI1NiIs
- 验证方式：用Token访问 /jeecg-boot/sys/user/getUserInfo 成功
