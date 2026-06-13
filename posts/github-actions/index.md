# GitHub Actions CI/CD实战


## Workflow基础
- `.github/workflows` 目录，存放 `.yml` 格式的 workflow 文件
- 核心组件：
  - **name**：workflow 名称
  - **on**：触发事件
  - **jobs**：要执行的任务
  - **steps**：每个 job 的执行步骤
  - **runs-on**：运行环境（ubuntu-latest、windows-latest 等）

## Trigger事件详解
- `push` / `pull_request`：代码推送或 PR 时触发
- `schedule`：定时触发（cron 表达式）
- `workflow_dispatch`：手动触发
- `release`：发布 Release 时触发
- 路径过滤：`paths` / `paths-ignore` 控制特定文件变更才触发

## 常用Action推荐
- `actions/checkout`：拉取代码
- `actions/setup-python` / `actions/setup-node`：配置语言环境
- `actions/cache`：缓存依赖加速构建
- `docker/login-action` / `docker/build-push-action`：Docker 镜像构建推送

## 环境变量与Secrets
- 内置环境变量：`GITHUB_SHA`、`GITHUB_REF`、`GITHUB_REPOSITORY` 等
- Secrets 管理：Settings &gt; Secrets and variables &gt; Actions
- 引用方式：`${{ secrets.MY_SECRET }}`
- 推荐使用 Personal Access Token (PAT) 进行跨仓库操作

## 多Job编排
- `needs`：定义 job 依赖关系
- `matrix` 策略：多版本并行测试
- `upload-artifact` / `download-artifact`：制品跨 job 传递

## GitHub Pages 部署实战

### 方案一：部署到同一仓库 Pages 分支
适用于项目站点（Project Pages），直接部署到 `gh-pages` 分支。

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
    steps:
      - uses: actions/checkout@v4
      - uses: actions/configure-pages@v5
      - uses: actions/upload-pages-artifact@v3
        with:
          path: &#39;.&#39;
      - uses: actions/deploy-pages@v4
```

### 方案二：部署到独立 Pages 仓库（跨仓库推送）
适用于用户/组织站点（User/Organization Pages），将静态文件推送到 `username.github.io` 仓库。

```yaml
name: Deploy Hugo Site to Pages Repository

on:
  push:
    branches: [main]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: &#39;0.139.2&#39;
          extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy to external repository
        uses: peaceiris/actions-gh-pages@v4
        with:
          personal_token: ${{ secrets.DEPLOY_TOKEN }}
          external_repository: username/username.github.io
          publish_branch: main
          publish_dir: ./public
```

## Custom Domain 配置

### 方式一：在 Pages 仓库设置中配置
1. 进入 Pages 仓库 Settings &gt; Pages
2. 在 **Custom domain** 输入自定义域名（如 `blog.example.com`）
3. 点击 Save，GitHub 会自动创建 CNAME 记录并校验

### 方式二：通过 CNAME 文件
在静态文件根目录添加 `CNAME` 文件，内容为自定义域名：

```
blog.example.com
```

Hugo 项目中可将 `CNAME` 放在 `static/` 目录下，构建时会自动复制到发布目录。

### 方式三：在 Workflow 中配置
使用 `peaceiris/actions-gh-pages` 时添加 `cname` 参数：

```yaml
- uses: peaceiris/actions-gh-pages@v4
  with:
    personal_token: ${{ secrets.DEPLOY_TOKEN }}
    external_repository: username/username.github.io
    publish_branch: main
    publish_dir: ./public
    cname: blog.example.com
```

### DNS 配置
- **A 记录**：指向 GitHub Pages IP 地址集
  ```
  185.199.108.153
  185.199.109.153
  185.199.110.153
  185.199.111.153
  ```
- **CNAME 记录**：将子域名别名指向 `username.github.io`
- 配置后等待 DNS 生效（通常几分钟到几小时）
- 在仓库 Settings &gt; Pages 中勾选 **Enforce HTTPS**

### 本博客部署架构
```
my-blog（笔记仓库，main 分支）
    ↓ GitHub Actions（build &#43; deploy）
yyt6801.github.io（Pages 仓库，main 分支）
    ↓ DNS CNAME
blog.yyt6801.top（自定义域名）
```

Workflow 使用 `DEPLOY_TOKEN`（PAT）实现跨仓库推送，`actions-hugo` 构建静态文件，`actions-gh-pages` 推送到 Pages 仓库，DNS 通过 CNAME 将 `blog.yyt6801.top` 指向 `yyt6801.github.io`。


---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: https://blog.yyt6801.top/posts/github-actions/  

