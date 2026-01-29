# AGENTS.md - Developer Guide for Coder Images Repository

This repository contains Docker images for Coder development environments targeting Azure infrastructure and development workflows.

## Repository Overview

This is a monorepo containing multiple Docker image configurations:
- `azurefunction/` - Python 3.12 with Azure Functions Core Tools v4
- `staticwebapp/` - Node.js 22 with Azure Static Web Apps CLI
- `azureopentofu/` - Ubuntu 22.04 with OpenTofu, Trivy security scanner
- `azureterraform/` - Debian 13 with Terraform, OpenCode AI
- `azureterragrunt/` - Ubuntu 22.04 with Terraform, Terragrunt, Trivy

## Build Commands

### Building Docker Images Locally

```bash
# Build a specific image
docker build -t coder_azurefunction ./azurefunction
docker build -t coder_staticwebapp ./staticwebapp
docker build -t coder_azureopentofu ./azureopentofu
docker build -t coder_azureterraform ./azureterraform
docker build -t coder_azureterragrunt ./azureterragrunt

# Build with multi-arch support (for images that support it)
docker buildx build --platform linux/amd64,linux/arm64 -t coder_staticwebapp ./staticwebapp
docker buildx build --platform linux/amd64,linux/arm64 -t coder_azureopentofu ./azureopentofu
docker buildx build --platform linux/amd64,linux/arm64 -t coder_azureterraform ./azureterraform
docker buildx build --platform linux/amd64,linux/arm64 -t coder_azureterragrunt ./azureterragrunt
```

### Testing Images

```bash
# Run and test an image interactively
docker run -it --rm coder_azureterraform /bin/bash

# Verify installed tools
docker run --rm coder_azureterraform terraform --version
docker run --rm coder_azureopentofu tofu --version
docker run --rm coder_azureterragrunt terragrunt --version
docker run --rm coder_staticwebapp node --version
docker run --rm coder_azurefunction func --version
```

### CI/CD Deployment

Images are built and published via GitHub Actions workflow (`.github/workflows/main.yml`):

```bash
# Trigger via GitHub UI (workflow_dispatch)
# Select image: all, azurefunction, staticwebapp, azureopentofu, azureterraform, or azureterragrunt
```

Published to: `ghcr.io/peanutsguy/coder_<image-name>:latest`

## Utility Scripts (Terraform/OpenTofu/Terragrunt Images)

Located in `*/bin/` directories, copied to `/usr/local/bin/` in containers:

### tfcheck
Validates Terraform/OpenTofu configurations:
```bash
# Validate current directory
tfcheck

# Validate specific directory
tfcheck /path/to/terraform/module
```

### module
Scaffolds a new Terraform module with standard structure:
```bash
# Create module with Azure provider version
module <module-name> <azurerm-provider-version>

# Example
module my_storage_account "~> 3.0"
```

Creates: `modules/<name>/main.tf`, `variables.tf`, `output.tf` with boilerplate

### gittar
Creates tarball of git-changed files:
```bash
# Create archive of changed files since commit
gittar archive.tar.gz <commit-hash>
```

## Code Style Guidelines

### Dockerfile Standards

**Base Images:**
- Use official, stable base images (Debian, Ubuntu LTS)
- Specify exact versions, not `latest` tags
- azurefunction: `python:3.12`
- staticwebapp: `debian:bookworm`
- azureopentofu: `ubuntu:22.04`
- azureterraform: `debian:13-slim`
- azureterragrunt: `ubuntu:22.04`

**Environment Variables:**
- Always set `DEBIAN_FRONTEND=noninteractive` for apt operations
- Set `LANG` and `LC_ALL` to `C.UTF-8` for proper locale support
- Use `ENV` directives at the top of Dockerfile

**Package Installation:**
- Combine related `apt-get` commands with `&&` to reduce layers
- Always run `apt-get update` before `apt-get install`
- Clean up with `rm -rf /var/lib/apt/lists/*` after installations
- Install essential tools: `git`, `curl`, `sudo`, `vim`, `wget`, `jq`

**Labels:**
- Include `org.opencontainers.image.source` pointing to GitHub repo
- Format: `LABEL org.opencontainers.image.source="https://github.com/peanutsguy/coder_images"`

**User Setup:**
- Create `coder` user with UID 1000
- Add to sudo group with NOPASSWD
- Set working directory to `/home/coder`
- Always switch to non-root user with `USER coder` at end

**Multi-arch Support:**
- Use `ARG TARGETARCH` for architecture-specific downloads
- Build for `linux/amd64,linux/arm64` where possible
- Test on both architectures before merging

### Shell Script Standards (bin/ utilities)

**Shebang:**
```bash
#!/bin/bash
```

**Error Handling:**
- Check for required arguments with conditionals
- Use meaningful variable names
- Provide defaults when appropriate

**Formatting:**
- Use tabs for indentation (matching existing style)
- Add comments for complex operations
- Keep scripts concise and focused

**Exit Codes:**
- Rely on command exit codes (terraform, git, etc.)
- Don't suppress errors unless intentional

### Git Commit Messages

Based on repository history:
- Use imperative mood: "Add", "Modify", "Fix", "Migrate"
- Be specific about what changed and why
- Examples:
  - "Add Azure Terragrunt support with Dockerfile and utility scripts"
  - "Modify OpenCode AI installation to use system-wide path instead of HOME directory"
  - "Migrate Azure Terraform image from Ubuntu 22.04 to Debian 13 slim"

### Naming Conventions

**Directories:**
- Lowercase, descriptive: `azurefunction`, `azureterraform`, `azureopentofu`
- No hyphens in directory names (use underscores if needed)

**Files:**
- `Dockerfile` - standard Docker builds
- `Dockerfile_forgejo` - variant builds (if needed)
- Utility scripts: lowercase, no extensions unless shell scripts

**Container Images:**
- Format: `coder_<purpose>` (e.g., `coder_azureterraform`)
- Registry path: `ghcr.io/peanutsguy/coder_<suffix>`

## Testing Checklist

Before submitting changes:

1. Build image locally successfully
2. Run image and verify tools are accessible
3. Check tool versions match expected versions
4. Verify `coder` user has proper permissions
5. Test utility scripts (tfcheck, module, gittar) if applicable
6. Ensure image size is reasonable (check for unnecessary files)
7. Verify multi-arch builds work (if applicable)

## Common Patterns

### Adding New Tools

1. Research official installation method (prefer package manager)
2. Add to appropriate Dockerfile section
3. Clean up installation artifacts
4. Verify tool is in PATH
5. Update this AGENTS.md with tool information

### Updating Base Images

1. Check for breaking changes in new version
2. Update Dockerfile with new version
3. Test build thoroughly
4. Update locale/environment variables if needed
5. Document migration in commit message

### Creating New Image Type

1. Create new directory: `azure<purpose>/`
2. Create `Dockerfile` following standards above
3. Add to `.github/workflows/main.yml` with new job
4. Add environment variable for suffix
5. Create `bin/` directory if custom scripts needed
6. Test locally before pushing

## Security Considerations

- Never commit secrets or credentials
- Use `--no-install-recommends` for minimal attack surface (when needed)
- Keep base images updated regularly
- Trivy scanner included in some images for security scanning
- Run containers as non-root user (`coder`)
