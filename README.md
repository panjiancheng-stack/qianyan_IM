# qianyan_IM · InfiniteChat

基于 Spring Boot 3 + Spring Cloud 的微服务即时通讯（IM）系统，覆盖账号、联系人、群聊、实时消息、红包、离线消息与 AI 助手等核心能力。

## 功能概览

- **账号**：邮箱验证码 / 密码注册登录，JWT Token 鉴权，头像直传
- **联系人**：用户搜索、好友申请与审批、好友列表、好友分组、申请过期自动处理
- **群聊**：创建群聊、邀请成员、群会话与系统通知
- **实时消息**：Netty WebSocket 长连接、在线状态维护、消息路由与推送
- **红包**：发红包、抢红包、余额流水、超时未领取自动退回（Kafka 异步 + 延迟任务）
- **离线消息**：历史消息分页查询、会话摘要、Canal 增量同步
- **AI 助手**：RAG 知识库问答、工具调用（时间 / 邮件 / 知识检索）、输入护栏、监控指标

## 模块说明

| 模块 | 端口 | 说明 |
| --- | --- | --- |
| `Common` | - | 统一响应、错误码、全局异常、常量枚举、JWT / 雪花 ID 工具、通用 DTO |
| `GateWay` | 10010 | Spring Cloud Gateway 网关，路由转发与 JWT 鉴权过滤器 |
| `UserService` | 8104 | 用户、联系人、群聊、会话、内部校验接口 |
| `RealTimeService` | 8102 / 9101 | Netty WebSocket 接入，消息收发与系统通知推送 |
| `RedPacketService` | 8103 | 红包发送 / 领取、余额、Kafka 异步落库与过期调度 |
| `OfflineDataService` | 8101 | 离线与历史消息查询，Canal 数据同步 |
| `AiService` | 8105 | AI 对话、RAG 检索增强、工具调用与监控 |

## 技术栈

- Java 17、Spring Boot 3.5、Spring Cloud 2025 + Nacos
- Spring Cloud Gateway、OpenFeign、LoadBalancer
- MyBatis-Plus、MySQL、Redis、Kafka、MinIO
- Netty WebSocket、Canal
- Spring AI / LangChain4j、DashScope（通义千问）、PGVector
- Docker / Docker Compose

## 快速开始

1. 准备 MySQL、Redis、Kafka、Nacos、MinIO、PGVector 等中间件，连接信息见各模块 `src/main/resources/application.yml`。
2. 构建全部模块：

   ```bash
   mvn -DskipTests clean package
   ```

3. 使用 Docker Compose 一键构建并启动：

   ```bash
   docker compose -f docker-compose-service.yml up -d --build
   ```

4. 服务启动后统一通过网关 `http://localhost:10010` 访问；AI 服务前端页面位于 `AiService/src/main/resources/front/`。

## 目录结构

```
InfiniteChat
├── Common                 # 公共依赖与工具
├── GateWay                # 网关
├── UserService            # 用户 / 联系人 / 群聊
├── RealTimeService        # WebSocket 实时消息
├── RedPacketService       # 红包
├── OfflineDataService     # 离线与历史消息
├── AiService              # AI 助手
├── docker-compose-service.yml
└── pom.xml
```

## 说明

- 各服务通过 Nacos 注册发现，网关统一鉴权后转发。
- 生产环境配置见各模块 `application-prod.yml`，敏感信息请通过环境变量注入，不要提交到仓库。
