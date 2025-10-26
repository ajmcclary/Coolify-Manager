# WordPress on Coolify: Common Issues and Fixes

## Table of Contents
1. [Accessing WordPress Container](#accessing-wordpress-container)
2. [.htaccess Issues](#htaccess-issues)
3. [PHP Configuration](#php-configuration)
4. [REST API Issues](#rest-api-issues)
5. [SSL Certificate Issues](#ssl-certificate-issues)

---

## Accessing WordPress Container

### Via Coolify Web Terminal
1. Navigate to your Coolify dashboard
2. Go to your WordPress service
3. Click **Terminal** in the sidebar
4. Select the **wordpress** container
5. You're now in the container shell

### Via Docker (if you have SSH access)
```bash
docker exec -it CONTAINER_NAME bash
```

### WordPress Files Location
```
/var/www/html/
```

---

## .htaccess Issues

### Symptom: Site is down after editing .htaccess

**Common causes:**
- Syntax errors in PHP directives
- Invalid Apache directives
- Malformed rewrite rules

### Quick Fix: View and Edit .htaccess
```bash
cd /var/www/html
cat .htaccess
```

### Remove Last Line (if it's problematic)
```bash
sed -i '$d' /var/www/html/.htaccess
```

### Restore Default WordPress .htaccess
```bash
cat > /var/www/html/.htaccess << 'EOF'
# BEGIN WordPress
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]
RewriteBase /
RewriteRule ^index\.php$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index.php [L]
</IfModule>
# END WordPress
EOF
```

---

## PHP Configuration

### Increasing PHP Limits via .htaccess

**Correct syntax** (note: space between value and number, no `=` sign):
```
php_value upload_max_filesize 64M
php_value post_max_size 128M
php_value memory_limit 256M
php_value max_execution_time 300
php_value max_input_time 300
php_value max_input_vars 3000
```

**Incorrect syntax** (will break Apache):
```
php_value max_input_vars = 3000#   ❌ Don't use = or trailing #
```

### Add PHP Settings to .htaccess
```bash
echo "php_value max_input_vars 3000" >> /var/www/html/.htaccess
```

### Verify Settings
```bash
tail -10 /var/www/html/.htaccess
```

---

## REST API Issues

### Symptom: REST API appears unavailable in Site Health

**Test if REST API is actually working:**
```bash
curl https://your-site.com/?rest_route=/
curl https://your-site.com/wp-json/
```

If JSON data returns, the REST API is working - it's a **false positive**.

### Common Causes of False Positives
1. **Site Health loopback test blocked** (the test itself fails, but API works)
2. **Authentication issues** - loopback endpoint requires auth
3. **Security plugins** blocking self-requests

### Check .htaccess for HTTP_AUTHORIZATION
Ensure this line exists in the WordPress section:
```apache
RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]
```

### Real API Issues
If REST API truly isn't working:
1. Check for security plugins (Wordfence, iThemes Security)
2. Check if REST API is disabled: Look for `disable_rest_api` option
3. Check firewall/CDN settings (Cloudflare, Sucuri)
4. Check server-level authentication

### Whitelist OptimizePress Routes
If using OptimizePress, whitelist these paths:
- `/wp-json/op3/v1/`
- `/wp-json/opf/v1`
- `/wp-json/opd/v1`
- `/wp-json/opc/v1`
- `/wp-json/opm/v1`

---

## SSL Certificate Issues

### Check SSL Certificate Status
```bash
echo | openssl s_client -servername your-site.com -connect your-site.com:443 2>/dev/null | openssl x509 -noout -dates -subject -issuer
```

### Verify Certificate Details
Look for:
- **Issuer**: Should be Let's Encrypt
- **Valid dates**: Check expiration
- **Subject**: Should match your domain

### Coolify Auto-SSL Configuration
Coolify uses Traefik with Let's Encrypt. Check these labels in your service:
```yaml
traefik.http.routers.https-*.tls.certresolver=letsencrypt
traefik.http.routers.https-*.tls=true
```

### Force SSL Renewal
If certificate is expired or invalid:
1. Go to Coolify dashboard
2. Navigate to your service
3. Click on the domain configuration
4. Regenerate SSL certificate

### Certificate Auto-Renewal
Coolify automatically renews Let's Encrypt certificates before expiration. No manual intervention needed unless there's an error.

---

## Container Restart Required?

After making changes to .htaccess or PHP configuration, you may need to restart:

### Via Coolify CLI
```bash
coolify service restart SERVICE_UUID
```

### Via Coolify Dashboard
1. Go to your service
2. Click "Restart" button

### Via API
```bash
curl -X POST \
  -H "Authorization: Bearer YOUR_TOKEN" \
  https://your-coolify-instance.com/api/v1/services/SERVICE_UUID/restart
```

---

## Debugging Workflow

When WordPress site is down:

1. **Check service status**
   ```bash
   coolify resource list
   ```

2. **Check logs**
   ```bash
   coolify app logs APP_UUID
   ```

3. **Access container terminal**
   - Via Coolify dashboard → Terminal
   - Or via `docker exec`

4. **Check .htaccess**
   ```bash
   cd /var/www/html
   cat .htaccess
   ```

5. **Test REST API**
   ```bash
   curl https://your-site.com/wp-json/
   ```

6. **Check SSL**
   ```bash
   curl -vI https://your-site.com 2>&1 | grep -i ssl
   ```

7. **Restart if needed**
   ```bash
   coolify service restart SERVICE_UUID
   ```
