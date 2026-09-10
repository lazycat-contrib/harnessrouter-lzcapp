# HarnessRouter for LazyCat

HarnessRouter Community Edition is a self-hosted unified interface for agent harnesses, implementing the Unified Harness Protocol (UHP). It provides sessions, streaming, files, cancellation and failure handling.

主页：https://github.com/HarnessRouter/harnessrouter

## 使用

要求懒猫微服 1.5.0 或更新版本，目标架构 amd64。安装向导设置初始用户名和密码，密码默认随机生成。Profile 中修改后保存的凭据优先于安装参数；重新设置安装参数不会覆盖已保存的账号密码。

首次启动需联网下载所启用的 agent CLI，建议预留至少 4 GB 磁盘空间，等待日志出现 `ready on :3000`。进入 Integrations 添加模型服务商密钥后才能运行任务；本包不附带模型或试用额度。

控制台和 API 使用 HarnessRouter 自身的登录鉴权，不关闭认证。保留手动登录，不注入文件选择器。原 Compose 的 `.env` 由明确的部署参数和应用内 Integrations 配置替代，不复制示例中的占位 API Key。

`/lzcapp/var/data` 映射 `/data`，保存数据库、文件、密钥存储、已安装的 CLI 和会话工作区。`HR_SECRET_KEY` 由懒猫稳定生成，用于数据库连接凭据加密；迁移到其他微服时必须同时保留原密钥与数据。

镜像必须以 root 启动以建立每个会话的用户，随后由上游入口自行降权；不设置 `user`/`run_as`，不授予额外特权或挂载 Docker socket。仅代理外部端口 3000，内部 Gateway/Runner 不额外暴露。

## 构建与发布

```sh
mkdir -p dist
lzc-cli project release -o dist/application.lpk
lzc-cli lpk info dist/application.lpk
```

仅发布喵喵商店，官方商店关闭。使用 `docker.1ms.run` 镜像模式，每日检查稳定版并验证 amd64 摘要与上游一致。初始版本为 0.15.10。

工作流使用组织级 `APPSTORE_URL`、`APPSTORE_TOKEN` 与可选的 `PRIVATE_STORE_GROUP_CODES`。喵喵商店引用 GitHub Release 文件 `community.lazycat.app.harnessrouter-v<version>.lpk` 及其 SHA256。

图标使用用户最后提供的图片转换为 512×512 PNG。构建与发布验证不替代微服上的首次初始化、登录及真实 agent 任务测试。
