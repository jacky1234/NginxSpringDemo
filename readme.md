# Nginx + Spring Boot 反向代理 Demo

这是一个使用 Nginx 作为反向代理服务器的 Spring Boot 应用示例。

## 项目结构

```
.
├── backend/           # Spring Boot 后端服务
├── frontend/          # 前端静态文件
├── nginx/            # Nginx 配置文件
│   └── default.conf  # Nginx 主配置文件
└── docker-compose.yml # Docker 编排配置
```

## 快速开始

### 1. 单独运行后端服务

```bash
# 进入后端目录
cd backend

# 运行 Spring Boot 应用
./gradlew bootRun
```

### 2. 单独运行 Nginx

```bash
# 停止并删除现有容器（如果有）
docker stop $(docker ps -q --filter ancestor=nginx:alpine)
docker rm $(docker ps -a -q --filter ancestor=nginx:alpine)

# 运行 Nginx 容器
docker run -d \
  -p 80:80 \
  -v $(pwd)/nginx/default.conf:/etc/nginx/conf.d/default.conf \
  -v $(pwd)/frontend:/var/www/frontend \
  --add-host=host.docker.internal:host-gateway \
  nginx:alpine
```

### 3. 使用 Docker Compose 运行（推荐）

```bash
# 启动所有服务
docker-compose up --build

# 停止服务
docker-compose down
```

## 服务访问

- 后端服务直接访问：http://localhost:8080/api/hello
- 通过 Nginx 代理访问：http://localhost/api/hello
- 前端页面：http://localhost

## 配置说明

### Nginx 配置

- 监听端口：80
- 前端静态文件目录：/var/www/frontend
- API 代理路径：/api/
- 后端服务地址：http://host.docker.internal:8080

### 后端配置

- 服务端口：8080
- API 基础路径：/api
- 示例接口：/api/hello

## 常见问题

1. **404 错误**
   - 检查后端服务是否正在运行
   - 确认 API 路径是否正确
   - 检查 Nginx 配置中的 proxy_pass 设置

2. **连接超时**
   - 检查后端服务是否可访问
   - 确认 host.docker.internal 是否正确配置
   - 检查防火墙设置

3. **静态文件无法访问**
   - 确认前端文件目录是否正确挂载
   - 检查文件权限

## 性能优化

1. **Nginx 优化**
   - 已配置 gzip 压缩
   - 静态资源缓存
   - 连接超时设置

2. **Docker 优化**
   - 使用 Alpine 基础镜像
   - 配置数据卷持久化
   - 服务自动重启

## 开发建议

1. **本地开发**
   - 建议先单独运行后端服务进行调试
   - 使用 Docker 运行 Nginx 进行代理测试
   - 确认功能正常后再使用 docker-compose

2. **生产部署**
   - 配置 HTTPS
   - 设置适当的日志级别
   - 配置监控和告警
   - 考虑使用 CI/CD 流程

## 扩展功能

1. **安全增强**
   - 配置 SSL 证书
   - 添加请求限流
   - 配置 CORS 策略

2. **监控和日志**
   - 配置日志收集
   - 添加性能监控
   - 设置健康检查

3. **高可用**
   - 配置负载均衡
   - 设置服务自动扩缩容
   - 实现故障转移

## 参考文档

- [Nginx 官方文档](http://nginx.org/en/docs/)
- [Spring Boot 文档](https://docs.spring.io/spring-boot/docs/current/reference/html/)
- [Docker 文档](https://docs.docker.com/)
- [Docker Compose 文档](https://docs.docker.com/compose/)

## docker

1. **启动项目**：

   在项目根目录运行：
   ```bash
   docker-compose up --build
   ```

2. **访问服务**：
   - 前端：http://localhost
   - 后端 API：http://localhost/api/

3. **注意事项**：
   - 确保后端应用的 application.properties/application.yml 中配置了正确的端口（8080）
   - 前端项目中的 API 请求地址应该使用相对路径（以 `/api` 开头）
   - 如果需要 HTTPS，需要额外配置 SSL 证书和 Nginx SSL 设置

4. **可选优化**：
   - 可以添加 Docker 健康检查
   - 可以添加数据持久化配置
   - 可以配置日志收集
   - 可以添加监控配置
