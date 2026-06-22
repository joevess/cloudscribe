# CloudScribe

CloudScribe 是一个部署在 Docker 中的云盘订阅与转存工具，用来把阿里云盘、夸克网盘分享资源持续同步到自己的云盘目录，并按需推送到 Aria2 下载。

它适合运行在 Docker、NAS、家庭服务器或 VPS 上。你可以接入多个云盘账号，管理目录与分享，创建订阅任务，配置季集识别、重命名和同集更新规则，让追更、转存、下载和记录归档集中在一个 Web 界面里完成。

## 功能特性

### 云盘账号与文件管理

- 支持阿里云盘、夸克网盘账号接入。
- 支持扫码接入和凭据接入。
- 显示账号可用状态、认证状态、容量占用和最近更新时间。
- 支持启用、停用、刷新和移除账号。
- 支持设置每个账号的默认保存目录。
- 支持浏览云盘目录、返回上级、刷新、搜索当前目录和新建文件夹。
- 支持单个文件删除、批量删除，并对夸克删除后的同步延迟给出状态提示。
- 支持从云盘文件直接创建分享，支持公开分享、提取码分享和有效期设置。
- 支持分享记录管理、复制分享信息、单个取消和批量取消分享。

### 分享转存与订阅

- 支持阿里云盘、夸克网盘分享链接预览和资源选择。
- 支持在文件管理中把分享资源快速转存到当前目录。
- 支持订阅分享链接，配置提取码、保存账号、保存目录和目标子目录。
- 支持手动执行单个订阅、立即执行全部已启用订阅。
- 支持定时检查已启用订阅，可按间隔、每天或每周设置，默认关闭。
- 支持启用、停用、编辑、删除订阅。
- 支持重置订阅历史，重新识别已保存内容。
- 支持订阅执行前预览保存效果。
- 支持在预览中直接调整文件筛选、季集范围、未识别季处理、重命名、同集更新和 Aria2 设置。
- 支持按包含关键词、排除关键词过滤资源。
- 支持按季集范围保存：全部保存、从指定季集开始、从指定集数开始、只保存指定季。
- 支持未识别季的自动推断、指定季数或不补充处理。

### 规则管理

- 支持季集识别规则，用于从文件名中识别季数和集数。
- 支持重命名规则，用模板生成转存后的文件名。
- 支持同集更新规则，当同一集出现多个版本时按优先级选择更合适的文件。
- 同集更新规则支持按文件名关键词、文件大小范围、扩展名匹配。
- 支持新增、编辑、删除自定义规则。
- 支持启用、停用和排序同集更新规则。
- 内置多套常用命名规则，如剧名季集、中文集数、季集编号前置等。
- 内置规则不可误删，适合不熟悉正则或模板时直接使用默认配置。

### Aria2 下载任务

- 支持配置 Aria2 RPC 地址、密钥、默认下载目录和启用状态，并可测试连接。
- 支持手动提交 URL 到 Aria2，支持多行或逗号分隔。
- 支持从云盘目录选择文件推送下载。
- 文件夹下载会自动展开并保留文件夹层级。
- 订阅转存后可按任务配置自动推送到 Aria2。
- 支持查看 Aria2 下载概览和任务列表。
- 支持下载队列分批推送，减少一次性提交过多任务。
- 支持按状态和关键词筛选下载任务。
- 支持分页、单个删除、批量删除、批量暂停和批量继续。

### 记录、日志与应用管理

- 执行记录按时间倒序展示，支持来源、执行方式、结果和关键词筛选。
- 支持删除单条执行记录和清空执行记录。
- 系统日志支持分页、级别筛选、分类筛选、刷新、删除单条和清空。
- 应用管理页显示当前版本、构建号、发布日期和运行状态。
- 支持应用内版本更新，默认读取 GitHub 更新清单，展示版本说明并可一键更新容器。
- 更新期间进入维护状态，等待服务恢复后自动刷新。
- 支持修改 Web 登录用户名和密码。
- `/health` 接口返回健康状态、版本、构建号和适配器版本。

## Docker 运行

```bash
docker run -d \
  --name cloudscribe \
  --restart unless-stopped \
  -p 8008:8008 \
  -v ./data:/data \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -e APP_SECRET_KEY=change-this-secret \
  -e APP_DATA_DIR=/data \
  -e APP_UPDATE_CHECK_URL=https://raw.githubusercontent.com/joevess/cloudscribe/main/update.json \
  -e APP_SELF_UPDATE_ENABLED=true \
  -e APP_SELF_UPDATE_CONTAINER_NAME=cloudscribe \
  -e APP_SELF_UPDATE_WATCHTOWER_IMAGE=containrrr/watchtower:latest \
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
      APP_SELF_UPDATE_ENABLED: "true"
      APP_SELF_UPDATE_CONTAINER_NAME: cloudscribe
      APP_SELF_UPDATE_WATCHTOWER_IMAGE: containrrr/watchtower:latest
      TZ: Asia/Shanghai
    volumes:
      - ./data:/data
      - /var/run/docker.sock:/var/run/docker.sock
    restart: unless-stopped
```

启动：

```bash
docker compose up -d
```

## 数据目录

应用运行数据统一保存到容器内 `/data`，包括：

- SQLite 数据库
- 云盘账号配置和认证信息
- 订阅配置和订阅历史
- 规则配置
- 分享记录
- 下载推送记录
- 执行记录
- 系统日志
- 应用账号配置

请务必挂载持久化目录：

```text
./data:/data
```

发布镜像不包含本地测试数据、云盘账号、订阅记录、日志或数据库文件。

## 更新方式

```bash
docker compose pull
docker compose up -d
```

应用内版本更新默认读取本仓库的 `update.json`。

## 应用内更新

应用管理页的“版本更新”会自动检查新版本，展示发布日期、镜像和更新说明。发现新版本后点击“立即更新”，CloudScribe 会启动一次性 Watchtower 任务，拉取 `joevess9/cloudscribe:latest` 并重建当前容器。

默认 Compose 配置已经包含应用内更新所需的环境变量和 Docker Socket 挂载：

```yaml
environment:
  APP_SELF_UPDATE_ENABLED: "true"
  APP_SELF_UPDATE_CONTAINER_NAME: cloudscribe
  APP_SELF_UPDATE_WATCHTOWER_IMAGE: containrrr/watchtower:latest
volumes:
  - ./data:/data
  - /var/run/docker.sock:/var/run/docker.sock
```

更新过程中页面会进入全屏更新状态，避免继续操作其它功能；服务恢复后页面会自动刷新。

建议正式部署使用 `latest` 标签。如果固定使用 `0.9.x` 版本标签，Watchtower 只会检查同一个标签，无法切换到新版本号。

`/var/run/docker.sock` 代表宿主机 Docker 管理能力，请只在可信的自用环境中部署。

健康检查：

```bash
wget -qO- http://127.0.0.1:8008/health
```

## 链接

- Docker Hub: https://hub.docker.com/r/joevess9/cloudscribe
- GitHub: https://github.com/joevess/cloudscribe
- 镜像: `joevess9/cloudscribe:latest`

当前版本：

- `0.9.94`
- 更新说明：修复老版本升级后启动失败问题。
- Digest: `sha256:2c14176c026cff0b6f74feac7d80131ec040aa1010eff8ae51b74ee8d5c345d4`

## 注意事项

云盘 Web 接口不是稳定的公开契约。登录、转存、分享、直链获取等能力可能受到账号状态、Cookie 或 token 失效、平台策略调整、风控和频率限制影响。

建议使用较低频率执行订阅检查，避免短时间大量请求。
