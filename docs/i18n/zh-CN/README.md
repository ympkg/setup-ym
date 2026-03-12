<h1 align="center">setup-ym</h1>

<p align="center">
  <strong>安装 <a href="https://github.com/ympkg/yummy">ym</a> 的 GitHub Action</strong><br>
  自动平台检测 · 版本锁定 · 二进制缓存
</p>

<p align="center">
  <a href="https://github.com/ympkg/setup-ym"><img src="https://img.shields.io/github/v/tag/ympkg/setup-ym?label=action" alt="Action version" /></a>
  <a href="https://github.com/ympkg/yummy/releases"><img src="https://img.shields.io/github/v/release/ympkg/yummy?include_prereleases&label=ym" alt="ym version" /></a>
  <a href="../../../LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="License" /></a>
</p>

<p align="center">
  <a href="#用法">用法</a> ·
  <a href="#输入参数">输入参数</a> ·
  <a href="#输出">输出</a> ·
  <a href="#示例">示例</a> ·
  <a href="#支持平台">支持平台</a>
</p>

<p align="center">
  🌐 <a href="../../../README.md">English</a> · <a href="README.md">简体中文</a>
</p>

---

## 用法

```yaml
steps:
  - uses: actions/checkout@v4
  - uses: actions/setup-java@v4
    with:
      distribution: zulu
      java-version: '21'
  - uses: ympkg/setup-ym@v1
  - run: ymc build
```

## 输入参数

| 名称 | 默认值 | 说明 |
|------|--------|------|
| `version` | `latest` | ym 版本 — `latest`、`dev` 或指定版本（如 `0.6.0`） |
| `cache` | `true` | 跨工作流缓存 ym 二进制 |

## 输出

| 名称 | 说明 |
|------|------|
| `ym-version` | 安装的版本号 |
| `ym-path` | `ym` / `ymc` 所在目录路径 |

## 示例

### 基础 — 编译项目

```yaml
name: Build
on: push

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          distribution: zulu
          java-version: '21'
      - uses: ympkg/setup-ym@v1
      - run: ymc build
```

### 锁定版本

```yaml
- uses: ympkg/setup-ym@v1
  with:
    version: '0.6.0'
```

### 使用 dev 通道

```yaml
- uses: ympkg/setup-ym@v1
  with:
    version: dev
```

### 编译 + 测试 + Docker

```yaml
name: CI
on: push

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          distribution: zulu
          java-version: '21'
      - uses: ympkg/setup-ym@v1

      - run: ymc build
      - run: ymc test

      - uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: myapp:latest
```

### 私有 Maven 仓库

```yaml
- run: ymc build
  env:
    YM_REGISTRY_USERNAME: ${{ secrets.MAVEN_USER }}
    YM_REGISTRY_PASSWORD: ${{ secrets.MAVEN_PASSWORD }}
```

### 工作空间 — 编译指定模块

```yaml
- run: ymc build my-service
```

## 支持平台

| Runner | Target | 归档格式 |
|--------|--------|----------|
| `ubuntu-latest` | `x86_64-unknown-linux-gnu` | `.tar.gz` |
| `windows-latest` | `x86_64-pc-windows-msvc` | `.zip` |
| `macos-latest` | `aarch64-apple-darwin` | `.tar.gz` |
| `macos-13` | `x86_64-apple-darwin` | `.tar.gz` |

## 工作原理

1. 检测 Runner 操作系统和架构
2. 从 [GitHub Releases](https://github.com/ympkg/yummy/releases) 解析目标版本
3. 检查缓存（按版本 + 平台 key）
4. 下载并解压二进制到 `~/.ym/bin`
5. 复制 `ym` → `ymc`（同一二进制，通过 `argv[0]` 区分行为）
6. 将 `~/.ym/bin` 加入 `$GITHUB_PATH`

## 许可证

[MIT](../../../LICENSE)
