# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

这是一个 GitHub Actions 项目,用于自动构建静态链接的 Nginx 二进制文件,支持多架构 (amd64/arm64)。

## 核心架构

项目仅包含一个 GitHub Actions workflow 文件,采用容器化编译策略:

- **构建环境**: CentOS 7 Docker 容器 (通过 QEMU 支持多架构)
- **编译方式**: 静态链接 (static linking),所有依赖库编译进二进制文件
- **支持的架构**: amd64, arm64
- **触发方式**: 手动触发 (workflow_dispatch)

## 版本管理

所有依赖版本集中在 workflow 文件的 `env` 部分管理:

```yaml
env:
  NGINX_VERSION: "1.28.1"
  OPENSSL_VERSION: "3.0.15"
  PCRE2_VERSION: "10.42"
  ZLIB_VERSION: "1.3.1"
```

更新版本时只需修改这些变量,无需修改编译脚本。

## 编译配置

Nginx 使用以下模块编译:

- HTTP SSL 模块 (HTTPS 支持)
- HTTP/2 模块
- RealIP 模块
- Stub Status 模块
- Gzip Static 模块
- Stream 模块 (TCP/UDP 代理)
- Stream SSL 模块
- Stream RealIP 模块

编译优化参数: `-O3 -static`

## 常用操作

### 手动触发构建

1. 进入 GitHub 仓库页面
2. 点击 "Actions" 标签
3. 选择 "Build Static Nginx (Multi-Version)" workflow
4. 点击 "Run workflow" 按钮

### 更新 Nginx 或依赖版本

编辑 `.github/workflows/build.yml` 文件中的 `env` 部分,修改对应的版本号,然后提交。

### 添加新的 Nginx 模块

在 `./configure` 命令中添加相应的 `--with-<module>` 参数。

## 产物

构建完成后,二进制文件作为 GitHub Actions artifact 上传,命名格式:

- `nginx-{NGINX_VERSION}-{arch}` (例如: `nginx-1.28.1-amd64`)
- `nginx-{NGINX_VERSION}-{arch}` (例如: `nginx-1.28.1-arm64`)

从 Actions 运行页面可以下载这些产物。
