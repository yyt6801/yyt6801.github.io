# GitHub Actions CI/CD实战


## Workflow基础
- .github/workflows 目录结构
- 核心组件：name、on、jobs、steps、runs-on

## Trigger事件详解
- push、pull_request、schedule、workflow_dispatch
- 路径过滤（paths、paths-ignore）

## 常用Action推荐
- actions/checkout、actions/setup-python、actions/cache
- 第三方Action：docker/login-action、docker/build-push-action

## 环境变量与Secrets
- 内置环境变量速查
- 敏感信息管理（Settings &gt; Secrets and variables）

## 多Job编排
- job依赖（needs）
- 矩阵构建（matrix strategy）
- 制品传递（upload-artifact / download-artifact）

## 博客部署实战
- Hugo构建 &#43; GitHub Pages部署（参考现有deploy.yml）


---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: https://blog.yyt6801.top/posts/github-actions/  

