# Nidhogg Orchestration

Nidhogg Orchestration 是 Yggdrasil-Labs 的微服务编排项目，提供基于 Docker Compose 和 Kubernetes 的容器化部署方案。

## 📋 项目简介

本项目为 Yggdrasil-Labs 微服务生态提供统一的编排管理，支持多环境（开发、测试、生产）的快速部署和配置管理。项目采用 Nacos 作为服务注册与配置中心，使用 Dubbo 作为 RPC 框架。

## 🏗️ 项目结构

```
nidhogg-orchestration/
├── docker/                    # Docker Compose 配置
│   ├── docker-compose.yml     # 主配置文件（所有环境共享）
│   ├── dev/                   # 开发环境配置
│   │   ├── docker-compose.override.yml
│   │   ├── env                # 环境变量配置
│   │   └── start.ps1         # 启动脚本
│   ├── test/                  # 测试环境配置
│   │   ├── docker-compose.override.yml
│   │   ├── env
│   │   └── start.ps1
│   ├── prod/                  # 生产环境配置
│   │   ├── docker-compose.override.yml
│   │   ├── env
│   │   └── start.ps1
│   ├── logs/                  # 日志目录
│   │   └── valhalla-auth/    # Valhalla Auth 服务日志
│   └── README.md             # Docker 配置说明文档
└── k8s/                       # Kubernetes 配置（待完善）
```

## 🚀 快速开始

### 前置要求

- Docker Engine 20.10+
- Docker Compose 2.0+
- PowerShell 5.1+ (Windows) 或 Bash (Linux/Mac)

### 启动服务

#### Windows PowerShell

```powershell
# 启动开发环境
cd docker/dev
.\start.ps1

# 启动测试环境
cd docker/test
.\start.ps1

# 启动生产环境
cd docker/prod
.\start.ps1
```

#### Linux/Mac

```bash
# 启动开发环境
cd docker/dev
docker-compose -f ../docker-compose.yml -f docker-compose.override.yml --env-file env up -d

# 启动测试环境
cd docker/test
docker-compose -f ../docker-compose.yml -f docker-compose.override.yml --env-file env up -d

# 启动生产环境
cd docker/prod
docker-compose -f ../docker-compose.yml -f docker-compose.override.yml --env-file env up -d
```

## 🛠️ 服务说明

### Nacos 服务注册与配置中心

- **镜像**: `nacos/nacos-server:latest`
- **端口**: 
  - `8848` - Nacos 主端口（Web 控制台和 API）
  - `9848` - gRPC 端口
  - `9849` - gRPC 服务器间通信端口
- **访问地址**: `http://localhost:8848/nacos`
- **默认账号**: `nacos` / `nacos`
- **模式**: standalone（单机模式）
- **数据持久化**: 使用 Docker volumes (`nacos-data`, `nacos-logs`)

### Valhalla Auth 服务

- **镜像**: `valhalla-auth:latest`
- **依赖**: 依赖 Nacos 服务，启动前会等待 Nacos 健康检查通过
- **Nacos 连接**: 自动配置连接到 `nacos-standalone-derby:8848`
- **健康检查**: `http://localhost:8081/actuator/health`

## 🌍 环境配置

### 开发环境 (dev)

- **Spring Profile**: `dev`
- **重启策略**: `no` (手动重启)
- **HTTP 端口**: `8081`
- **服务端口**: `20880`
- **JVM 参数**: `-Xms64m -Xmx128m` (较小内存)
- **额外功能**: 
  - 挂载日志目录
  - 开放 Java 远程调试端口 `5005`

### 测试环境 (test)

- **Spring Profile**: `test`
- **重启策略**: `unless-stopped`
- **HTTP 端口**: `8082`
- **服务端口**: `20881`
- **JVM 参数**: `-Xms128m -Xmx256m` (中等内存)

### 生产环境 (prod)

- **Spring Profile**: `production`
- **重启策略**: `always` (总是重启)
- **HTTP 端口**: `8081`
- **服务端口**: `20880`
- **JVM 参数**: `-Xms256m -Xmx512m -XX:+UseG1GC` (较大内存，使用 G1GC)
- **资源限制**: CPU 2核，内存 1GB

## 📝 常用命令

### 停止服务

```bash
# 在对应的环境目录下执行
docker-compose -f ../docker-compose.yml -f docker-compose.override.yml down

# 停止并删除卷
docker-compose -f ../docker-compose.yml -f docker-compose.override.yml down -v
```

### 查看日志

```bash
# 查看 Valhalla Auth 服务日志
docker-compose -f ../docker-compose.yml -f docker-compose.override.yml logs -f valhalla-auth

# 查看所有服务日志
docker-compose -f ../docker-compose.yml -f docker-compose.override.yml logs -f
```

### 查看服务状态

```bash
docker-compose -f ../docker-compose.yml -f docker-compose.override.yml ps
```

### 重启服务

```bash
docker-compose -f ../docker-compose.yml -f docker-compose.override.yml restart valhalla-auth
```

## 🔧 网络配置

所有服务使用统一的 Docker 网络：`Yggdrasil-net`

服务间通过容器名称进行通信，例如：
- `nacos-standalone-derby:8848` - Nacos 服务地址
- `valhalla-auth` - Valhalla Auth 服务容器名

## 📚 相关文档

- [Docker 配置详细说明](./docker/README.md)

## 🤝 贡献

欢迎提交 Issue 和 Pull Request。

## 📄 许可证

本项目属于 Yggdrasil-Labs，具体许可证信息请参考项目根目录。

## 🔗 相关链接

- [Nacos 官方文档](https://nacos.io/docs/latest/what-is-nacos/)
- [Dubbo 官方文档](https://dubbo.apache.org/zh/docs/)
- [Docker Compose 文档](https://docs.docker.com/compose/)

