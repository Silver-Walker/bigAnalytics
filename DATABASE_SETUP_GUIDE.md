# Database Setup Guide for BIG Analytica CMS

## Overview
This guide will help you set up a secure database-driven authentication system for your CMS. The system uses Node.js with Express, bcrypt for password hashing, and JWT for secure authentication.

## Features
- 🔐 **Secure Authentication** - Passwords are hashed using bcrypt
- 🗄️ **JSON Database** - Simple file-based database for easy management
- 👥 **User Management** - Create and manage multiple admin users
- 🔑 **JWT Tokens** - Secure session management
- 🛡️ **Role-based Access** - Admin and Editor roles
- 📊 **User Dashboard** - Manage users through web interface

## Prerequisites
- Node.js (version 14 or higher)
- npm (Node Package Manager)

## Installation Steps

### 1. Install Dependencies
```bash
npm install
```

### 2. Initialize Database
```bash
npm run setup
```
This will create the initial database with a default admin user:
- **Username**: `admin`
- **Password**: `biganalytica2025`

### 3. Start the Server
```bash
npm start
```

For development with auto-restart:
```bash
npm run dev
```

## Access Points

### Admin Login
- **URL**: `http://localhost:3000/admin`
- **Username**: `admin`
- **Password**: `biganalytica2025`

### User Management
- **URL**: `http://localhost:3000/admin/users.html`
- **Features**: Create, view, and manage users

### CMS Interface
- **URL**: `http://localhost:3000/admin/`
- **Features**: Content management interface

## Database Structure

### Users Table
```json
{
  "users": [
    {
      "id": 1,
      "username": "admin",
      "password": "$2b$10$...", // bcrypt hashed
      "email": "admin@biganalytica.com",
      "role": "admin",
      "created_at": "2025-01-27T00:00:00Z",
      "last_login": "2025-01-27T10:30:00Z",
      "is_active": true
    }
  ],
  "sessions": []
}
```

### User Roles
- **admin**: Full access to all features including user management
- **editor**: Access to CMS content management only

## Security Features

### Password Security
- Passwords are hashed using bcrypt with salt rounds of 10
- Original passwords are never stored in the database
- Password comparison is done using bcrypt.compare()

### Session Management
- JWT tokens expire after 24 hours
- Tokens are stored in localStorage on the client
- Server-side session tracking for security

### Authentication Flow
1. User submits username/password
2. Server verifies credentials against database
3. If valid, server generates JWT token
4. Token is returned to client and stored
5. All subsequent requests include token in Authorization header

## API Endpoints

### Authentication
- `POST /api/login` - User login
- `POST /api/logout` - User logout
- `GET /api/verify` - Verify token validity

### User Management (Admin only)
- `GET /api/users` - Get all users
- `POST /api/users` - Create new user

## File Structure
```
├── data/
│   └── users.json          # Database file
├── admin/
│   ├── index.html          # CMS interface
│   ├── config.yml          # CMS configuration
│   └── users.html          # User management interface
├── content/                # CMS content files
├── js/
│   └── cms-integration.js  # CMS integration script
├── server.js               # Express server
├── setup.js                # Database initialization
├── package.json            # Dependencies
└── admin.html              # Admin login page
```

## Production Deployment

### Environment Variables
Set these environment variables for production:

```bash
JWT_SECRET=your-super-secure-secret-key-here
PORT=3000
```

### Security Recommendations
1. **Change Default Password**: Update the default admin password immediately
2. **Use Strong JWT Secret**: Generate a strong, random JWT secret
3. **Enable HTTPS**: Use SSL certificates in production
4. **Regular Backups**: Backup the `data/users.json` file regularly
5. **Monitor Logs**: Keep an eye on authentication logs

### Database Backup
```bash
# Backup database
cp data/users.json data/users.backup.$(date +%Y%m%d).json

# Restore database
cp data/users.backup.20250127.json data/users.json
```

## Troubleshooting

### Common Issues

#### "Cannot find module" errors
```bash
# Reinstall dependencies
rm -rf node_modules package-lock.json
npm install
```

#### Database not found
```bash
# Reinitialize database
npm run setup
```

#### Authentication fails
1. Check if server is running
2. Verify database file exists
3. Check browser console for errors
4. Ensure JWT secret is set

#### CMS not loading
1. Verify admin/config.yml is correct
2. Check if all content files exist
3. Ensure proper file permissions

### Logs and Debugging
- Server logs are displayed in the console
- Check browser developer tools for client-side errors
- Database file can be manually edited if needed

## User Management

### Creating New Users
1. Login as admin
2. Go to User Management (`/admin/users.html`)
3. Click "Add New User"
4. Fill in user details
5. Save user

### Changing Passwords
Currently, passwords can only be changed by editing the database file directly or by creating a new user. A password change feature can be added if needed.

### Deactivating Users
Set `is_active: false` in the user record in `data/users.json`

## Support
For technical support or questions about the database setup, please contact your web developer.

## Security Notice
⚠️ **Important**: This is a basic authentication system suitable for small to medium websites. For high-traffic or mission-critical applications, consider using a more robust database system like PostgreSQL or MongoDB with proper security measures.

