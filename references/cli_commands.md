# Coolify CLI Commands Reference

Version: 1.0.3

## Installation

```bash
curl -fsSL https://raw.githubusercontent.com/coollabsio/coolify-cli/main/scripts/install.sh | bash
```

Or use the bundled script:
```bash
bash scripts/install_coolify_cli.sh
```

## Global Flags

Available with all commands:
- `--context <name>` - Use specific context by name
- `--format <type>` - Output format: table, json, or pretty (default: table)
- `--debug` - Enable verbose logging
- `-s, --show-sensitive` - Show sensitive information (passwords, tokens, IPs)
- `-f, --force` - Skip confirmation prompts

## Context Management

Manage multiple Coolify instances.

### Add Context
```bash
coolify context add <name> <url> <token>
```
Example:
```bash
coolify context add production https://coolify.example.com TOKEN
```

### Set Token for Cloud
```bash
coolify context set-token cloud <token>
```

### List Contexts
```bash
coolify context list
```

### Switch Context
```bash
coolify context use <name>
```

### Verify Connection
```bash
coolify context verify
```
Checks authentication and connection to current context.

### Check API Version
```bash
coolify context version
```

### Remove Context
```bash
coolify context remove <name>
```

## Servers

Manage Coolify servers.

### List Servers
```bash
coolify server list
coolify server list -s  # Show sensitive info (IPs, ports)
```

### Get Server Details
```bash
coolify server get <uuid>
coolify server get <uuid> -s  # Show sensitive info
```

### Add Server
```bash
coolify server add
```

### Validate Server
```bash
coolify server validate <uuid>
```

### Get Server Domains
```bash
coolify server domains <uuid>
```

### Remove Server
```bash
coolify server remove <uuid>
```

## Applications

Manage applications.

### List Applications
```bash
coolify app list
coolify app list --format json
```

### Get Application Details
```bash
coolify app get <uuid>
```

### Start/Stop/Restart Application
```bash
coolify app start <uuid>
coolify app stop <uuid>
coolify app restart <uuid>
```

### Get Application Logs
```bash
coolify app logs <uuid>
coolify app logs <uuid> --lines 500
```

### Update Application
```bash
coolify app update <uuid>
```

### Delete Application
```bash
coolify app delete <uuid>
coolify app delete <uuid> --force  # Skip confirmation
```

### Manage Environment Variables
```bash
coolify app env list <uuid>
coolify app env get <uuid> <key>
coolify app env set <uuid> <key> <value>
coolify app env delete <uuid> <key>
```

## Services

Manage one-click services (WordPress, Redis, PostgreSQL, etc.).

### List Services
```bash
coolify service list
coolify service list --format json
```

### Get Service Details
```bash
coolify service get <uuid>
```

### Start/Stop/Restart Service
```bash
coolify service start <uuid>
coolify service stop <uuid>
coolify service restart <uuid>
```

### Delete Service
```bash
coolify service delete <uuid>
```

## Databases

Manage databases.

### List Databases
```bash
coolify database list
```

### Get Database Details
```bash
coolify database get <uuid>
```

### Start/Stop/Restart Database
```bash
coolify database start <uuid>
coolify database stop <uuid>
coolify database restart <uuid>
```

### Backup Database
```bash
coolify database backup <uuid>
```

### List Backups
```bash
coolify database backups <uuid>
```

## Deployments

Manage deployments.

### Deploy Resource
```bash
coolify deploy <uuid>
```

### List Deployments
```bash
coolify deploy list
coolify deploy list <resource-uuid>  # Filter by resource
```

### Get Deployment Details
```bash
coolify deploy get <deployment-uuid>
```

## Projects

Manage projects and environments.

### List Projects
```bash
coolify project list
```

### Get Project Details
```bash
coolify project get <uuid>
```

### List Environments
```bash
coolify project envs <project-uuid>
```

## Resources

View all resources (applications, services, databases).

### List All Resources
```bash
coolify resource list
coolify resource list --format json
```

This shows all resources with:
- UUID
- Name
- Type (application/service/database)
- Status

## Teams

Manage teams and members.

### List Teams
```bash
coolify teams list
```

### Get Team Details
```bash
coolify teams get <id>
```

### List Team Members
```bash
coolify teams members <id>
```

## Private Keys

Manage SSH private keys for server access.

### List Private Keys
```bash
coolify private-key list
```

### Get Private Key
```bash
coolify private-key get <id>
```

### Add Private Key
```bash
coolify private-key add
```

### Delete Private Key
```bash
coolify private-key delete <id>
```

## GitHub Integration

Manage GitHub App connections.

### List GitHub Apps
```bash
coolify github list
```

### Get GitHub App Details
```bash
coolify github get <id>
```

## Configuration

### Show Config Location
```bash
coolify config
```

Displays the path to the configuration file (usually `~/.config/coolify/config.json`).

## Update CLI

### Check for Updates
```bash
coolify update
```

### Get Current Version
```bash
coolify version
```

## Common Workflows

### Initial Setup
```bash
# 1. Get API token from Coolify dashboard (/security/api-tokens)
# 2. Add context
coolify context add production https://your-instance.com YOUR_TOKEN

# 3. Verify connection
coolify context verify

# 4. List resources
coolify resource list
```

### Debug Service Issues
```bash
# 1. Check resource status
coolify resource list

# 2. Get service details
coolify service get SERVICE_UUID

# 3. Check logs
coolify app logs APP_UUID

# 4. Restart if needed
coolify service restart SERVICE_UUID
```

### Deploy Application
```bash
# 1. Get application UUID
coolify resource list

# 2. Deploy
coolify deploy APP_UUID

# 3. Monitor deployment
coolify deploy list APP_UUID
```

### Manage Environment Variables
```bash
# List all env vars
coolify app env list APP_UUID

# Add new env var
coolify app env set APP_UUID NEW_VAR "value"

# Update existing var
coolify app env set APP_UUID EXISTING_VAR "new value"

# Delete env var
coolify app env delete APP_UUID OLD_VAR

# Restart to apply changes
coolify app restart APP_UUID
```

## Output Formats

### Table (default)
```bash
coolify resource list
```
Human-readable table format.

### JSON
```bash
coolify resource list --format json
```
Machine-readable JSON for parsing with `jq`.

### Pretty JSON
```bash
coolify resource list --format pretty
```
Formatted JSON with indentation.

## Tips

1. **Use JSON format with jq** for filtering:
   ```bash
   coolify resource list --format json | jq '.[] | select(.status=="running")'
   ```

2. **Show sensitive info when debugging**:
   ```bash
   coolify server list -s
   ```

3. **Skip confirmations in scripts**:
   ```bash
   coolify app delete UUID --force
   ```

4. **Switch contexts easily**:
   ```bash
   coolify context use staging
   coolify deploy APP_UUID
   coolify context use production
   ```

5. **Debug with verbose output**:
   ```bash
   coolify --debug context verify
   ```
