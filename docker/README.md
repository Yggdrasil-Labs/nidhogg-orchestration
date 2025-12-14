# Docker Compose 环境配置说明

## 目录结构

```
docker/
├── docker-compose.yml          # 主配置文件（所有环境共享）
├── dev/                        # 开发环境配置
│   ├── docker-compose.override.yml
│   ├── env
│   └── start.ps1
├── test/                       # 测试环境配置
│   ├── docker-compose.override.yml
│   ├── env
│   └── start.ps1
└── prod/                       # 生产环境配置
    ├── docker-compose.override.yml
    ├── env
    └── start.ps1
```

## 服务说明

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
- **认证**: 已启用（可通过环境变量配置）

### Valhalla Auth 服务
- **镜像**: `valhalla-auth:latest`
- **依赖**: 依赖 Nacos 服务，启动前会等待 Nacos 健康检查通过
- **Nacos 连接**: 自动配置连接到 `nacos:8848`

## 网络配置
所有服务使用统一的网络：`Yggdrasil-net`

## 环境启动方式

### 方式一：使用启动脚本（推荐）

#### Windows PowerShell
```powershell
# 启动开发环境
cd dev
.\start.ps1

# 启动测试环境
cd test
.\start.ps1

# 启动生产环境
cd prod
.\start.ps1
```

#### Linux/Mac
```bash
# 启动开发环境
cd dev
docker-compose -f ../docker-compose.yml -f docker-compose.override.yml --env-file env up -d

# 启动测试环境
cd test
docker-compose -f ../docker-compose.yml -f docker-compose.override.yml --env-file env up -d

# 启动生产环境
cd prod
docker-compose -f ../docker-compose.yml -f docker-compose.override.yml --env-file env up -d
```

### 方式二：直接使用 docker-compose 命令

```bash
# 开发环境（在 dev 目录下）
docker-compose -f ../docker-compose.yml -f docker-compose.override.yml --env-file env up -d

# 测试环境（在 test 目录下）
docker-compose -f ../docker-compose.yml -f docker-compose.override.yml --env-file env up -d

# 生产环境（在 prod 目录下）
docker-compose -f ../docker-compose.yml -f docker-compose.override.yml --env-file env up -d
```

## 环境配置差异

### 开发环境 (dev)
- Spring Profile: `dev`
- 重启策略: `no` (手动重启)
- HTTP 端口: `8081`
- 服务端口: `20880`
- JVM 参数: `-Xms64m -Xmx128m` (较小内存)
- 额外功能: 挂载日志目录，开放 Java 远程调试端口 `5005`

### 测试环境 (test)
- Spring Profile: `test`
- 重启策略: `unless-stopped`
- HTTP 端口: `8082`
- 服务端口: `20881`
- JVM 参数: `-Xms128m -Xmx256m` (中等内存)

### 生产环境 (prod)
- Spring Profile: `production`
- 重启策略: `always` (总是重启)
- HTTP 端口: `8081`
- 服务端口: `20880`
- JVM 参数: `-Xms256m -Xmx512m -XX:+UseG1GC` (较大内存，使用 G1GC)
- 资源限制: CPU 2核，内存 1GB

## 停止服务

```bash
# 在对应的环境目录下执行
# 停止当前环境
docker-compose -f ../docker-compose.yml -f docker-compose.override.yml down

# 停止并删除卷
docker-compose -f ../docker-compose.yml -f docker-compose.override.yml down -v
```

## 查看日志

```bash
# 在对应的环境目录下执行
docker-compose -f ../docker-compose.yml -f docker-compose.override.yml logs -f valhalla-auth
```

