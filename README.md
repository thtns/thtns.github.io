# thtns's blog

这是 [thtns.cn](https://thtns.cn) 的 Hexo 源码仓库，使用 [NexT](https://github.com/next-theme/hexo-theme-next) 主题。

## 技术栈

- Node.js 24 LTS
- Hexo 8
- NexT 8（Gemini 布局）
- 字数统计、阅读时长、中文混排和 Mermaid 图表
- 不蒜子访问统计与微信/支付宝赞赏
- GitHub Actions + GitHub Pages
- Linux + Nginx（可选服务器部署）

## 本地开发

建议先使用 nvm、fnm 等版本管理工具切换到 `.nvmrc` 中声明的 Node.js 版本。

```bash
npm ci
npm run dev
```

浏览器访问 `http://localhost:4000`。开发命令会显示草稿；正式构建不会发布草稿。

常用命令：

```bash
npm run new -- "文章标题"
npm run new:draft -- "草稿标题"
npm run publish -- "草稿标题"
npm run build
```

文章位于 `source/_posts`，主题覆盖配置位于 `_config.next.yml`，不要直接编辑 `node_modules/hexo-theme-next`。

## GitHub Pages

GitHub Pages 仓库是 `thtns/thtns.github.io`，当前项目的 `origin` 已指向该仓库。Hexo 源码和 GitHub Actions 工作流统一使用 `master` 分支。

仓库准备好后：

1. 将新版源码提交并推送到 `master` 分支。
2. 进入仓库 **Settings → Pages**。
3. 将 **Source** 设置为 **GitHub Actions**。
4. 推送 `master` 分支，`pages.yml` 会自动构建并发布到 `https://thtns.github.io`。

GitHub Pages 不绑定 `thtns.cn`，因此 GitHub Pages 和独立服务器可以同时提供服务。GitHub 构建使用 `_config.github.yml`，规范地址为 `https://thtns.github.io`。

## 服务器部署

推荐目录：

```text
/var/www/thtns-blog/
├── releases/
└── current -> releases/<git-commit>
```

服务器需要安装 Nginx、创建只能写入该目录的部署用户，并配置其 SSH 公钥。将 `deploy/nginx.conf` 安装到 Nginx 后，可使用 Certbot 配置 HTTPS。

在 GitHub 仓库的 `production-server` Environment 中配置：

- `SERVER_HOST`：服务器地址
- `SERVER_PORT`：SSH 端口，例如 `22`
- `SERVER_USER`：部署用户
- `SERVER_SSH_KEY`：部署用户的私钥
- `SERVER_DEPLOY_PATH`：例如 `/var/www/thtns-blog`

然后在 **Actions → Deploy Server → Run workflow** 手动发布。每次发布都会创建一个以 Git 提交 SHA 命名的目录，再原子更新 `current` 软链接。

回滚时，在服务器上把 `current` 重新指向旧版本即可：

```bash
ln -sfn /var/www/thtns-blog/releases/<旧提交SHA> /var/www/thtns-blog/current
```

`thtns.cn` 作为服务器版本的独立域名，服务器构建使用 `_config.server.yml`。当前公网 DNS 已指向服务器；完成 Nginx 配置和服务器工作流 Secrets 后即可发布。

## 配置文件

- `_config.yml`：公共站点配置
- `_config.next.yml`：NexT 主题配置
- `_config.github.yml`：GitHub Pages 构建覆盖项
- `_config.server.yml`：服务器构建覆盖项
- `source/_data/styles.styl`：少量自定义样式

ICP 与公安备案入口位于 `_config.next.yml` 的 `footer.beian`。拿到真实备案号后再启用，未备案时不要填写占位号码。

## 发布检查

Pull Request 会运行 `check.yml` 验证站点能够生成。`public/` 是构建产物，不提交到 Git。
