我来帮你编写一个标准的项目文档。

# CI/CD 项目技术文档

## 1. 项目概述

### 1.1 系统架构
本项目采用现代 DevOps/GitOps 架构，实现自动化部署流程。

### 1.2 技术栈
- **构建工具**: Jenkins Pipeline
- **容器技术**: Docker
- **制品仓库**: Harbor
- **版本控制**: Git
- **通知系统**: 飞书 Webhook
- **部署方式**: SSH Remote Deploy

## 2. 部署架构

### 2.1 部署流程
```mermaid
graph LR
    A[代码提交] --> B[Jenkins触发]
    B --> C[预检查]
    C --> D[Docker构建]
    D --> E[Harbor推送]
    E --> F[远程部署]
    F --> G[健康检查]
    G --> H[通知反馈]
```

### 2.2 环境配置
| 环境变量 | 说明 | 配置方式 |
|---------|------|---------|
| HARBOR_CRED | Harbor登录凭证 | Jenkins Credentials |
| SSH_CRED | SSH密钥 | Jenkins Credentials |
| WEBHOOK_URL | 飞书通知地址 | Jenkins Credentials |
| harbor_host | Harbor仓库地址 | Jenkins Credentials |
| hosts | 部署目标服务器 | Jenkins Credentials |

## 3. 配置说明

### 3.1 Jenkins 凭证配置
需要在 Jenkins 中配置以下凭证：
1. `harbor-credentials`: Harbor 登录凭证
2. `ssh-credentials`: SSH 密钥
3. `feishu-webhook`: 飞书 Webhook URL
4. `harbor-host`: Harbor 仓库地址
5. `deploy-hosts`: 部署服务器地址

### 3.2 容器资源配置
```yaml
资源限制:
  内存: 4GB
  CPU: 1024 shares
端口映射:
  - 8801:8700
  - 8901:8800
挂载目录:
  - /data/logs/${service_name}:/data/logs/${service_name}
```

## 4. 部署流程

### 4.1 预检查阶段
- 检查必要参数完整性
- 验证环境变量配置

### 4.2 部署阶段
1. Harbor 登录认证
2. 拉取目标镜像
3. 优雅停止旧容器
4. 启动新容器
5. 健康检查验证

### 4.3 回滚机制
- 触发条件：部署失败或健康检查失败
- 回滚操作：重启备份容器
- 错误通知：发送失败告警

## 5. 监控告警

### 5.1 健康检查
- 检查端点: `/health`
- 超时时间: 5秒
- 重试机制: 循环检测

### 5.2 通知机制
飞书通知卡片包含：
- 部署状态
- 提交者信息
- 构建编号
- 提交信息
- 部署版本
- 详情链接

## 6. 安全规范

### 6.1 凭证管理
- 使用 Jenkins Credentials 管理敏感信息
- SSH 密钥认证
- Harbor 安全登录

### 6.2 容器安全
- 资源限制
- 自动重启策略
- 数据持久化

## 7. 运维指南

### 7.1 日常运维
```bash
# 查看容器状态
docker ps -a | grep ${service_name}

# 查看容器日志
docker logs -f ${service_name}

# 手动触发健康检查
curl -f http://${host}:${port}/health
```

### 7.2 故障处理
1. 部署失败
   - 检查 Harbor 连接
   - 验证镜像存在
   - 检查目标服务器状态

2. 健康检查失败
   - 检查应用日志
   - 验证端口可用性
   - 检查资源使用情况

## 8. 变更管理

### 8.1 版本规范
版本号格式：`yyyyMMddHHmmss_BuildNumber`
示例：`20240115143000_123`

### 8.2 变更流程
1. 代码提交
2. Jenkins 自动触发
3. 构建部署
4. 验证确认
5. 通知相关方

## 9. 项目维护

### 9.1 定期维护
- 日志清理
- 镜像清理
- 凭证更新

### 9.2 升级建议
- Pipeline 脚本版本控制
- 定期更新基础镜像
- 安全补丁及时应用

## 10. 联系支持

### 10.1 技术支持
- 运维团队
- 开发团队
- 安全团队

### 10.2 文档维护
- 更新日志
- 变更记录
- 问题追踪

此文档应根据项目发展持续更新，确保与实际情况保持一致。