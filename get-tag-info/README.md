# Get Tag Info Action

提取 Git tag 資訊的 GitHub Action。

## 功能

- 檢測是否為 tag 事件
- 提取 tag 名稱和版本號
- 判斷是否為預發布版本
- 支援語義化版本控制

## 輸出

| 輸出名稱 | 描述 | 範例 |
|---------|------|------|
| `tag` | Git tag 名稱 | `v1.2.3` |
| `version` | 移除 v 前綴的版本號 | `1.2.3` |
| `is-prerelease` | 是否為預發布版本 | `true`/`false` |
| `is-release` | 是否為正式版本 | `true`/`false` |

## 使用範例

```yaml
name: Release

on:
  push:
    tags:
      - 'v*'

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        
      - name: Get tag info
        id: tag
        uses: Truley-AI/truley-github-actions/get-tag-info@v1
        
      - name: Create release
        if: steps.tag.outputs.is-release == 'true'
        run: |
          echo "Creating release for version ${{ steps.tag.outputs.version }}"
          
      - name: Create prerelease
        if: steps.tag.outputs.is-prerelease == 'true'
        run: |
          echo "Creating prerelease for version ${{ steps.tag.outputs.version }}"
```

## 預發布版本判斷規則

以下情況會被判斷為預發布版本：

1. 包含 `alpha`、`beta`、`rc`、`dev` 關鍵字
2. 包含連字號 `-`

### 範例

| Tag | Version | Is Prerelease | Is Release |
|-----|---------|---------------|------------|
| `v1.0.0` | `1.0.0` | `false` | `true` |
| `v1.0.0-beta` | `1.0.0-beta` | `true` | `false` |
| `v1.0.0-alpha.1` | `1.0.0-alpha.1` | `true` | `false` |
| `v1.0.0-rc.1` | `1.0.0-rc.1` | `true` | `false` |
| `v1.0.0-dev` | `1.0.0-dev` | `true` | `false` |

## 支援的事件

- `push` 到 tags: 處理 tag 資訊
- 其他事件: 返回空值

## 工作流程觸發器設定

```yaml
on:
  push:
    tags:
      - 'v*'           # 所有 v 開頭的 tags
      - 'v[0-9]+.*'    # 語義化版本 tags
```