# AGENTS.md

本文件是智能体的唯一入口，保持为"地图而不是手册"。

## 项目概述

nidhogg-orchestration 是 Yggdrasil-Labs 生态的基础设施编排项目，为 Valhalla 平台提供统一的 Docker Compose 部署方案。项目不包含应用代码，仅定义服务编排、网络拓扑和多环境配置（dev / test / prod）。编排服务：Nacos v3.0.3（配置中心 + 服务注册）、valhalla-auth（认证）、valhalla-user（用户管理）、valhalla-user-admin（管理后台前端），所有服务通过 Yggdrasil-net bridge 网络通信。

## 全局规范

1. 智能体优先遵循项目规范（`AGENTS.md`、`ARCHITECTURE.md`、`docs/design-docs/`）。项目约束 > 智能体全局约束。
2. Git Conventional Commits，message 中文。格式：`<type>(<scope>): <中文描述>`。
3. 文档与代码冲突时以代码为准并回写文档。

## 导航

### A. 长期约束（只读，修改需架构 RFC）

- 系统边界与依赖方向：[`ARCHITECTURE.md`](./ARCHITECTURE.md)

### B. 流转文档

- 活跃版本：[`docs/active/index.md`](./docs/active/index.md)
- 版本归档：[`docs/archive/index.md`](./docs/archive/index.md)
- 技术债：[`docs/active/tech-debt-tracker.md`](./docs/active/tech-debt-tracker.md)
- 设计决策：[`docs/design-docs/index.md`](./docs/design-docs/index.md)

## 开发命令

```bash
# 启动开发环境
cd docker/dev && ./start.sh
# 等价手动命令
docker-compose -f docker/docker-compose.yml -f docker/dev/docker-compose.override.yml --env-file docker/dev/env up -d

# 启动测试环境
docker-compose -f docker/docker-compose.yml -f docker/test/docker-compose.override.yml --env-file docker/test/env up -d

# 启动生产环境
docker-compose -f docker/docker-compose.yml -f docker/prod/docker-compose.override.yml --env-file docker/prod/env up -d

# 停止服务
docker-compose -f docker/docker-compose.yml -f docker/{env}/docker-compose.override.yml --env-file docker/{env}/env down

# 查看日志
docker-compose -f docker/docker-compose.yml -f docker/{env}/docker-compose.override.yml --env-file docker/{env}/env logs -f [service_name]

# 重建单个服务（拉取最新镜像）
docker-compose -f docker/docker-compose.yml -f docker/{env}/docker-compose.override.yml --env-file docker/{env}/env pull [service_name]
docker-compose -f docker/docker-compose.yml -f docker/{env}/docker-compose.override.yml --env-file docker/{env}/env up -d [service_name]
```
