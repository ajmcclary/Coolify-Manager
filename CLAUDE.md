# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Claude Skill for managing and troubleshooting Coolify deployments. It extends Claude's capabilities with specialized knowledge for:
- Coolify CLI installation and configuration
- Service health diagnosis and troubleshooting
- WordPress-specific issue resolution on Coolify
- Container access and management
- API and CLI operations

## Architecture

### Skill Structure

This skill follows the Claude Skills specification with three main components:

1. **SKILL.md** - Main skill definition with YAML frontmatter
   - `name`: Skill identifier for Claude's skill system
   - `description`: Trigger conditions and use cases
   - Content: Step-by-step workflows, decision trees, and troubleshooting patterns

2. **scripts/** - Executable automation
   - `install_coolify_cli.sh`: Platform-aware CLI installer (supports darwin/linux, amd64/arm64)
   - `check_health.sh`: Connection and service health validator

3. **references/** - Loadable reference documentation
   - `api_endpoints.md`: REST API reference for direct HTTP calls
   - `cli_commands.md`: Complete CLI command reference
   - `wordpress_fixes.md`: WordPress-specific troubleshooting guide

### Progressive Disclosure Design

The skill uses a three-level context loading strategy:
1. **Metadata** (always loaded): Name + description from YAML frontmatter
2. **SKILL.md** (loaded when triggered): Core workflows and instructions
3. **references/** (loaded as needed): Detailed documentation loaded on demand

This keeps the context window efficient while providing deep expertise when required.

## Key Workflows

### Installation & Setup
```bash
# 1. Install CLI
bash scripts/install_coolify_cli.sh [version]

# 2. Configure context
coolify context add <name> <url> <token>

# 3. Verify
bash scripts/check_health.sh
```

### Service Diagnosis Pattern
When troubleshooting issues, follow this decision tree (from SKILL.md lines 93-107):

1. **Service Availability** → `coolify resource list` → Check logs → Fix → Verify
2. **WordPress Issues** → Access container terminal → Check .htaccess/config → Fix → Restart
3. **Performance/Config** → Manage env vars → Deploy changes

### Container Access Methods
Two primary methods for accessing Coolify containers:
1. **Coolify Web Terminal** (recommended): Dashboard → Service → Terminal → Select container
2. **Docker CLI**: `docker exec -it CONTAINER_NAME bash` (requires SSH access)

WordPress files location: `/var/www/html/`

## Common Development Tasks

### Testing Scripts

Validate shell script syntax:
```bash
bash -n scripts/install_coolify_cli.sh
bash -n scripts/check_health.sh
```

### Packaging the Skill

Package and validate the skill for distribution:
```bash
python3 /path/to/skill-creator/scripts/package_skill.py /path/to/coolify-manager [output-dir]
```

This automatically validates:
- YAML frontmatter format and required fields
- Skill naming conventions and directory structure
- Description completeness
- File organization and resource references

### Modifying Reference Documentation

When updating references:
- **api_endpoints.md**: Add new endpoints as Coolify API evolves
- **cli_commands.md**: Update when CLI version changes (currently targets v1.0.3)
- **wordpress_fixes.md**: Add new WordPress troubleshooting patterns discovered in practice

Ensure SKILL.md references remain accurate when modifying reference file paths or organization.

## Critical Implementation Details

### WordPress .htaccess Syntax

**Correct PHP directive syntax** (CRITICAL - incorrect syntax breaks Apache):
```apache
php_value max_input_vars 3000          ✓ Space between directive and value
php_value max_input_vars = 3000#       ✗ No equals sign, no trailing characters
```

Reference: `references/wordpress_fixes.md` lines 46-57

### HTTP_AUTHORIZATION Header

WordPress REST API requires this rewrite rule in .htaccess:
```apache
RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]
```

This enables loopback requests for Site Health checks. Without it, REST API health checks fail even when the API works.

### CLI Installation Architecture

The install script handles platform detection with this mapping:
- Platform: `uname -s` (darwin → darwin, linux → linux)
- Architecture: `uname -m` (x86_64 → amd64, aarch64/arm64 → arm64)
- Download URL: `https://github.com/coollabsio/coolify-cli/releases/download/{version}/coolify-cli_{version}_{platform}_{arch}.tar.gz`

Install target: `~/.local/bin/coolify` (requires PATH configuration)

### Context Switching Pattern

For managing multiple Coolify instances:
```bash
coolify context list              # View all contexts
coolify context use staging       # Switch to staging
coolify deploy APP_UUID           # Deploy to staging
coolify context use production    # Switch back
```

Default context is selected on `context add` unless explicitly changed.

## Skill Validation

The skill must pass validation before packaging. Key requirements:
1. YAML frontmatter with `name` and `description` fields
2. No TODO markers in SKILL.md
3. All referenced files exist (`scripts/`, `references/`)
4. Scripts are executable (chmod +x)
5. Description explains WHEN to use the skill

## File Reference Paths

All references in SKILL.md use relative paths:
- Scripts: `scripts/install_coolify_cli.sh`, `scripts/check_health.sh`
- References: `references/api_endpoints.md`, `references/cli_commands.md`, `references/wordpress_fixes.md`

These paths are relative to the skill root directory.

## Coolify-Specific Knowledge

### Service Status Indicators
- `running:healthy` - Service operational
- `running:unhealthy` - Service has issues (check logs)
- `stopped` - Not running
- `deploying` - Deployment in progress

### REST API False Positives
WordPress Site Health often reports REST API unavailable when it's actually working. Test with:
```bash
curl https://site.com/wp-json/
```

If JSON returns, it's a false positive - the Site Health loopback test is blocked, not the API itself.

### SSL Auto-Renewal
Coolify uses Traefik with Let's Encrypt. Certificates auto-renew. Check labels:
```yaml
traefik.http.routers.https-*.tls.certresolver=letsencrypt
traefik.http.routers.https-*.tls=true
```

## Writing Style

When modifying SKILL.md, maintain imperative/infinitive form (verb-first instructions):
- ✓ "To accomplish X, do Y"
- ✓ "Check the service status"
- ✗ "You should check the service status"
- ✗ "If you need to check X"

This maintains consistency for AI consumption per Claude Skills specification.
