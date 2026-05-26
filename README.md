# AI Job Hunting Server 5.26 — Windows 本地部
## 系统要求
- Windows 10/11 (x64)
- 无需安装任何数据库（内置 H2 本地数据库）

## 目录结构
```
ai-job-windows/
├── ai-job.jar              # SpringBoot 应用
├── start.bat               # 启动脚本（带控制台）
├── start-silent.vbs        # 无窗口启动
├── stop.bat                # 停止脚本（同时停主服务和H2 Console）
├── start-h2-console.bat    # H2 数据库控制台（独立端口9200）
├── config/
│   ├── application.properties   # 应用配置（数据源、端口等）
│   └── schema-h2.sql           # H2 建表脚本（首次启动自动执行）
├── lib/
│   └── h2-2.2.224.jar      # H2 数据库驱动
├── runtime/                 # JDK 17 运行时
├── data/                    # H2 数据库文件（自动创建）
│   └── ai_job.mv.db
└── logs/                    # 日志目录（自动创建）
    └── ai-job.log
```

## 快速启动

### 方式一：带控制台窗口
双击 `start.bat` 或在命令行中运行：
```
start.bat
```

### 方式二：无窗口后台运行
双击 `start-silent.vbs`

## 停止服务
双击 `stop.bat` 或在控制台窗口按 `Ctrl+C`

## 访问地址
| 服务 | URL |
|------|-----|
| 主服务 | http://localhost:9100 |
| 配置后台 | http://localhost:6768 |
| H2 数据库控制台 | http://localhost:9200 |

## H2 数据库控制台

双击 `start-h2-console.bat` 启动（独立进程，端口 9200，不与主服务冲突）：
- 访问: http://localhost:9200
- JDBC URL: `jdbc:h2:file:./ai_job;MODE=MySQL;AUTO_SERVER=TRUE;IFEXISTS=FALSE`
- 用户名: `sa`
- 密码: （空）

注意：H2 Console 需在主服务启动后才能连接数据库。`stop.bat` 会同时停止主服务和 H2 Console。

## 数据存储

所有数据存储在 `data/ai_job.mv.db` 文件中：
- 应用重启后数据不丢失
- 如需重置数据库，停止服务后删除 `data/` 目录即可
- 数据库文件不可直接用文本编辑器打开，请通过 H2 控制台管理

## 配置说明

编辑 `config/application.properties` 可修改：
- 端口号：`server.port=9100`
- 内存限制：start.bat 中 `-Xms256m -Xmx512m`
- AI 配置：`openai.pool.config.list`
- 支付宝配置：`alipay.*`

## 注意事项

1. **无需安装 MySQL** — 本版本使用 H2 嵌入式数据库，数据文件本地存储
2. **端口冲突** — 如果 9100 端口被占用，修改 `config/application.properties` 中的 `server.port`
3. **Kimi AI 报错** — `Invalid Authentication` 属于预期错误（API Key 为占位符），不影响核心功能
4. **数据迁移** — 如需从远程 MySQL 迁移数据，请通过 H2 控制台的 CSV 导入功能
