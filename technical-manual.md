# Starbike Technical Manual
## Installation, Configuration & Deployment Guide

**Version:** 1.0  
**Last Updated:** December 2025  
**Project:** Starbike E-Bike and Motorcycle Rental System

---

## Table of Contents

1. [System Overview](#system-overview)
2. [Prerequisites](#prerequisites)
3. [System Architecture](#system-architecture)
4. [Installation Steps](#installation-steps)
5. [Firebase Configuration](#firebase-configuration)
6. [Backend Setup (Laravel API)](#backend-setup-laravel-api)
7. [Frontend Setup (React + Vite)](#frontend-setup-react--vite)
8. [Environment Configuration](#environment-configuration)
9. [Database Setup](#database-setup)
10. [Running the Application](#running-the-application)
11. [Testing](#testing)
12. [Deployment](#deployment)
13. [Security Considerations](#security-considerations)
14. [Troubleshooting](#troubleshooting)
15. [Maintenance & Updates](#maintenance--updates)

---

## System Overview

Starbike is a full-stack web application that provides:
- **Frontend**: React 19 with Vite, Tailwind CSS, and React Router
- **Backend**: Laravel 12 REST API for user management
- **Authentication**: Firebase Authentication with role-based access control
- **Database**: Cloud Firestore (primary) and SQLite/MySQL (Laravel API)
- **Storage**: Firebase Storage for images and documents
- **Real-time Features**: Firestore real-time listeners for notifications and status updates

### Repository Structure

```
Starbike/
├── starbike-frontend/           # React frontend application
│   ├── src/
│   │   ├── components/         # Reusable components
│   │   ├── pages/              # Route pages (user/admin/staff)
│   │   ├── context/            # React context providers
│   │   ├── firebase/           # Firebase configuration
│   │   ├── services/           # API services
│   │   └── utils/              # Utility functions
│   ├── public/                 # Static assets
│   └── dist/                   # Production build output
│   └── user-management-api/    # Laravel API
│       ├── app/                # Application code
│       ├── database/           # Migrations & seeders
│       ├── routes/             # API routes
│       └── public/             # Public entry point
├── functions/                   # Firebase Cloud Functions (optional)
├── docs/                       # Documentation
└── firebase.json               # Firebase configuration
```

---

## Prerequisites

### Required Software

| Software | Version | Purpose |
|----------|---------|---------|
| Node.js | 18.x or higher | Frontend development and build |
| npm or pnpm | Latest | Package management |
| PHP | 8.2 or higher | Backend API |
| Composer | 2.x | PHP dependency management |
| SQLite | 3.x | Default database (or MySQL/PostgreSQL) |
| Git | Latest | Version control |

### Firebase Requirements

- Active Firebase project
- Enabled services:
  - Firebase Authentication (Email/Password provider)
  - Cloud Firestore Database
  - Firebase Storage
  - (Optional) Firebase Cloud Functions
  - (Optional) Firebase Hosting

### System Requirements

- **OS**: Windows 10/11, macOS 10.15+, or Linux (Ubuntu 20.04+)
- **RAM**: 4GB minimum, 8GB recommended
- **Disk Space**: 2GB free space
- **Browser**: Chrome, Firefox, Safari, or Edge (latest versions)

### Development Tools (Optional but Recommended)

- VS Code or your preferred IDE
- Postman or similar API testing tool
- Git GUI client (GitHub Desktop, SourceTree, etc.)
- Browser DevTools extensions (React Developer Tools)

---

## System Architecture

### High-Level Architecture

```
┌─────────────────┐         ┌──────────────────┐
│   React Frontend │◄───────►│  Firebase Auth   │
│   (Port 5173)    │         │  & Firestore     │
└────────┬─────────┘         └──────────────────┘
         │
         │ REST API
         ▼
┌─────────────────┐         ┌──────────────────┐
│  Laravel API    │◄───────►│ SQLite/MySQL DB  │
│  (Port 8000)    │         │                  │
└─────────────────┘         └──────────────────┘
```

### User Roles & Access Control

- **Customer**: Browse vehicles, create bookings, track status
- **Staff/Technician/Manager**: Process requests, manage vehicles/services
- **Admin**: Full system access, user management, reports

---

## Installation Steps

### Step 1: Clone the Repository

```bash
# Clone the repository
git clone <your-repository-url>
cd Starbike

# Verify project structure
ls -la
```

### Step 2: Verify Prerequisites

```bash
# Check Node.js version
node --version  # Should be 18.x or higher

# Check npm version
npm --version

# Check PHP version
php --version  # Should be 8.2 or higher

# Check Composer version
composer --version

# Check SQLite installation
sqlite3 --version
```

---

## Firebase Configuration

### Step 1: Create Firebase Project

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Click **"Add project"** and follow the wizard
3. Project name: `starbike` (or your preferred name)
4. Enable Google Analytics (optional)

### Step 2: Enable Firebase Services

#### Enable Authentication

1. In Firebase Console, navigate to **Authentication**
2. Click **"Get started"**
3. Under **Sign-in method**, enable:
   - **Email/Password** (required)
4. Save changes

#### Configure Firestore Database

1. Navigate to **Firestore Database**
2. Click **"Create database"**
3. Choose **Production mode** or **Test mode** (start with Test mode for development)
4. Select a location (choose closest to your users)
5. Create the following collections (will be auto-created on first use):
   - `users` - User profiles
   - `motorcycles` - Vehicle inventory
   - `bookings` - Rental bookings
   - `services` - Service offerings
   - `notifications` - User notifications

#### Enable Firebase Storage

1. Navigate to **Storage**
2. Click **"Get started"**
3. Choose security rules (start with Test mode)
4. Note the Storage bucket URL

### Step 3: Configure Firestore Security Rules

In Firebase Console → Firestore Database → Rules, set:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Allow authenticated users to read/write their own data
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    
    // Allow all authenticated users to read motorcycles
    match /motorcycles/{motorcycleId} {
      allow read: if request.auth != null;
      allow write: if request.auth != null && 
                      get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role in ['admin', 'staff'];
    }
    
    // Bookings accessible by owner and staff/admin
    match /bookings/{bookingId} {
      allow read: if request.auth != null;
      allow create: if request.auth != null;
      allow update, delete: if request.auth != null && 
                                (resource.data.userId == request.auth.uid || 
                                 get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role in ['admin', 'staff']);
    }
  }
}
```

### Step 4: Get Firebase Configuration

1. In Firebase Console, go to **Project Settings** (gear icon)
2. Scroll to **"Your apps"** section
3. Click **"Web"** icon (</>) to add a web app
4. Register app name: `Starbike Frontend`
5. Copy the `firebaseConfig` object

### Step 5: Configure Frontend

Navigate to `starbike-frontend/src/firebase/config.js` and update with your Firebase config:

```javascript
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT_ID.appspot.com",
  messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
  appId: "YOUR_APP_ID",
  measurementId: "YOUR_MEASUREMENT_ID"
};
```

**⚠️ Security Note**: For production, use environment variables instead of hardcoding credentials.

---

## Backend Setup (Laravel API)

### Step 1: Navigate to Laravel Directory

```bash
cd starbike-frontend/user-management-api
```

### Step 2: Install PHP Dependencies

```bash
composer install
```

If you encounter memory issues:

```bash
COMPOSER_MEMORY_LIMIT=-1 composer install
```

### Step 3: Environment Configuration

```bash
# Copy environment file
cp .env.example .env

# Generate application key
php artisan key:generate
```

### Step 4: Configure Database

Edit `.env` file:

#### Option A: SQLite (Default, Recommended for Development)

```env
DB_CONNECTION=sqlite
DB_DATABASE=/absolute/path/to/starbike-frontend/user-management-api/database/database.sqlite
```

Create the database file:

```bash
# Linux/Mac
touch database/database.sqlite

# Windows (PowerShell)
New-Item database/database.sqlite -ItemType File
```

#### Option B: MySQL

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=starbike_db
DB_USERNAME=your_username
DB_PASSWORD=your_password
```

Create the MySQL database:

```sql
CREATE DATABASE starbike_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

#### Option C: PostgreSQL

```env
DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=starbike_db
DB_USERNAME=your_username
DB_PASSWORD=your_password
```

### Step 5: Run Migrations

```bash
# Run database migrations
php artisan migrate

# (Optional) Seed sample data
php artisan db:seed
```

### Step 6: Configure CORS

Edit `config/cors.php` or use the built-in CORS middleware settings:

```php
'allowed_origins' => ['http://localhost:5173', 'http://127.0.0.1:5173'],
'allowed_methods' => ['*'],
'allowed_headers' => ['*'],
'exposed_headers' => [],
'max_age' => 0,
'supports_credentials' => false,
```

For production, update `allowed_origins` with your production domain.

### Step 7: Test API Installation

```bash
# Start Laravel development server
php artisan serve
```

API will be available at: `http://localhost:8000`

Test endpoints:
- `http://localhost:8000/api/users` - List users
- Import Postman collection from `postman/User_Management_API.postman_collection.json`

---

## Frontend Setup (React + Vite)

### Step 1: Navigate to Frontend Directory

```bash
cd starbike-frontend
```

### Step 2: Install Dependencies

Using npm:

```bash
npm install
```

Using pnpm (faster alternative):

```bash
pnpm install
```

### Step 3: Verify Firebase Configuration

Ensure `src/firebase/config.js` has your Firebase credentials from earlier step.

### Step 4: Start Development Server

```bash
# Using npm
npm run dev

# Using pnpm
pnpm dev
```

The application will start on: `http://localhost:5173`

### Step 5: Build for Production

```bash
# Create optimized production build
npm run build

# Preview production build locally
npm run preview
```

Production files will be in the `dist/` directory.

---

## Environment Configuration

### Frontend Environment Variables

Create `.env` file in `starbike-frontend/`:

```env
VITE_API_URL=http://localhost:8000/api
VITE_APP_NAME=Starbike
```

### Backend Environment Variables

Key settings in `starbike-frontend/user-management-api/.env`:

```env
APP_NAME=Starbike
APP_ENV=local
APP_DEBUG=true
APP_URL=http://localhost:8000

# Database (see Database Setup section)
DB_CONNECTION=sqlite

# CORS
CORS_ALLOWED_ORIGINS=http://localhost:5173

# Logging
LOG_CHANNEL=stack
LOG_LEVEL=debug
```

---

## Database Setup

### Firestore Collections Structure

#### users
```json
{
  "uid": "firebase_uid",
  "email": "user@example.com",
  "displayName": "John Doe",
  "role": "customer|staff|admin",
  "phoneNumber": "+1234567890",
  "address": "123 Main St",
  "createdAt": "timestamp",
  "updatedAt": "timestamp"
}
```

#### motorcycles
```json
{
  "id": "motorcycle_id",
  "brand": "Honda",
  "model": "CBR500R",
  "type": "motorcycle|ebike",
  "status": "available|rented|maintenance",
  "pricePerDay": 50,
  "imageUrl": "storage_url",
  "specifications": {}
}
```

#### bookings
```json
{
  "id": "booking_id",
  "userId": "user_id",
  "motorcycleId": "motorcycle_id",
  "startDate": "timestamp",
  "endDate": "timestamp",
  "status": "pending|confirmed|completed|cancelled",
  "totalPrice": 150,
  "createdAt": "timestamp"
}
```

### Laravel Database Tables

Tables created by migrations:
- `users` - User management records
- `password_reset_tokens` - Password reset tokens
- `sessions` - User sessions (if using database sessions)

---

## Running the Application

### Development Mode

#### Option 1: Run Separately (Recommended for Debugging)

**Terminal 1 - Backend:**
```bash
cd starbike-frontend/user-management-api
php artisan serve
```

**Terminal 2 - Frontend:**
```bash
cd starbike-frontend
npm run dev
```

#### Option 2: Using Concurrent Runners

Install concurrently (if not already installed):
```bash
npm install -g concurrently
```

Create a start script in project root `package.json`:
```json
{
  "scripts": {
    "dev": "concurrently \"cd starbike-frontend/user-management-api && php artisan serve\" \"cd starbike-frontend && npm run dev\"",
    "dev:frontend": "cd starbike-frontend && npm run dev",
    "dev:backend": "cd starbike-frontend/user-management-api && php artisan serve"
  }
}
```

Then run:
```bash
npm run dev
```

### Access the Application

- **Frontend**: http://localhost:5173
- **Backend API**: http://localhost:8000
- **API Documentation**: http://localhost:8000/api/documentation (if configured)

### Default User Roles

After initial setup, create users with roles in Firebase:

1. Register a user through the app
2. In Firebase Console → Authentication, note the UID
3. In Firestore → users collection, create document with UID and add `role` field:
   - `customer` - Regular users
   - `staff` - Staff members
   - `admin` - Administrators

---

## Testing

### Frontend Testing

```bash
cd starbike-frontend

# Run linter
npm run lint

# Fix linting issues automatically
npm run lint -- --fix
```

### Backend Testing

```bash
cd starbike-frontend/user-management-api

# Run PHPUnit tests
php artisan test

# Run specific test
php artisan test --filter UserTest

# Generate code coverage report
php artisan test --coverage
```

### API Testing with Postman

1. Import collection: `user-management-api/postman/User_Management_API.postman_collection.json`
2. Create environment with variable `base_url` = `http://localhost:8000`
3. Test all endpoints

### Manual Testing Checklist

- [ ] User registration and login
- [ ] Password reset flow
- [ ] Customer dashboard access
- [ ] Staff dashboard access
- [ ] Admin dashboard access
- [ ] Vehicle browsing and booking
- [ ] Booking status updates
- [ ] Notifications display
- [ ] Profile management
- [ ] Role-based access control

---

## Deployment

### Frontend Deployment

#### Option 1: Vercel

```bash
# Install Vercel CLI
npm install -g vercel

# Deploy
cd starbike-frontend
vercel
```

Follow the prompts and configure:
- Framework: Vite
- Build command: `npm run build`
- Output directory: `dist`

#### Option 2: Netlify

```bash
# Build the project
npm run build

# Deploy dist folder to Netlify
# Or connect GitHub repo in Netlify dashboard
```

Netlify settings:
- Build command: `npm run build`
- Publish directory: `dist`
- Add environment variables for Firebase config

#### Option 3: Firebase Hosting

```bash
# Install Firebase CLI
npm install -g firebase-tools

# Login
firebase login

# Initialize hosting
firebase init hosting

# Build and deploy
npm run build
firebase deploy --only hosting
```

### Backend Deployment

#### Option 1: Traditional VPS (Nginx + PHP-FPM)

**1. Server Requirements:**
- Ubuntu 20.04+ or similar
- Nginx or Apache
- PHP 8.2+ with required extensions
- Composer
- SSL certificate (Let's Encrypt recommended)

**2. Deploy Steps:**

```bash
# On server, clone repository
git clone <repository-url> /var/www/starbike-api

# Navigate to API directory
cd /var/www/starbike-api/starbike-frontend/user-management-api

# Install dependencies
composer install --no-dev --optimize-autoloader

# Configure environment
cp .env.example .env
nano .env  # Update for production

# Set production values
APP_ENV=production
APP_DEBUG=false
APP_URL=https://api.yourdomain.com

# Generate key and run migrations
php artisan key:generate
php artisan migrate --force

# Optimize
php artisan config:cache
php artisan route:cache
php artisan view:cache

# Set permissions
sudo chown -R www-data:www-data storage bootstrap/cache
sudo chmod -R 775 storage bootstrap/cache
```

**3. Nginx Configuration:**

Create `/etc/nginx/sites-available/starbike-api`:

```nginx
server {
    listen 80;
    server_name api.yourdomain.com;
    root /var/www/starbike-api/starbike-frontend/user-management-api/public;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";

    index index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.2-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

Enable site and restart Nginx:

```bash
sudo ln -s /etc/nginx/sites-available/starbike-api /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

**4. SSL Certificate:**

```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d api.yourdomain.com
```

#### Option 2: Railway.app

1. Create account at [Railway.app](https://railway.app)
2. Connect GitHub repository
3. Configure:
   - Root directory: `starbike-frontend/user-management-api`
   - Start command: `php artisan serve --host=0.0.0.0 --port=$PORT`
4. Add environment variables from `.env`
5. Deploy

#### Option 3: Heroku

```bash
# Install Heroku CLI
curl https://cli-assets.heroku.com/install.sh | sh

# Login
heroku login

# Create app
heroku create starbike-api

# Add buildpack
heroku buildpacks:set heroku/php

# Set environment variables
heroku config:set APP_KEY=$(php artisan key:generate --show)
heroku config:set APP_ENV=production
heroku config:set APP_DEBUG=false

# Deploy
git subtree push --prefix starbike-frontend/user-management-api heroku main

# Run migrations
heroku run php artisan migrate --force
```

### Post-Deployment Checklist

- [ ] Update Firebase config with production URLs
- [ ] Configure CORS for production domains
- [ ] Set up SSL certificates
- [ ] Configure production database
- [ ] Set environment variables
- [ ] Test all authentication flows
- [ ] Verify API endpoints
- [ ] Check error logging
- [ ] Set up monitoring (optional)
- [ ] Configure backups
- [ ] Update DNS records

---

## Security Considerations

### Frontend Security

1. **Environment Variables**: Never commit Firebase config or API keys
2. **Authentication**: Always verify user roles before rendering protected content
3. **Input Validation**: Sanitize all user inputs
4. **XSS Prevention**: Use React's built-in escaping
5. **HTTPS**: Always use HTTPS in production

### Backend Security

1. **Environment**: Set `APP_DEBUG=false` in production
2. **CORS**: Restrict to known origins only
3. **Rate Limiting**: Configure API rate limits
4. **SQL Injection**: Use Laravel's query builder or Eloquent ORM
5. **Authentication**: Implement proper token validation
6. **File Permissions**: Ensure correct permissions on storage directories

### Firebase Security

1. **Security Rules**: Implement strict Firestore and Storage security rules
2. **API Keys**: Restrict Firebase API keys to specific domains
3. **Authentication**: Enable email verification
4. **Rate Limiting**: Monitor and limit authentication attempts

### Best Practices

- Keep all dependencies updated
- Regular security audits
- Implement logging and monitoring
- Regular database backups
- Use secrets management (e.g., AWS Secrets Manager, HashiCorp Vault)
- Implement CSP (Content Security Policy) headers

---

## Troubleshooting

### Common Issues and Solutions

#### Issue 1: Frontend Can't Connect to Backend

**Symptoms**: CORS errors, API requests fail

**Solutions**:
```bash
# 1. Check if backend is running
curl http://localhost:8000/api/users

# 2. Verify CORS configuration in Laravel
# Edit config/cors.php and add frontend URL

# 3. Clear Laravel config cache
cd starbike-frontend/user-management-api
php artisan config:clear
php artisan cache:clear
```

#### Issue 2: Firebase Authentication Loops

**Symptoms**: User logs in but gets redirected to login repeatedly

**Solutions**:
1. Verify Firebase config is correct in `src/firebase/config.js`
2. Check that user role is set in Firestore `users` collection
3. Clear browser cache and cookies
4. Check browser console for specific errors

```javascript
// Debug authentication state
import { auth } from './firebase/config';
auth.onAuthStateChanged((user) => {
  console.log('Auth state:', user);
  // Check user claims
  user?.getIdTokenResult().then(token => {
    console.log('Claims:', token.claims);
  });
});
```

#### Issue 3: Database Migration Errors

**Symptoms**: Migration fails, table already exists errors

**Solutions**:
```bash
# Reset database (⚠️ Warning: Deletes all data)
php artisan migrate:fresh

# Rollback last migration
php artisan migrate:rollback

# Check migration status
php artisan migrate:status

# For SQLite permission errors (Linux/Mac)
chmod 664 database/database.sqlite
chmod 775 database/
```

#### Issue 4: npm install Failures

**Symptoms**: Dependency installation errors

**Solutions**:
```bash
# Clear npm cache
npm cache clean --force

# Delete node_modules and lock file
rm -rf node_modules package-lock.json

# Reinstall
npm install

# Or try pnpm
npm install -g pnpm
pnpm install
```

#### Issue 5: Port Already in Use

**Symptoms**: "Port 5173/8000 already in use"

**Solutions**:
```bash
# Find process using port (Linux/Mac)
lsof -ti:5173 | xargs kill -9
lsof -ti:8000 | xargs kill -9

# Windows PowerShell
netstat -ano | findstr :5173
taskkill /PID <PID> /F

# Or use different ports
npm run dev -- --port 3000
php artisan serve --port=8001
```

#### Issue 6: Firestore Permission Denied

**Symptoms**: "Missing or insufficient permissions"

**Solutions**:
1. Check Firestore security rules in Firebase Console
2. For development, temporarily use test mode rules:
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if request.time < timestamp.date(2025, 12, 31);
    }
  }
}
```
3. Verify user is authenticated before Firestore operations
4. Check user has correct role claims

#### Issue 7: Build Errors

**Symptoms**: Production build fails

**Solutions**:
```bash
# Check for linting errors
npm run lint

# Clear Vite cache
rm -rf node_modules/.vite

# Check for TypeScript errors (if using TypeScript)
npx tsc --noEmit

# Try building with verbose output
npm run build -- --debug
```

### Getting Help

1. **Check Logs**:
   - Browser Console: F12 → Console tab
   - Laravel Logs: `storage/logs/laravel.log`
   - Vite Logs: Terminal output
   - Firebase Console: Firebase Console → Usage and billing → Usage

2. **Debug Mode**:
   ```bash
   # Laravel debug mode (development only!)
   APP_DEBUG=true
   
   # React DevTools: Install browser extension
   # Firebase Debug: Enable in browser console
   ```

3. **Community Support**:
   - Laravel Documentation: https://laravel.com/docs
   - React Documentation: https://react.dev
   - Firebase Documentation: https://firebase.google.com/docs
   - Stack Overflow: Tag questions appropriately

---

## Maintenance & Updates

### Regular Maintenance Tasks

#### Daily/Weekly
- Monitor error logs
- Check system performance
- Review user feedback
- Monitor Firebase usage and quotas

#### Monthly
- Update dependencies
- Review and optimize database
- Check security advisories
- Backup database

#### Quarterly
- Security audit
- Performance optimization
- Review and update documentation
- User training refreshers

### Updating Dependencies

#### Frontend Dependencies

```bash
cd starbike-frontend

# Check for outdated packages
npm outdated

# Update packages
npm update

# Update to latest (be cautious)
npm install package-name@latest

# Security audit
npm audit
npm audit fix
```

#### Backend Dependencies

```bash
cd starbike-frontend/user-management-api

# Check for outdated packages
composer outdated

# Update packages
composer update

# Security check (requires sensiolabs/security-checker)
composer require --dev sensiolabs/security-checker
./vendor/bin/security-checker security:check
```

### Backup Strategies

#### Database Backup

**SQLite:**
```bash
# Simple copy
cp database/database.sqlite database/backups/database-$(date +%Y%m%d).sqlite

# Automated daily backup (cron)
0 2 * * * cp /path/to/database.sqlite /path/to/backups/db-$(date +\%Y\%m\%d).sqlite
```

**MySQL:**
```bash
# Backup
mysqldump -u username -p starbike_db > backup-$(date +%Y%m%d).sql

# Restore
mysql -u username -p starbike_db < backup-20251222.sql
```

#### Firestore Backup

Use Firebase CLI or Cloud Scheduler:
```bash
# Export Firestore data
gcloud firestore export gs://your-bucket/backups/$(date +%Y%m%d)
```

#### Code Backup

- Use Git for version control
- Push regularly to remote repository
- Tag releases: `git tag -a v1.0.0 -m "Release 1.0.0"`

### Monitoring

#### Application Monitoring

- **Frontend**: Use Firebase Performance Monitoring or Google Analytics
- **Backend**: Laravel Telescope (development) or log monitoring
- **Uptime**: UptimeRobot, Pingdom, or StatusCake
- **Errors**: Sentry, Bugsnag, or Rollbar

#### Key Metrics to Monitor

- Response times
- Error rates
- User authentication success/failure
- API endpoint usage
- Database query performance
- Firebase quota usage

---

## Appendix

### Useful Commands Reference

#### Laravel Artisan

```bash
php artisan list                    # List all commands
php artisan make:controller Name    # Create controller
php artisan make:model Name         # Create model
php artisan make:migration name     # Create migration
php artisan migrate                 # Run migrations
php artisan migrate:rollback        # Rollback last migration
php artisan db:seed                 # Seed database
php artisan tinker                  # REPL
php artisan route:list              # List all routes
php artisan cache:clear             # Clear application cache
php artisan config:clear            # Clear config cache
php artisan view:clear              # Clear compiled views
```

#### Firebase CLI

```bash
firebase login                      # Login to Firebase
firebase init                       # Initialize Firebase project
firebase deploy                     # Deploy all
firebase deploy --only hosting      # Deploy hosting only
firebase deploy --only functions    # Deploy functions only
firebase serve                      # Serve locally
firebase firestore:indexes          # Deploy Firestore indexes
```

#### npm Scripts

```bash
npm run dev                         # Start dev server
npm run build                       # Build for production
npm run preview                     # Preview production build
npm run lint                        # Run linter
```

### Configuration Files Reference

#### package.json (Frontend)
- Dependencies and scripts
- Located: `starbike-frontend/package.json`

#### vite.config.js
- Vite build configuration
- Located: `starbike-frontend/vite.config.js`

#### tailwind.config.js
- Tailwind CSS configuration
- Located: `starbike-frontend/tailwind.config.js`

#### .env (Backend)
- Environment variables for Laravel
- Located: `starbike-frontend/user-management-api/.env`

#### firebase.json
- Firebase configuration
- Located: `firebase.json` (project root)

### Support & Contact

For technical support or questions:
- **Email**: support@starbike.com (if applicable)
- **Documentation**: Check this manual and user manual
- **Development Team**: Contact your project maintainer

---

**Document Version**: 1.0  
**Last Updated**: December 22, 2025  
**Maintained By**: Starbike Development Team
