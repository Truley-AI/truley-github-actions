# 快速設定指南

## 📦 創建新儲存庫

### 1. 在 GitHub 上創建儲存庫

1. 前往 https://github.com/orgs/Truley-AI/repositories
2. 點擊 "New repository"
3. 設定：
   - **Repository name**: `truley-github-actions`
   - **Description**: `Shared GitHub Actions for Truley AI projects`
   - **Visibility**: Public
   - **Initialize**: 不要勾選任何選項（我們會上傳現有檔案）

### 2. 將檔案上傳到新儲存庫

```bash
# 1. 克隆新創建的空儲存庫
git clone https://github.com/Truley-AI/truley-github-actions.git
cd truley-github-actions

# 2. 複製所有檔案（從這個準備好的檔案包）
# 將 /tmp/truley-github-actions-setup/ 中的所有檔案複製到此目錄

# 3. 提交初始代碼
git add .
git commit -m "feat: initial release of shared GitHub Actions

- Add get-branch-name action for Docker tag generation
- Add get-tag-info action for release management  
- Add get-version-info action for version detection
- Include comprehensive documentation and examples"

# 4. 推送到遠端
git push origin main

# 5. 創建發布標籤
git tag -a v1.0.0 -m "Release v1.0.0 - Initial stable release"
git push origin v1.0.0

# 6. 創建主要版本標籤
git tag v1
git push origin v1
```

### 3. 在 GitHub 上創建 Release

1. 前往儲存庫頁面
2. 點擊 "Releases" → "Create a new release"
3. 選擇 tag `v1.0.0`
4. 標題：`Release v1.0.0`
5. 描述：
```markdown
## 🎉 Initial Release

首次發布 Truley AI 共享 GitHub Actions。

### 📦 包含的 Actions

- **get-branch-name**: 生成 Docker 相容的分支標籤
- **get-tag-info**: 解析 Git tag 資訊用於發布管理
- **get-version-info**: 從 package.json 或 Git 提取版本資訊

### 🚀 使用方式

```yaml
- name: Get branch name
  uses: Truley-AI/truley-github-actions/get-branch-name@v1
```

查看各個 action 目錄中的 README 了解詳細用法。
```

## 🔄 更新現有專案

### 在 truley_ai_agent 專案中

將以下用法：
```yaml
uses: ./.github/actions/get-branch-name
uses: ./.github/actions/get-tag-info  
uses: ./.github/actions/get-version-info
```

替換為：
```yaml
uses: Truley-AI/truley-github-actions/get-branch-name@v1
uses: Truley-AI/truley-github-actions/get-tag-info@v1
uses: Truley-AI/truley-github-actions/get-version-info@v1
```

### 需要更新的檔案

- `.github/workflows/build.yml`
- `.github/workflows/deploy-uat.yml`
- `.github/workflows/deploy-staging.yml`
- 其他使用這些 actions 的工作流程

## ✅ 驗證

1. 推送變更後，檢查 Actions 是否正常運行
2. 創建測試 PR 驗證功能
3. 確認所有輸出值正確

## 🧹 清理

確認一切正常後：
1. 刪除原專案的 `.github/actions/` 目錄
2. 提交清理變更

完成！🎉