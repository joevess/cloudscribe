# CloudScribe

CloudScribe 是一个面向云盘订阅、分享转存和 Aria2 推送下载的 Docker 应用。

它适合部署在 NAS、Unraid、家庭服务器或 VPS 上，用于统一管理阿里云盘、夸克网盘账号，订阅分享资源，将更新内容转存到自己的云盘目录，并按需推送到 Aria2 下载。

## 功能特性

- 支持阿里云盘、夸克网盘账号接入
- 支持扫码接入和凭据接入
- 支持云盘账号状态、容量、默认目录和文件管理
- 文件管理支持浏览目录、新建文件夹、删除、分享、快速转存
- 支持从云盘目录手动选择文件并推送到 Aria2
- 支持分享链接订阅、提取码、转存目录和下载推送配置
- 支持手动执行订阅和定时检查已启用订阅
- 执行记录支持来源、执行方式、结果和关键词筛选
- 下载任务支持 Aria2 设置、手动 URL 下载、推送记录和批量清理
- 系统日志支持分页、筛选和清理
- 应用账号支持修改用户名和密码
- 支持应用内检查更新
- 所有运行数据统一保存到 `/data`

## Docker 运行

```bash
docker run -d \
  --name cloudscribe \
  --restart unless-stopped \
  -p 8008:8008 \
  -v ./data:/data \
  -e APP_SECRET_KEY=change-this-secret \
  -e APP_DATA_DIR=/data \
  -e APP_UPDATE_CHECK_URL=https://raw.githubusercontent.com/joevess/cloudscribe/main/update.json \
  -e TZ=Asia/Shanghai \
  joevess9/cloudscribe:latest
```

启动后访问：

```text
http://服务器IP:8008
```

默认登录信息：

- 账号：`admin`
- 密码：`password`

首次登录后建议立即在“应用管理”中修改密码。

## Docker Compose

```yaml
services:
  cloudscribe:
    image: joevess9/cloudscribe:latest
    container_name: cloudscribe
    ports:
      - "8008:8008"
    environment:
      APP_SECRET_KEY: change-this-secret
      APP_DATA_DIR: /data
      APP_UPDATE_CHECK_URL: https://raw.githubusercontent.com/joevess/cloudscribe/main/update.json
      TZ: Asia/Shanghai
    volumes:
      - ./data:/data
    restart: unless-stopped
```

启动：

```bash
docker compose up -d
```

## 数据目录

应用运行数据统一保存到容器内 `/data`，包括：

- SQLite 数据库
- 云盘账号配置
- 订阅配置
- 执行记录
- 下载推送记录
- 系统日志

请务必挂载持久化目录：

```text
./data:/data
```

发布镜像不包含本地测试数据、云盘账号、订阅记录、日志或数据库文件。

## 更新

```bash
docker compose pull
docker compose up -d
```

应用内“检查更新”默认读取本仓库的 `update.json`。

## 链接

- Docker Hub: https://hub.docker.com/r/joevess9/cloudscribe
- GitHub: https://github.com/joevess/cloudscribe
- 镜像: `joevess9/cloudscribe:latest`

当前版本：

- `0.9.86`
- Digest: `sha256:b45ec687d0f0c96782df4b8debae93fc4e4852f8eedd024bc92c246eb4216e2c`

## 注意事项

云盘 Web 接口不是稳定的公开契约。登录、转存、分享、直链获取等能力可能受到账号状态、Cookie 或 token 失效、平台策略调整、风控和频率限制影响。

建议使用较低频率执行订阅检查，避免短时间大量请求。
