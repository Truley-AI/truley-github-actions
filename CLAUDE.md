# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a collection of shared GitHub Actions for Truley AI projects. The repository contains three composite actions that provide reusable CI/CD workflow components:

1. **get-branch-name** - Generates Docker-compatible branch tags
2. **get-tag-info** - Extracts and analyzes Git tag information for releases  
3. **get-version-info** - Retrieves version information from package.json or Git

## Architecture

Each action is organized as a standalone directory containing:
- `action.yml` - The action definition with inputs, outputs, and composite steps
- `README.md` - Detailed documentation with usage examples

All actions are composite actions using shell scripts (bash) rather than Docker or Node.js actions for better performance and compatibility.

## Common Development Tasks

### Testing Actions Locally
```bash
# Install act for local GitHub Actions testing
brew install act

# Test action syntax
act -j test-action

# Simulate different events
act push -e .github/events/push.json
act pull_request -e .github/events/pr.json
```

### Version Management and Releases
```bash
# Create and push a new version tag
git tag v1.2.3
git push origin v1.2.3

# Update major version tag (for users referencing @v1)
git tag -f v1
git push -f origin v1
```

### Action Development Pattern
- Each action should be self-contained in its directory
- Use composite actions with shell scripts for maximum compatibility
- Include comprehensive error handling and logging
- Follow semantic versioning for releases
- Provide clear branding with appropriate icons and colors

## Key Files

- `action.yml` files define the action interface and implementation
- Individual README files contain usage documentation and examples
- `SETUP_GUIDE.md` contains repository setup and migration instructions

## Action Usage Patterns

Actions are designed to be used in GitHub workflows with version references:
- `@v1` - Latest v1.x.x (recommended for stability)
- `@v1.2` - Latest v1.2.x
- `@v1.2.3` - Exact version
- `@main` - Development version (not recommended for production)

Common usage patterns involve chaining these actions for comprehensive CI/CD pipelines that handle branch detection, version management, and release automation.