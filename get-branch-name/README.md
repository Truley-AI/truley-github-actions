# Get Branch Name Action

Extracts and sanitizes branch names for Docker tag generation in GitHub workflows.

## Overview

This action retrieves the current branch name from GitHub events and transforms it into a Docker-compatible tag format. It automatically handles branch name sanitization and generates consistent tagging schemes for CI/CD pipelines.

## Outputs

| Output | Description | Example |
|--------|-------------|---------|
| `branch-name` | Sanitized branch name | `feature-auth-system` |
| `tag` | Docker tag format (branch-commit) | `feature-auth-system-a1b2c3d` |

## Quick Start

```yaml
- name: Get branch info
  id: branch
  uses: Truley-AI/truley-github-actions/get-branch-name@v1

- name: Build Docker image
  run: docker build -t myapp:${{ steps.branch.outputs.tag }} .
```

## Complete Example

```yaml
name: CI Pipeline

on:
  push:
    branches: [ main, develop, 'feature/*' ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        
      - name: Get branch info
        id: branch
        uses: Truley-AI/truley-github-actions/get-branch-name@v1
        
      - name: Build and tag Docker image
        run: |
          docker build -t myapp:${{ steps.branch.outputs.tag }} .
          docker tag myapp:${{ steps.branch.outputs.tag }} myapp:${{ steps.branch.outputs.branch-name }}-latest
          
      - name: Display build info
        run: |
          echo "Branch: ${{ steps.branch.outputs.branch-name }}"
          echo "Docker tag: ${{ steps.branch.outputs.tag }}"
```

## Branch Name Sanitization

The action applies these transformations to ensure Docker compatibility:

1. **Character replacement**: Non-alphanumeric characters (except `.`, `_`, `-`) → `-`
2. **Deduplicate hyphens**: Multiple consecutive `-` → single `-`
3. **Trim hyphens**: Remove leading/trailing `-`
4. **Lowercase conversion**: All uppercase → lowercase

### Transformation Examples

| Original Branch | Sanitized Result |
|----------------|------------------|
| `feature/user-auth` | `feature-user-auth` |
| `hotfix/fix_bug#123` | `hotfix-fix-bug-123` |
| `Feature/New-UI` | `feature-new-ui` |
| `release/v2.1.0` | `release-v2-1-0` |
| `user@company/fix` | `user-company-fix` |

## Supported Events

| Event Type | Branch Source | Notes |
|------------|---------------|-------|
| `push` | `github.ref_name` | Branch pushes only |
| `pull_request` | `github.head_ref` | Source branch of PR |

## Tag Event Handling

⚠️ **Important**: This action is designed for branch events only.

- **Tag events** (e.g., `v1.0.0`) return empty outputs
- Use [`get-tag-info`](../get-tag-info/) action for tag-based workflows
- Helpful error message guides users to the correct action

## Output Format

- **Branch name**: Sanitized for Docker compliance
- **Tag format**: `{branch-name}-{commit-sha}`
- **Commit SHA**: First 7 characters of `github.sha`

## Limitations

- Docker tag length limit: 128 characters
- Only processes branch events (not tags)
- Commit SHA is truncated to 7 characters

## Related Actions

- [`get-tag-info`](../get-tag-info/) - For tag event processing
- [`get-version-info`](../get-version-info/) - For version extraction