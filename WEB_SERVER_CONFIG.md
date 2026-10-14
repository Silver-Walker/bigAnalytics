# Web Server Configuration for BIG Analytica
# This file provides configuration examples for different web servers

## Apache (.htaccess)
# Place this in your web root directory

RewriteEngine On

# Redirect admin requests to proper paths
RewriteRule ^admin/?$ admin/admin.html [L]
RewriteRule ^admin/dashboard/?$ admin/simple-dashboard.html [L]
RewriteRule ^admin/cms/?$ admin/cms.html [L]

# Enable compression
<IfModule mod_deflate.c>
    AddOutputFilterByType DEFLATE text/plain
    AddOutputFilterByType DEFLATE text/html
    AddOutputFilterByType DEFLATE text/xml
    AddOutputFilterByType DEFLATE text/css
    AddOutputFilterByType DEFLATE application/xml
    AddOutputFilterByType DEFLATE application/xhtml+xml
    AddOutputFilterByType DEFLATE application/rss+xml
    AddOutputFilterByType DEFLATE application/javascript
    AddOutputFilterByType DEFLATE application/x-javascript
</IfModule>

# Set cache headers
<IfModule mod_expires.c>
    ExpiresActive on
    ExpiresByType text/css "access plus 1 year"
    ExpiresByType application/javascript "access plus 1 year"
    ExpiresByType image/png "access plus 1 year"
    ExpiresByType image/jpg "access plus 1 year"
    ExpiresByType image/jpeg "access plus 1 year"
    ExpiresByType image/gif "access plus 1 year"
    ExpiresByType image/svg+xml "access plus 1 year"
</IfModule>

# Security headers
<IfModule mod_headers.c>
    Header always set X-Content-Type-Options nosniff
    Header always set X-Frame-Options DENY
    Header always set X-XSS-Protection "1; mode=block"
</IfModule>

## Nginx Configuration
# Add this to your nginx server block

server {
    listen 80;
    server_name your-domain.com;
    root /path/to/your/website;
    index index.html;

    # Admin redirects
    location /admin {
        try_files $uri $uri/ /admin/admin.html;
    }
    
    location /admin/dashboard {
        try_files $uri $uri/ /admin/simple-dashboard.html;
    }
    
    location /admin/cms {
        try_files $uri $uri/ /admin/cms.html;
    }

    # Enable gzip compression
    gzip on;
    gzip_vary on;
    gzip_min_length 1024;
    gzip_types text/plain text/css text/xml text/javascript application/javascript application/xml+rss application/json;

    # Cache static files
    location ~* \.(css|js|png|jpg|jpeg|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # Security headers
    add_header X-Content-Type-Options nosniff;
    add_header X-Frame-Options DENY;
    add_header X-XSS-Protection "1; mode=block";
}

## IIS Web.config
# Place this in your web root directory

<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <rewrite>
            <rules>
                <rule name="Admin Redirect" stopProcessing="true">
                    <match url="^admin/?$" />
                    <action type="Redirect" url="admin/admin.html" />
                </rule>
                <rule name="Dashboard Redirect" stopProcessing="true">
                    <match url="^admin/dashboard/?$" />
                    <action type="Redirect" url="admin/simple-dashboard.html" />
                </rule>
                <rule name="CMS Redirect" stopProcessing="true">
                    <match url="^admin/cms/?$" />
                    <action type="Redirect" url="admin/cms.html" />
                </rule>
            </rules>
        </rewrite>
        
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="365.00:00:00" />
        </staticContent>
        
        <httpCompression>
            <scheme name="gzip" dll="%Windir%\system32\inetsrv\gzip.dll" />
            <dynamicTypes>
                <add mimeType="text/*" enabled="true" />
                <add mimeType="message/*" enabled="true" />
                <add mimeType="application/javascript" enabled="true" />
                <add mimeType="application/json" enabled="true" />
            </dynamicTypes>
            <staticTypes>
                <add mimeType="text/*" enabled="true" />
                <add mimeType="message/*" enabled="true" />
                <add mimeType="application/javascript" enabled="true" />
                <add mimeType="application/json" enabled="true" />
            </staticTypes>
        </httpCompression>
        
        <httpProtocol>
            <customHeaders>
                <add name="X-Content-Type-Options" value="nosniff" />
                <add name="X-Frame-Options" value="DENY" />
                <add name="X-XSS-Protection" value="1; mode=block" />
            </customHeaders>
        </httpProtocol>
    </system.webServer>
</configuration>

## Deployment Instructions

### For Apache:
1. Upload all files to your web server
2. Place the .htaccess content in your web root
3. Ensure mod_rewrite is enabled
4. Access via: https://yourdomain.com/admin

### For Nginx:
1. Upload all files to your web server
2. Add the nginx configuration to your server block
3. Reload nginx configuration
4. Access via: https://yourdomain.com/admin

### For IIS:
1. Upload all files to your web server
2. Place the web.config file in your web root
3. Ensure URL Rewrite module is installed
4. Access via: https://yourdomain.com/admin

### For Simple Hosting (cPanel, etc.):
1. Upload all files to public_html
2. Access directly via: https://yourdomain.com/admin/admin.html
3. No additional configuration needed

## File Structure for Web Server
```
public_html/ (or web root)
├── index.html
├── admin.html
├── about.html
├── blog.html
├── contact.html
├── projects.html
├── eventscalendar.html
├── images/
├── admin/
│   ├── admin.html
│   ├── simple-dashboard.html
│   ├── cms.html
│   └── create-user.html
└── .htaccess (for Apache)
```

