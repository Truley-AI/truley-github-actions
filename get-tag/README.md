# Get Tag Action

Extract Git tag name from tag events.

## Outputs

- `tag` - Git tag name (e.g., `v1.0.0`)

## Usage

```yaml
- name: Get tag
  id: get-tag
  uses: Truley-AI/truley-github-actions/get-tag@v1
  
- name: Use tag
  run: echo "Tag is ${{ steps.get-tag.outputs.tag }}"
```

## Example

When a tag `v1.0.0` is pushed, this action will output:
- `tag`: `v1.0.0`