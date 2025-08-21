# Get Version Info Action

從 package.json 或 git 獲取版本資訊的 GitHub Action。

## 功能

- 從 package.json 讀取版本號
- 當 package.json 不存在時回退到 git 版本
- 生成基於分支和 commit 的版本標籤
- 提供版本來源資訊

## 輸入

| 輸入名稱 | 描述 | 必需 | 預設值 |
|---------|------|------|-------|
| `package-path` | package.json 檔案路徑 | 否 | `./package.json` |
| `fallback-to-git` | package.json 不存在時使用 git 資訊 | 否 | `true` |

## 輸出

| 輸出名稱 | 描述 | 範例 |
|---------|------|------|
| `version` | 最終使用的版本 | `1.2.3` 或 `main-a1b2c3d` |
| `package-version` | package.json 中的版本 | `1.2.3` |
| `git-version` | Git 版本格式 | `main-a1b2c3d` |
| `source` | 版本來源 | `package` 或 `git` |

## 使用範例

### 基本使用

```yaml
name: Build

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        
      - name: Get version info
        id: version
        uses: Truley-AI/truley-github-actions/get-version-info@v1
        
      - name: Build with version
        run: |
          echo "Building version: ${{ steps.version.outputs.version }}"
          echo "Source: ${{ steps.version.outputs.source }}"
```

### 自訂 package.json 路徑

```yaml
      - name: Get version info
        id: version
        uses: Truley-AI/truley-github-actions/get-version-info@v1
        with:
          package-path: './frontend/package.json'
```

### 禁用 git 回退

```yaml
      - name: Get version info
        id: version
        uses: Truley-AI/truley-github-actions/get-version-info@v1
        with:
          fallback-to-git: 'false'
```

### 條件式建構

```yaml
      - name: Build from package
        if: steps.version.outputs.source == 'package'
        run: |
          echo "Building release version: ${{ steps.version.outputs.package-version }}"
          
      - name: Build from git
        if: steps.version.outputs.source == 'git'
        run: |
          echo "Building development version: ${{ steps.version.outputs.git-version }}"
```

## 版本來源優先順序

1. **package.json**: 如果檔案存在且包含版本號
2. **git**: 當 `fallback-to-git` 為 `true` 且 package.json 不可用時

## Git 版本格式

- 格式: `{分支名}-{commit SHA}`
- 分支名會被清理以符合標籤規範
- commit SHA 截取前 7 個字元

### 範例

| 分支 | Commit | Git Version |
|-----|--------|-------------|
| `main` | `a1b2c3d4e5f6` | `main-a1b2c3d` |
| `feature/auth` | `1234567890ab` | `feature-auth-1234567` |

## 錯誤處理

當 `fallback-to-git` 為 `false` 且找不到 package.json 時，action 會失敗並返回錯誤代碼 1。

## 專案類型支援

- **Node.js**: 預設支援 package.json
- **其他語言**: 可設定 `fallback-to-git: true` 使用 git 版本
- **Monorepo**: 可指定特定路徑的 package.json