# thtns's blog

这是 [thtns.cn](https://thtns.cn) 的 Hexo 源码仓库，使用 [NexT](https://github.com/next-theme/hexo-theme-next) 主题。

## 技术栈

- Node.js 24 LTS
- Hexo 8
- NexT 8（Gemini 布局）
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
4. 推送 `master` 分支，`pages.yml` 会自动构建并发布。
5. 在 Pages 设置中将自定义域名设为 `thtns.cn`，并启用 HTTPS。
6. 按 GitHub Pages 设置页面给出的记录配置域名 DNS。

`source/CNAME` 已包含 `thtns.cn`。启用独立域名后，`thtns.github.io` 通常会跳转到 `thtns.cn`，站内链接也以独立域名作为规范地址。

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

独立域名同一时间只能指向一组实际入口。使用 GitHub Pages 时把 DNS 指向 GitHub；切换到自己的服务器时，把 DNS 改为服务器地址，并在 GitHub Pages 中移除自定义域名以避免冲突。

## 配置文件

- `_config.yml`：公共站点配置
- `_config.next.yml`：NexT 主题配置
- `_config.github.yml`：GitHub Pages 构建覆盖项
- `_config.server.yml`：服务器构建覆盖项
- `source/_data/styles.styl`：少量自定义样式

## 发布检查

Pull Request 会运行 `check.yml` 验证站点能够生成。`public/` 是构建产物，不提交到 Git。
