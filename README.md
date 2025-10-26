# Coolify Manager

A comprehensive [Claude Skill](https://www.anthropic.com/news/skills) for managing and troubleshooting Coolify deployments. This skill extends Claude Code's capabilities with specialized knowledge for Coolify server management, WordPress troubleshooting, service diagnostics, and deployment operations.

## Features

- **🚀 Automated CLI Setup** - Install and configure the official Coolify CLI with platform detection
- **🏥 Health Diagnostics** - Quick health checks for services, containers, and connections
- **🔧 WordPress Troubleshooting** - Specialized workflows for common WordPress issues on Coolify
- **📊 Service Management** - Monitor, restart, and manage applications, services, and databases
- **🐳 Container Access** - Access container terminals and manage WordPress files
- **🔐 SSL Certificate Management** - Check and troubleshoot SSL certificates
- **📚 Comprehensive Documentation** - Complete API and CLI reference guides
- **🔄 Multi-Instance Support** - Manage multiple Coolify instances with context switching

## Installation

### For Claude Code Users

1. Download the `coolify-manager` skill
2. Place it in your Claude skills directory
3. Claude will automatically detect and load the skill

### Manual Installation

```bash
# Clone the repository
git clone https://github.com/ajmcclary/Coolify-Manager.git
cd Coolify-Manager

# Install the Coolify CLI
bash scripts/install_coolify_cli.sh

# Add to PATH (if not already)
export PATH="$HOME/.local/bin:$PATH"

# Configure your Coolify instance
coolify context add production https://your-coolify-instance.com YOUR_API_TOKEN

# Verify connection
bash scripts/check_health.sh
```

## Quick Start

### Prerequisites

- Access to a Coolify instance (self-hosted or cloud)
- API token from your Coolify dashboard at `/security/api-tokens`
- Coolify instance URL

### Basic Usage

```bash
# Check service status
coolify resource list

# Get service details
coolify service get SERVICE_UUID

# Check logs
coolify app logs APP_UUID

# Deploy an application
coolify deploy APP_UUID

# Restart a service
coolify service restart SERVICE_UUID
```

### WordPress Troubleshooting

Access your WordPress container:

```bash
# Via Coolify dashboard: Service → Terminal → Select "wordpress" container

# Check .htaccess
cd /var/www/html
cat .htaccess

# Fix PHP configuration
echo "php_value max_input_vars 3000" >> /var/www/html/.htaccess

# Test REST API
curl https://your-site.com/wp-json/
```

## What's Included

### Scripts

- **`install_coolify_cli.sh`** - Automated CLI installer with platform detection (macOS/Linux, x86_64/ARM64)
- **`check_health.sh`** - Comprehensive health check for CLI, contexts, connections, and resources

### References

- **`api_endpoints.md`** - Complete Coolify API reference with examples
- **`cli_commands.md`** - Full CLI command documentation with workflows
- **`wordpress_fixes.md`** - WordPress troubleshooting guide for common issues

### Documentation

- **`SKILL.md`** - Main skill definition with workflows and decision trees
- **`CLAUDE.md`** - Development guide for working with this skill
- **`README.md`** - This file

## Documentation Structure

```
coolify-manager/
├── README.md                      # Getting started guide
├── SKILL.md                       # Main skill documentation
├── CLAUDE.md                      # Development guide
├── scripts/
│   ├── install_coolify_cli.sh    # CLI installer
│   └── check_health.sh           # Health checker
└── references/
    ├── api_endpoints.md          # API reference
    ├── cli_commands.md           # CLI guide
    └── wordpress_fixes.md        # WordPress troubleshooting
```

## Common Tasks

### Diagnosing Service Issues

1. **Check Status**: `coolify resource list`
2. **Get Details**: `coolify service get UUID`
3. **Check Logs**: `coolify app logs APP_UUID`
4. **Fix Issue**: Based on logs and error messages
5. **Restart**: `coolify service restart SERVICE_UUID`
6. **Verify**: `coolify resource list`

### Fixing WordPress Problems

**Site down after .htaccess change:**
```bash
# Access container terminal (via Coolify dashboard)
cd /var/www/html
sed -i '$d' .htaccess  # Remove last line
```

**Increase PHP limits:**
```bash
echo "php_value max_input_vars 3000" >> /var/www/html/.htaccess
echo "php_value upload_max_filesize 64M" >> /var/www/html/.htaccess
```

**Check SSL certificate:**
```bash
echo | openssl s_client -servername your-site.com -connect your-site.com:443 2>/dev/null | openssl x509 -noout -dates
```

### Managing Multiple Environments

```bash
# List contexts
coolify context list

# Switch to staging
coolify context use staging
coolify deploy APP_UUID

# Switch back to production
coolify context use production
```

## Requirements

- **Coolify CLI**: v1.0.3+ (automatically installed via script)
- **Platform**: macOS (darwin) or Linux
- **Architecture**: x86_64 (amd64) or ARM64
- **Shell**: bash or zsh
- **Tools**: curl, tar (for installation)

## Coolify API Token

To obtain an API token:

1. Navigate to your Coolify dashboard
2. Go to `/security/api-tokens`
3. Create a new token with appropriate permissions:
   - Read access for status/logs
   - Write access for deployments/restarts
   - Deploy access for triggering deployments

## Troubleshooting

### CLI Not Found

Ensure `~/.local/bin` is in your PATH:

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

### Connection Failed

1. Verify API token is valid
2. Check Coolify instance URL is correct
3. Test manually:
   ```bash
   curl -H "Authorization: Bearer YOUR_TOKEN" https://your-instance.com/api/v1/version
   ```

### Service Unhealthy

1. Check service logs: `coolify app logs APP_UUID`
2. Access container terminal via Coolify dashboard
3. Check container-specific logs and configuration
4. Restart service: `coolify service restart SERVICE_UUID`

## Contributing

This skill was created to capture real-world Coolify management workflows. Contributions are welcome for:

- Additional troubleshooting patterns
- New CLI commands as Coolify evolves
- WordPress-specific fixes and solutions
- Documentation improvements

## License

MIT License - See repository for details

## Resources

- [Coolify Official Documentation](https://coolify.io/docs)
- [Coolify CLI GitHub](https://github.com/coollabsio/coolify-cli)
- [Coolify API Reference](https://coolify.io/docs/api-reference/api/)
- [Claude Skills](https://www.anthropic.com/news/skills)

## Credits

Created with [Claude Code](https://claude.com/claude-code) based on real-world Coolify management and troubleshooting workflows.

---

**Built for**: Coolify users managing VPS deployments
**Works with**: Coolify v4.0.0-beta.380+
**CLI Version**: 1.0.3
