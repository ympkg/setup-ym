# setup-ym

GitHub Action to install [Yummy (ym)](https://github.com/ympkg/yummy) — a fast Java build tool written in Rust.

## Usage

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

### Inputs

| Name | Default | Description |
|------|---------|-------------|
| `version` | `latest` | `latest`, `dev`, or specific version (e.g. `0.6.0`) |
| `cache` | `true` | Cache ym binary across runs |

### Outputs

| Name | Description |
|------|-------------|
| `ym-version` | Installed version |
| `ym-path` | Path to ym binary directory |

### Pin version

```yaml
- uses: ympkg/setup-ym@v1
  with:
    version: '0.6.0'
```

### Dev channel

```yaml
- uses: ympkg/setup-ym@v1
  with:
    version: dev
```

## Platforms

| Runner | Target |
|--------|--------|
| `ubuntu-latest` | `x86_64-unknown-linux-gnu` |
| `windows-latest` | `x86_64-pc-windows-msvc` |
| `macos-latest` | `aarch64-apple-darwin` |
| `macos-13` | `x86_64-apple-darwin` |
