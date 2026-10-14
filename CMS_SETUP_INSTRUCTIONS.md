# Web Server CMS Setup Instructions

## Overview
This website includes a standalone CMS system that works with any web server. The admin interface allows you to manage content through a user-friendly interface at `/admin`.

## Features
- **Content Management**: Edit text, images, and other content without touching code
- **Secure Authentication**: Username/password protected admin area
- **Real-time Updates**: Changes are automatically reflected on the website
- **Image Management**: Upload and manage images through the CMS interface
- **Blog Support**: Add and manage blog posts
- **Partner Management**: Easily update partner logos and information

## Setup Instructions

### 1. Upload to Web Server
1. Upload all files to your web server's public directory (public_html, www, etc.)
2. Ensure the file structure is maintained
3. Set proper file permissions (644 for files, 755 for directories)

### 2. Configure Web Server (Optional)
1. For Apache: The included .htaccess file will handle URL rewriting
2. For Nginx: Use the configuration provided in WEB_SERVER_CONFIG.md
3. For IIS: Use the web.config file provided in WEB_SERVER_CONFIG.md

### 3. Access the Admin Panel
1. Visit `https://your-site-name.com/admin`
2. Use the following credentials:
   - **Username**: `admin`
   - **Password**: `biganalytica2025` (change this in admin/admin.html)

### 4. Change Default Password
1. Open `admin/admin.html` in a text editor
2. Find the `ADMIN_CREDENTIALS` object
3. Change the password to something secure
4. Upload the updated file to your server

## Content Management

### Managing Homepage Content
1. Go to **CMS** in the admin dashboard
2. Use the **Homepage** tab to edit:
   - Hero headlines and subheadlines
   - Section titles
   - Platform section content
   - Strategy section content
   - Partnerships section content

### Managing Site Settings
1. Go to **CMS** in the admin dashboard
2. Use the **Site Settings** tab to update:
   - Site title and description
   - Contact information (phone, email, address)
   - Social media links

### Managing Strategic Areas
1. Go to **CMS** in the admin dashboard
2. Use the **Strategic Areas** tab to edit:
   - Area titles and descriptions
   - Icons and background images
   - Display order

### Managing Partnerships
1. Go to **CMS** in the admin dashboard
2. Use the **Partnerships** tab to update:
   - Counter numbers and descriptions
   - Partner logos and information

## File Structure
```
├── index.html              # Main website
├── admin.html              # Admin login (root level)
├── .htaccess              # Apache configuration
├── WEB_SERVER_CONFIG.md   # Server configuration guide
├── admin/
│   ├── admin.html         # Admin login page
│   ├── simple-dashboard.html # Admin dashboard
│   ├── cms.html           # Content management interface
│   └── create-user.html   # User creation interface
└── images/                # Website images
```

## Security Notes
- Change the default admin password immediately
- Use strong, unique passwords
- Regularly backup your website files
- Monitor admin access logs
- Consider using HTTPS for production

## Troubleshooting

### Admin Panel Not Loading
- Check file permissions (644 for files, 755 for directories)
- Verify all files are uploaded correctly
- Check web server error logs
- Ensure JavaScript is enabled in browser

### Content Not Updating
- Check browser cache
- Verify the CMS interface is working
- Check browser console for errors
- Ensure proper file permissions

### Authentication Issues
- Ensure you're using the correct credentials
- Check that admin/admin.html has the right password
- Try clearing browser cache and cookies
- Verify localStorage is enabled

## Deployment Options

### Shared Hosting (cPanel, etc.)
1. Upload files to public_html
2. Access via: `https://yourdomain.com/admin/admin.html`
3. No additional configuration needed

### VPS/Dedicated Server
1. Upload files to web root directory
2. Configure web server using provided configs
3. Set up SSL certificate
4. Access via: `https://yourdomain.com/admin`

### CDN Integration
1. Upload files to CDN
2. Configure CDN to serve HTML files
3. Set up proper caching rules
4. Access via CDN URL

## Support
For technical support or questions about the CMS setup, please contact your web developer or hosting provider.


