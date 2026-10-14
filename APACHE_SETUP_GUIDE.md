# Apache Server Setup Guide for BIG Analytica

## Overview
This guide will help you set up your BIG Analytica website on an Apache web server. The guide includes step-by-step instructions for different hosting scenarios.

## Prerequisites
- Apache web server (version 2.4 or higher recommended)
- Access to server configuration (or hosting control panel)
- FTP/SFTP access to upload files
- Domain name pointing to your server

## Step-by-Step Setup

### 1. Upload Files to Web Server

#### For Shared Hosting (cPanel, etc.):
```bash
# Upload these files to public_html directory:
├── index.html              # Main website
├── admin.html              # Admin login (root level)
├── .htaccess              # Apache configuration
├── about.html              # About page
├── blog.html               # Blog page
├── contact.html            # Contact page
├── projects.html           # Projects page
├── eventscalendar.html     # Events calendar
├── images/                 # Website images
└── admin/                  # Admin panel files
    ├── admin.html
    ├── simple-dashboard.html
    ├── cms.html
    └── create-user.html
```

#### For VPS/Dedicated Server:
```bash
# Upload files to web root directory (usually /var/www/html/ or /var/www/)
sudo cp -r /path/to/local/files/* /var/www/html/
sudo chown -R www-data:www-data /var/www/html/
sudo chmod -R 755 /var/www/html/
sudo chmod 644 /var/www/html/.htaccess
```

### 2. Configure Apache Modules

#### Check Required Modules:
```bash
# Check if required modules are enabled
sudo a2enmod rewrite
sudo a2enmod deflate
sudo a2enmod expires
sudo a2enmod headers

# Restart Apache
sudo systemctl restart apache2
```

#### For Shared Hosting:
- Most shared hosting providers have these modules enabled by default
- If not, contact your hosting provider to enable them

### 3. Configure Virtual Host (VPS/Dedicated Server)

#### Create Virtual Host Configuration:
```apache
# Create file: /etc/apache2/sites-available/biganalytica.conf
<VirtualHost *:80>
    ServerName yourdomain.com
    ServerAlias www.yourdomain.com
    DocumentRoot /var/www/html
    
    # Enable .htaccess overrides
    <Directory /var/www/html>
        AllowOverride All
        Require all granted
    </Directory>
    
    # Log files
    ErrorLog ${APACHE_LOG_DIR}/biganalytica_error.log
    CustomLog ${APACHE_LOG_DIR}/biganalytica_access.log combined
</VirtualHost>

# Enable SSL (if you have SSL certificate)
<VirtualHost *:443>
    ServerName yourdomain.com
    ServerAlias www.yourdomain.com
    DocumentRoot /var/www/html
    
    # SSL Configuration
    SSLEngine on
    SSLCertificateFile /path/to/your/certificate.crt
    SSLCertificateKeyFile /path/to/your/private.key
    
    # Enable .htaccess overrides
    <Directory /var/www/html>
        AllowOverride All
        Require all granted
    </Directory>
    
    # Log files
    ErrorLog ${APACHE_LOG_DIR}/biganalytica_ssl_error.log
    CustomLog ${APACHE_LOG_DIR}/biganalytica_ssl_access.log combined
</VirtualHost>
```

#### Enable the Site:
```bash
# Enable the site
sudo a2ensite biganalytica.conf

# Disable default site (optional)
sudo a2dissite 000-default.conf

# Restart Apache
sudo systemctl restart apache2
```

### 4. Configure SSL Certificate (Recommended)

#### Using Let's Encrypt (Free SSL):
```bash
# Install Certbot
sudo apt update
sudo apt install certbot python3-certbot-apache

# Get SSL certificate
sudo certbot --apache -d yourdomain.com -d www.yourdomain.com

# Auto-renewal (already configured by Certbot)
sudo certbot renew --dry-run
```

#### Using Commercial SSL Certificate:
1. Purchase SSL certificate from a provider
2. Generate CSR (Certificate Signing Request)
3. Install certificate files on server
4. Update virtual host configuration

### 5. Test Configuration

#### Check Apache Configuration:
```bash
# Test Apache configuration
sudo apache2ctl configtest

# Check if site is running
sudo systemctl status apache2
```

#### Test Website Access:
```bash
# Test from command line
curl -I http://yourdomain.com
curl -I https://yourdomain.com

# Test admin panel
curl -I http://yourdomain.com/admin
```

### 6. Configure Firewall (VPS/Dedicated Server)

#### UFW (Ubuntu):
```bash
# Allow HTTP and HTTPS
sudo ufw allow 'Apache Full'

# Check status
sudo ufw status
```

#### FirewallD (CentOS/RHEL):
```bash
# Allow HTTP and HTTPS
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload
```

## Configuration Options

### 1. Enable HTTPS Redirect
Uncomment these lines in .htaccess:
```apache
# Force HTTPS for security
RewriteCond %{HTTPS} off
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
```

### 2. Configure WWW Redirect
Choose one option in .htaccess:

#### Redirect www to non-www:
```apache
RewriteCond %{HTTP_HOST} ^www\.(.*)$ [NC]
RewriteRule ^(.*)$ https://%1/$1 [R=301,L]
```

#### Redirect non-www to www:
```apache
RewriteCond %{HTTP_HOST} !^www\. [NC]
RewriteRule ^(.*)$ https://www.%{HTTP_HOST}/$1 [R=301,L]
```

### 3. Custom Error Pages
Create custom error pages and uncomment in .htaccess:
```apache
ErrorDocument 404 /404.html
ErrorDocument 500 /500.html
ErrorDocument 403 /403.html
```

### 4. Performance Optimization

#### Enable Compression:
The .htaccess file already includes compression settings. Verify it's working:
```bash
# Test compression
curl -H "Accept-Encoding: gzip" -I http://yourdomain.com
```

#### Enable Caching:
The .htaccess file includes caching headers. Check browser developer tools to verify.

## Troubleshooting

### Common Issues

#### 1. 500 Internal Server Error
```bash
# Check Apache error logs
sudo tail -f /var/log/apache2/error.log

# Check .htaccess syntax
sudo apache2ctl configtest
```

#### 2. Admin Panel Not Accessible
- Verify .htaccess file is uploaded correctly
- Check file permissions (644 for .htaccess)
- Ensure mod_rewrite is enabled

#### 3. Images Not Loading
- Check file permissions (644 for files, 755 for directories)
- Verify image paths in HTML files
- Check Apache error logs

#### 4. SSL Certificate Issues
```bash
# Check SSL certificate
openssl s_client -connect yourdomain.com:443 -servername yourdomain.com

# Test SSL configuration
sudo apache2ctl -S
```

### Debug Commands

#### Check Apache Status:
```bash
# Check Apache status
sudo systemctl status apache2

# Check Apache configuration
sudo apache2ctl configtest

# List enabled modules
sudo apache2ctl -M

# Check virtual hosts
sudo apache2ctl -S
```

#### Check File Permissions:
```bash
# Check file permissions
ls -la /var/www/html/

# Fix permissions if needed
sudo chown -R www-data:www-data /var/www/html/
sudo chmod -R 755 /var/www/html/
sudo chmod 644 /var/www/html/.htaccess
```

## Security Considerations

### 1. File Permissions
```bash
# Set proper permissions
sudo chmod 644 /var/www/html/*.html
sudo chmod 644 /var/www/html/.htaccess
sudo chmod 755 /var/www/html/admin/
sudo chmod 644 /var/www/html/admin/*.html
```

### 2. Directory Listing
The .htaccess file includes protection against directory listing. Verify it's working by trying to access a directory without an index file.

### 3. Sensitive Files
The .htaccess file blocks access to:
- Configuration files (*.md)
- Backup files (*.bak)
- Temporary files (*.tmp)

### 4. Security Headers
The .htaccess file includes security headers:
- X-Content-Type-Options: nosniff
- X-Frame-Options: DENY
- X-XSS-Protection: 1; mode=block

## Monitoring and Maintenance

### 1. Log Monitoring
```bash
# Monitor access logs
sudo tail -f /var/log/apache2/access.log

# Monitor error logs
sudo tail -f /var/log/apache2/error.log

# Check for 404 errors
sudo grep "404" /var/log/apache2/access.log
```

### 2. Performance Monitoring
```bash
# Check Apache status
sudo apache2ctl status

# Monitor server resources
htop
df -h
free -h
```

### 3. Regular Updates
```bash
# Update system packages
sudo apt update && sudo apt upgrade

# Update Apache if needed
sudo apt upgrade apache2
```

## Backup Strategy

### 1. File Backup
```bash
# Create backup of website files
sudo tar -czf /backup/biganalytica-$(date +%Y%m%d).tar.gz /var/www/html/

# Automated backup script
#!/bin/bash
BACKUP_DIR="/backup"
WEB_DIR="/var/www/html"
DATE=$(date +%Y%m%d_%H%M%S)
tar -czf $BACKUP_DIR/biganalytica-$DATE.tar.gz $WEB_DIR
find $BACKUP_DIR -name "biganalytica-*.tar.gz" -mtime +7 -delete
```

### 2. Configuration Backup
```bash
# Backup Apache configuration
sudo cp -r /etc/apache2/ /backup/apache2-config-$(date +%Y%m%d)/
```

## Support and Resources

### Apache Documentation
- [Apache HTTP Server Documentation](https://httpd.apache.org/docs/)
- [Apache .htaccess Guide](https://httpd.apache.org/docs/current/howto/htaccess.html)

### Troubleshooting Resources
- [Apache Error Codes](https://httpd.apache.org/docs/current/mod/core.html#errordocument)
- [Apache Log Files](https://httpd.apache.org/docs/current/logs.html)

### Community Support
- [Apache HTTP Server Mailing Lists](https://httpd.apache.org/lists.html)
- [Stack Overflow Apache Tag](https://stackoverflow.com/questions/tagged/apache)

## Quick Reference

### Essential Commands
```bash
# Restart Apache
sudo systemctl restart apache2

# Check Apache status
sudo systemctl status apache2

# Test configuration
sudo apache2ctl configtest

# Enable site
sudo a2ensite sitename

# Disable site
sudo a2dissite sitename

# Enable module
sudo a2enmod modulename

# Disable module
sudo a2dismod modulename
```

### Important File Locations
- **Web Root**: `/var/www/html/`
- **Apache Config**: `/etc/apache2/`
- **Virtual Hosts**: `/etc/apache2/sites-available/`
- **Enabled Sites**: `/etc/apache2/sites-enabled/`
- **Modules**: `/etc/apache2/mods-available/`
- **Logs**: `/var/log/apache2/`

This guide should help you successfully deploy your BIG Analytica website on Apache. If you encounter any issues, refer to the troubleshooting section or contact your hosting provider.

