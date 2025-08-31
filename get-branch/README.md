# Get Branch Action

Get branch name and branch name with commit hash. Branch names are Docker-safe (slashes replaced with hyphens).

## Outputs

- `name` - Branch name, Docker-safe with slashes replaced by hyphens (e.g., `feature-new-ui`, `fix-abc`)
- `name-with-hash` - Branch name with commit hash, Docker-safe (e.g., `feature-new-ui-a1b2c3d`, `fix-abc-a1b2c3d`)

## Usage

```yaml
- name: Get branch
  id: get-branch
  uses: Truley-AI/truley-github-actions/get-branch@v1
  
- name: Use branch
  run: |
    echo "Branch name: ${{ steps.get-branch.outputs.name }}"
    echo "Name with hash: ${{ steps.get-branch.outputs.name-with-hash }}"
```

## Examples

For branch `fix-abc` with commit `a1b2c3d`:
- `name`: `fix-abc`
- `name-with-hash`: `fix-abc-a1b2c3d`

For branch `feature/new-ui` with commit `a1b2c3d`:
- `name`: `feature-new-ui` (slash converted to hyphen for Docker compatibility)
- `name-with-hash`: `feature-new-ui-a1b2c3d`

## Docker Usage

The outputs are safe to use directly as Docker tags:

```yaml
- name: Get branch
  id: get-branch
  uses: Truley-AI/truley-github-actions/get-branch@v1
  
- name: Build and push Docker image
  run: |
    docker build -t myapp:${{ steps.get-branch.outputs.name-with-hash }} .
    docker push myapp:${{ steps.get-branch.outputs.name-with-hash }}
```