# Truley GitHub Actions

Simple GitHub Actions for Truley AI projects.

## Actions

### get-tag
Extract Git tag name from tag events.

**Output:**
- `tag` - Git tag name (e.g., `v1.0.0`)

```yaml
- name: Get tag
  id: get-tag
  uses: Truley-AI/truley-github-actions/get-tag@v1

- name: Use tag
  run: echo "Tag is ${{ steps.get-tag.outputs.tag }}"
```

### get-branch
Get branch name and branch name with commit hash.

**Outputs:**
- `name` - Branch name (e.g., `fix-abc`)
- `name-with-hash` - Branch name with commit hash (e.g., `fix-abc-a1b2c3d`)

```yaml
- name: Get branch
  id: get-branch
  uses: Truley-AI/truley-github-actions/get-branch@v1

- name: Use branch
  run: |
    echo "Branch name: ${{ steps.get-branch.outputs.name }}"
    echo "Name with hash: ${{ steps.get-branch.outputs.name-with-hash }}"
```