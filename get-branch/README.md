# Get Branch Action

Get branch name and branch name with commit hash.

## Outputs

- `name` - Branch name (e.g., `fix-abc`)
- `name-with-hash` - Branch name with commit hash (e.g., `fix-abc-a1b2c3d`)

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

## Example

For branch `fix-abc` with commit `a1b2c3d`, this action will output:
- `name`: `fix-abc`
- `name-with-hash`: `fix-abc-a1b2c3d`