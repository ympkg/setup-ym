<h1 align="center">setup-ym</h1>

<p align="center">
  <strong>GitHub Action to install <a href="https://github.com/ympkg/yummy">ym</a></strong><br>
  Auto platform detection · Version pinning · Binary caching
</p>

<p align="center">
  <a href="https://github.com/ympkg/setup-ym"><img src="https://img.shields.io/github/v/tag/ympkg/setup-ym?label=action" alt="Action version" /></a>
  <a href="https://github.com/ympkg/yummy/releases"><img src="https://img.shields.io/github/v/release/ympkg/yummy?include_prereleases&label=ym" alt="ym version" /></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="License" /></a>
</p>

<p align="center">
  <a href="#usage">Usage</a> ·
  <a href="#inputs">Inputs</a> ·
  <a href="#outputs">Outputs</a> ·
  <a href="#examples">Examples</a> ·
  <a href="#platforms">Platforms</a>
</p>

<p align="center">
  🌐 <a href="README.md">English</a> · <a href="docs/i18n/zh-CN/README.md">简体中文</a>
</p>

---

## Usage

```yaml
steps:
  - uses: actions/checkout@v4
  - uses: actions/setup-java@v4
    with:
      distribution: zulu
      java-version: '25'
  - uses: ympkg/setup-ym@v1
  - run: ymc build
```

## Inputs

| Name | Default | Description |
|------|---------|-------------|
| `version` | `latest` | ym version — `latest`, `dev`, or specific (e.g. `0.6.0`) |
| `cache` | `true` | Cache ym binary across workflow runs |

## Outputs

| Name | Description |
|------|-------------|
| `ym-version` | The version that was installed |
| `ym-path` | Path to the directory containing `ym` / `ymc` |

## Examples

### Basic — build a project

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
          java-version: '25'
      - uses: ympkg/setup-ym@v1
      - run: ymc build
```

### Pin a specific version

```yaml
- uses: ympkg/setup-ym@v1
  with:
    version: '0.3.10'
```

### Use dev channel

```yaml
- uses: ympkg/setup-ym@v1
  with:
    version: dev
```

### Build + test + Docker

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
          java-version: '25'
      - uses: ympkg/setup-ym@v1

      - run: ymc build
      - run: ymc test

      - uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: myapp:latest
```

### Private Maven registry

```yaml
- run: ymc build
  env:
    YM_REGISTRY_USERNAME: ${{ secrets.MAVEN_USER }}
    YM_REGISTRY_PASSWORD: ${{ secrets.MAVEN_PASSWORD }}
```

### Workspace — build a specific module

```yaml
- run: ymc build my-service
```

## Platforms

| Runner | Target | Archive |
|--------|--------|---------|
| `ubuntu-latest` | `x86_64-unknown-linux-musl` | `.tar.gz` |
| `windows-latest` | `x86_64-pc-windows-msvc` | `.zip` |
| `macos-latest` | `aarch64-apple-darwin` | `.tar.gz` |
| `macos-13` | `x86_64-apple-darwin` | `.tar.gz` |

## How it works

1. Detects runner OS and architecture
2. Resolves the requested version from [GitHub Releases](https://github.com/ympkg/yummy/releases)
3. Restores from cache if available (keyed by version + platform)
4. Downloads and extracts the binary to `~/.ym/bin`
5. Copies `ym` → `ymc` (same binary, behavior determined by `argv[0]`)
6. Adds `~/.ym/bin` to `$GITHUB_PATH`

## License

[MIT](LICENSE)
