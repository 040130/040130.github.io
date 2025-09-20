# 部署Jekyll Gitbook到GitHub Pages的步骤

## 1. 创建GitHub仓库

1. 登录到您的GitHub账户
2. 点击右上角的"+"号，选择"New repository"
3. 为仓库命名（例如：jekyll-gitbook）
4. 选择设为公开（Public）或私有（Private）
5. 不要初始化README、.gitignore或license
6. 点击"Create repository"

## 2. 配置本地Git仓库并推送代码

在项目目录中打开终端或命令提示符，执行以下命令：

```bash
# 初始化Git仓库（如果尚未初始化）
git init

# 添加所有文件到暂存区
git add .

# 提交更改
git commit -m "Initial commit"

# 添加远程仓库地址（替换为您的仓库URL）
git remote add origin https://github.com/your-username/your-repo-name.git

# 推送到GitHub（设置上游分支）
git push -u origin main
```

## 3. 配置GitHub Pages

1. 在GitHub上访问您的仓库
2. 点击"Settings"选项卡
3. 向下滚动到"Pages"部分
4. 在"Source"下拉菜单中选择"GitHub Actions"
5. 保存设置

## 4. 配置Jekyll构建和部署

由于这是一个Jekyll项目，我们需要配置GitHub Actions来自动构建和部署网站。

创建文件 `.github/workflows/jekyll.yml` 并添加以下内容：

```yaml
name: Build and deploy Jekyll site to GitHub Pages

on:
  push:
    branches: [ main ]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Setup Ruby
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: '3.1'
          bundler-cache: true
          cache-version: 0
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Build with Jekyll
        run: |
          bundle exec jekyll build --baseurl "${{ steps.pages.outputs.base_path }}"
        env:
          JEKYLL_ENV: production
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

## 5. 等待部署完成

1. 提交并推送这个工作流文件到GitHub
2. 在仓库的"Actions"选项卡中查看构建进度
3. 构建完成后，您的网站将在 `https://your-username.github.io/your-repo-name/` 上可用

## 注意事项

- 确保您的 `_config.yml` 文件中的 `baseurl` 设置正确（应该与仓库名称匹配）
- 如果您希望使用自定义域名，请在仓库根目录添加 `CNAME` 文件
- 您可能需要在GitHub Pages设置中选择正确的分支（通常是gh-pages分支）