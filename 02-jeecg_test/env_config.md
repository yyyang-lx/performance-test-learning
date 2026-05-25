# JeecgBoot测试环境配置与认证记录

## 1. 部署环境
- 部署日期：2026-5-23
- JeecgBoot版本：v3.9.2
- 访问地址：http://localhost:8080/jeecg-boot
- 部署方式：Docker Compose 单体模式
- 宿主机：Windows WSL2
- 宿主机配置: 内存 8GB
- 测试工具：Postman
  
## 2. 容器状态
| 容器名称 | 镜像版本 | 端口映射 | 状态 |
|---------|---------|---------|------|
| jeecg-boot-mysql | jeecg-boot-mysql:latest | 3306->3306 | Up |
| jeecg-boot-redis | redis:5.0 | 6379->6379 | Up |
| jeecg-boot-system | jeecg-boot-system:latest | 8080->8080 | Up |
<img width="2150" height="1173" alt="image" src="https://github.com/user-attachments/assets/e385455e-5391-4e53-ae88-bda36d17d6c2" />
<img width="2553" height="1453" alt="image" src="https://github.com/user-attachments/assets/0ccc0e39-1fe4-4172-9d97-eea072b9c267" />
<img width="2559" height="1466" alt="image" src="https://github.com/user-attachments/assets/9ff0f270-cc17-40e4-b0fa-a1eeb763c8f6" />

## 3. 登录认证（测试表单接口前）

### 3.1 成功调用的获取验证码接口
- **URL**：`GET localhost:8080/jeecg-boot/sys/randomImage/test001`
- **响应**：
 ```json
 {
    "success": true,
    "message": "",
    "code": 0,
    "result": "data:image/jpg;base64,/9j/4AAQSkZJRgABAgAAAQABAAD/2wBDAAgGBgcGBQgHBwcJCQgKDBQNDAsLDBkSEw8UHRofHh0aHBwgJC4nICIsIxwcKDcpLDAxNDQ0Hyc5PTgyPC4zNDL/2wBDAQkJCQwLDBgNDRgyIRwhMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjL/wAARCAAjAGkDASIAAhEBAxEB/8QAHwAAAQUBAQEBAQEAAAAAAAAAAAECAwQFBgcICQoL/8QAtRAAAgEDAwIEAwUFBAQAAAF9AQIDAAQRBRIhMUEGE1FhByJxFDKBkaEII0KxwRVS0fAkM2JyggkKFhcYGRolJicoKSo0NTY3ODk6Q0RFRkdISUpTVFVWV1hZWmNkZWZnaGlqc3R1dnd4eXqDhIWGh4iJipKTlJWWl5iZmqKjpKWmp6ipqrKztLW2t7i5usLDxMXGx8jJytLT1NXW19jZ2uHi4+Tl5ufo6erx8vP09fb3+Pn6/8QAHwEAAwEBAQEBA... # 将完整的值输入在线Base64转图片获取验证码
    "timestamp": 1779695439246
}
```
### 3.2 成功调用的登录接口
- **URL**：`POST http://localhost:8080/jeecg-boot/sys/Login`
- **请求Body**：
```json
{
    "username": "admin",
    "password": "123456",
    "loginOrgCode": "",
    "captcha": "真实返回的验证码",
    "checkKey": "test001"
}
```
- **响应**：
```json
{
    "success": true,
    "message": "登录成功",
    "code": 200,
    "result": {
        "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwiY2xpZW50VHlwZSI6IlBDIiwiZXhwIjoxNzc5OTk3ODc3fQ.yyY2nUFkswahbIugmOSYny8On64YWHQn7_QFE7_ySUM",
  ...
 }
}
  ```
### 3.3 将token存入环境变量
- 首先点击Postman右上角的 Environment 下拉框（显示 "No Environment" 的位置）
- 输入环境名称：JeecgBoot Local

- 添加以下变量：

| Variable | Value |
|---------|---------|
| base_url | http://localhost:8080/jeecg-boot	|
| token	|（留空）|

- 在登录接口scripts添加post-res脚本自动获取token
```javascript
// 解析响应JSON
const response = pm.response.json();

// 检查登录是否成功
if (response.code === 200 && response.result && response.result.token) {
    // 将token存入环境变量
    pm.environment.set("token", response.result.token);
    console.log("✅ Token获取成功: " + response.result.token.substring(0, 20) + "...");
} else {
    console.log("❌ 登录失败: " + response.message);
}
```
