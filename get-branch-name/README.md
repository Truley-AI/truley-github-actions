# Get Branch Name Action

獲取並清理分支名稱用於 Docker tags 的 GitHub Action。

## 功能

- 獲取當前分支名稱
- 清理分支名稱使其符合 Docker tag 規範
- 生成 `分支名-commit` 格式的標籤
- 支援 Pull Request 事件

## 輸出

| 輸出名稱 | 描述 | 範例 |
|---------|------|------|
| `branch-name` | 清理後的分支名稱 | `feature-auth` |
| `tag` | Docker tag 格式 | `feature-auth-a1b2c3d` |

## 使用範例

```yaml
name: Build Docker Image

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        
      - name: Get branch name
        id: branch
        uses: Truley-AI/truley-github-actions/get-branch-name@v1
        
      - name: Build Docker image
        run: |
          docker build -t truley-app:${{ steps.branch.outputs.tag }} .
          
      - name: Display info
        run: |
          echo "Branch: ${{ steps.branch.outputs.branch-name }}"
          echo "Tag: ${{ steps.branch.outputs.tag }}"
```

## 分支名稱清理規則

1. 將非字母數字、點、底線、連字號的字元替換為連字號
2. 移除連續的連字號
3. 移除開頭和結尾的連字號
4. 轉換為小寫

### 範例轉換

| 原始分支名稱 | 清理後 |
|-------------|--------|
| `feature/user-auth` | `feature-user-auth` |
| `hotfix/fix_bug#123` | `hotfix-fix-bug-123` |
| `Feature/New-UI` | `feature-new-ui` |

## 支援的事件

- `push`: 使用 `github.ref_name` (僅限分支，不支援 tag 事件)
- `pull_request`: 使用 `github.head_ref`

## Tag 事件處理

**重要**: 此 action 專門用於分支名稱處理，不適用於 tag 事件。

- 當檢測到 tag 事件時，會返回空值並提示使用 `get-tag-info` action
- 如需處理 tag 資訊，請使用 [`get-tag-info`](../get-tag-info/) action

## 注意事項

- Docker tag 長度限制為 128 字元
- 此 action 會自動處理分支名稱以符合 Docker 規範
- commit SHA 會截取前 7 個字元
- Tag 事件會返回空值，請使用對應的 tag action