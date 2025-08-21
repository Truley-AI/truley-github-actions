# Truley GitHub Actions

Truley AI 組織的共享 GitHub Actions 集合，提供可重用的 CI/CD 工作流程組件。

## 📦 Available Actions

### 🌿 [get-branch-name](./get-branch-name/)
獲取並清理分支名稱用於 Docker tags。

**使用場景**: Docker 映像標籤、版本標識、部署識別

```yaml
- name: Get branch name
  id: branch
  uses: Truley-AI/truley-github-actions/get-branch-name@v1

- name: Use branch info
  run: |
    echo "Branch: ${{ steps.branch.outputs.branch-name }}"
    echo "Tag: ${{ steps.branch.outputs.tag }}"
```

### 🏷️ [get-tag-info](./get-tag-info/)
提取 Git tag 資訊和版本分析。

**使用場景**: 版本發布、預發布判斷、自動化部署

```yaml
- name: Get tag info
  id: tag
  uses: Truley-AI/truley-github-actions/get-tag-info@v1

- name: Create release
  if: steps.tag.outputs.is-release == 'true'
  run: echo "Release version: ${{ steps.tag.outputs.version }}"
```

### 📋 [get-version-info](./get-version-info/)
從 package.json 或 git 獲取版本資訊。

**使用場景**: 版本管理、建構標籤、多語言專案支援

```yaml
- name: Get version info
  id: version
  uses: Truley-AI/truley-github-actions/get-version-info@v1
  with:
    package-path: './package.json'
    fallback-to-git: 'true'

- name: Build with version
  run: echo "Version: ${{ steps.version.outputs.version }}"
```

## 🚀 Quick Start

### 1. 在工作流程中使用

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
    tags: [ 'v*' ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      # 獲取分支資訊
      - name: Get branch info
        id: branch
        uses: Truley-AI/truley-github-actions/get-branch-name@v1

      # 獲取版本資訊
      - name: Get version info
        id: version
        uses: Truley-AI/truley-github-actions/get-version-info@v1

      # 如果是 tag 則獲取 tag 資訊
      - name: Get tag info
        id: tag
        if: startsWith(github.ref, 'refs/tags/')
        uses: Truley-AI/truley-github-actions/get-tag-info@v1

      # 建構 Docker 映像
      - name: Build Docker image
        run: |
          if [ "${{ github.event_name }}" == "push" ] && [ "${{ startsWith(github.ref, 'refs/tags/') }}" == "true" ]; then
            # Tag 建構
            docker build -t truley-app:${{ steps.tag.outputs.version }} .
          else
            # 分支建構
            docker build -t truley-app:${{ steps.branch.outputs.tag }} .
          fi
```

### 2. 版本引用

推薦使用以下格式引用 actions：

- `@v1` - 主要版本，自動獲取最新的 v1.x.x
- `@v1.2` - 次要版本，自動獲取最新的 v1.2.x
- `@v1.2.3` - 特定版本，完全鎖定
- `@main` - 最新開發版本（不建議用於生產環境）

## 📋 Complete Example

以下是一個完整的 CI/CD 工作流程範例：

```yaml
name: Build and Deploy

on:
  push:
    branches: [ main, develop ]
    tags: [ 'v*' ]
  pull_request:
    branches: [ main ]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  build:
    runs-on: ubuntu-latest
    outputs:
      version: ${{ steps.version.outputs.version }}
      tag: ${{ steps.branch.outputs.tag }}
      is-release: ${{ steps.tag.outputs.is-release }}
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Get branch info
        id: branch
        uses: Truley-AI/truley-github-actions/get-branch-name@v1

      - name: Get version info
        id: version
        uses: Truley-AI/truley-github-actions/get-version-info@v1

      - name: Get tag info
        id: tag
        if: startsWith(github.ref, 'refs/tags/')
        uses: Truley-AI/truley-github-actions/get-tag-info@v1

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Log in to Container Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push Docker image
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: |
            ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ steps.version.outputs.version }}
            ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ steps.branch.outputs.tag }}

  deploy:
    needs: build
    if: github.ref == 'refs/heads/main' || needs.build.outputs.is-release == 'true'
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to staging
        if: github.ref == 'refs/heads/main'
        run: |
          echo "Deploying ${{ needs.build.outputs.tag }} to staging"

      - name: Deploy to production
        if: needs.build.outputs.is-release == 'true'
        run: |
          echo "Deploying ${{ needs.build.outputs.version }} to production"
```

## 🔧 Development

### 本地測試

```bash
# 測試 action 語法
act -j test-action

# 模擬不同事件
act push -e .github/events/push.json
act pull_request -e .github/events/pr.json
```

### 發布新版本

1. 更新 action.yml 檔案
2. 提交變更並推送
3. 創建新的 release tag
4. 更新主要版本標籤

```bash
git tag v1.2.3
git push origin v1.2.3

# 更新主要版本標籤
git tag -f v1
git push -f origin v1
```

## 📖 Documentation

每個 action 都有詳細的文檔：

- [get-branch-name/README.md](./get-branch-name/README.md)
- [get-tag-info/README.md](./get-tag-info/README.md)  
- [get-version-info/README.md](./get-version-info/README.md)

## 🤝 Contributing

1. Fork 此儲存庫
2. 創建功能分支 (`git checkout -b feature/new-action`)
3. 提交變更 (`git commit -am 'Add new action'`)
4. 推送到分支 (`git push origin feature/new-action`)
5. 創建 Pull Request

### Action 開發規範

- 每個 action 放在獨立目錄
- 包含 `action.yml` 和 `README.md`
- 使用語義化版本控制
- 提供完整的文檔和範例
- 包含適當的 branding

## 📜 License

MIT License - 詳見 [LICENSE](LICENSE) 檔案

## 🔗 Related

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Creating Actions](https://docs.github.com/en/actions/creating-actions)
- [Marketplace](https://github.com/marketplace?type=actions)