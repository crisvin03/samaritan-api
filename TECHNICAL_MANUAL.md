# Technical Manual - Installation Guide
## Samaritan Bayanihan Community Management System

**Version:** 1.0  
**Last Updated:** December 2025  
**Framework:** Laravel 12.x

---

## Table of Contents

1. [System Requirements](#system-requirements)
2. [Prerequisites](#prerequisites)
3. [Installation Steps](#installation-steps)
4. [Configuration](#configuration)
5. [Database Setup](#database-setup)
6. [Environment Variables](#environment-variables)
7. [Running the Application](#running-the-application)
8. [Production Deployment](#production-deployment)
9. [Troubleshooting](#troubleshooting)
10. [Maintenance](#maintenance)

---

## System Requirements

### Minimum Requirements

- **Operating System:** Windows 10/11, macOS 10.15+, or Linux (Ubuntu 20.04+)
- **PHP:** 8.2 or higher
- **Database:** MySQL 8.0+ or MariaDB 10.3+
- **Web Server:** Apache 2.4+ or Nginx 1.18+
- **Memory:** 2GB RAM minimum (4GB recommended)
- **Storage:** 500MB free space minimum

### Required PHP Extensions

- BCMath
- Ctype
- Fileinfo
- JSON
- Mbstring
- OpenSSL
- PDO
- Tokenizer
- XML
- GD or Imagick (for image processing)
- Zip (for file operations)

### Development Tools

- **Composer:** 2.0+ (PHP dependency manager)
- **Node.js:** 18.0+ (for asset compilation)
- **NPM:** 9.0+ (comes with Node.js)
- **Git:** 2.0+ (for version control)

---

## Prerequisites

### 1. Install PHP

**Windows:**
- Download PHP from [php.net](https://windows.php.net/download/)
- Or use XAMPP/WAMP which includes PHP, Apache, and MySQL

**macOS:**
```bash
brew install php@8.2
```

**Linux (Ubuntu/Debian):**
```bash
sudo apt update
sudo apt install php8.2 php8.2-cli php8.2-common php8.2-mysql php8.2-zip php8.2-gd php8.2-mbstring php8.2-curl php8.2-xml php8.2-bcmath
```

### 2. Install Composer

**Windows:**
- Download from [getcomposer.org](https://getcomposer.org/download/)
- Run the installer

**macOS/Linux:**
```bash
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer
```

Verify installation:
```bash
composer --version
```

### 3. Install Node.js and NPM

Download from [nodejs.org](https://nodejs.org/) (LTS version recommended)

Verify installation:
```bash
node --version
npm --version
```

### 4. Install MySQL/MariaDB

**Windows:**
- Download MySQL from [mysql.com](https://dev.mysql.com/downloads/installer/)
- Or use XAMPP/WAMP

**macOS:**
```bash
brew install mysql
```

**Linux (Ubuntu/Debian):**
```bash
sudo apt install mysql-server
```

### 5. Install Git

**Windows:** Download from [git-scm.com](https://git-scm.com/download/win)  
**macOS:** `brew install git`  
**Linux:** `sudo apt install git`

---

## Installation Steps

### Step 1: Clone the Repository

```bash
git clone https://github.com/crisvin03/samaritan-bayanihan.git
cd samaritan-bayanihan
```

Or download and extract the ZIP file to your desired directory.

### Step 2: Install PHP Dependencies

```bash
composer install
```

This will install all Laravel framework dependencies and packages.

**Note:** If you encounter memory issues, use:
```bash
php -d memory_limit=512M composer install
```

### Step 3: Install Node.js Dependencies

```bash
npm install
```

This installs frontend dependencies (Vite, Tailwind CSS, etc.).

### Step 4: Environment Configuration

Copy the environment example file:

```bash
cp .env.example .env
```

**Windows (PowerShell):**
```powershell
Copy-Item .env.example .env
```

### Step 5: Generate Application Key

```bash
php artisan key:generate
```

This generates a unique encryption key for your application.

### Step 6: Database Configuration

#### 6.1 Create Database

Log in to MySQL:
```bash
mysql -u root -p
```

Create the database:
```sql
CREATE DATABASE bayanihan_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
EXIT;
```

#### 6.2 Configure Database Connection

Edit the `.env` file and update the database settings:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=bayanihan_db
DB_USERNAME=root
DB_PASSWORD=your_password
```

Replace `your_password` with your MySQL root password (or create a dedicated database user).

#### 6.3 Run Migrations

```bash
php artisan migrate
```

This creates all necessary database tables.

#### 6.4 Seed Database (Optional)

Populate the database with initial data:

```bash
php artisan db:seed
```

This creates:
- Default admin user
- Sample barangays
- Sample roles
- Test data (if seeders are configured)

**Default Admin Credentials:**
- Email: Check the `AdminUserSeeder.php` file
- Password: Check the seeder file or reset using `php artisan tinker`

### Step 7: Configure File Storage

Create symbolic link for public storage:

```bash
php artisan storage:link
```

**Windows (if symbolic link fails):**
- Manually create a shortcut or configure your web server to serve files from `storage/app/public`

### Step 8: Compile Assets

For development:
```bash
npm run dev
```

For production:
```bash
npm run build
```

---

## Configuration

### Broadcasting Setup (Real-time Notifications)

The system uses Pusher for real-time notifications. Configure in `.env`:

```env
BROADCAST_CONNECTION=pusher

PUSHER_APP_ID=your_app_id
PUSHER_APP_KEY=your_app_key
PUSHER_APP_SECRET=your_app_secret
PUSHER_APP_CLUSTER=your_cluster
```

**To get Pusher credentials:**
1. Sign up at [pusher.com](https://pusher.com/)
2. Create a new app
3. Copy the credentials to your `.env` file

**For local development without Pusher:**
```env
BROADCAST_CONNECTION=log
```

### Mail Configuration

Configure email settings in `.env`:

```env
MAIL_MAILER=smtp
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USERNAME=your_email@gmail.com
MAIL_PASSWORD=your_app_password
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=noreply@bayanihan.com
MAIL_FROM_NAME="${APP_NAME}"
```

**For Gmail:**
- Enable 2-factor authentication
- Generate an app-specific password
- Use the app password in `MAIL_PASSWORD`

### File Storage Configuration

Default storage is local. For production, consider cloud storage (S3, etc.):

```env
FILESYSTEM_DISK=local
```

Update `config/filesystems.php` for cloud storage configuration.

---

## Database Setup

### Manual Database Import (Alternative)

If you have a SQL dump file:

```bash
mysql -u root -p bayanihan_db < database/bayanihan_db.sql
```

### Database Migrations

View migration status:
```bash
php artisan migrate:status
```

Rollback last migration:
```bash
php artisan migrate:rollback
```

Rollback all migrations:
```bash
php artisan migrate:reset
```

Fresh migration (drops all tables and re-runs):
```bash
php artisan migrate:fresh
php artisan db:seed
```

---

## Environment Variables

### Essential Variables

```env
# Application
APP_NAME="Samaritan Bayanihan"
APP_ENV=local
APP_KEY=base64:... (generated by key:generate)
APP_DEBUG=true
APP_URL=http://localhost:8000

# Database
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=bayanihan_db
DB_USERNAME=root
DB_PASSWORD=

# Broadcasting
BROADCAST_CONNECTION=pusher
PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_APP_CLUSTER=

# Cache & Session
CACHE_DRIVER=file
SESSION_DRIVER=file
QUEUE_CONNECTION=sync

# Mail
MAIL_MAILER=smtp
MAIL_HOST=
MAIL_PORT=
MAIL_USERNAME=
MAIL_PASSWORD=
```

### Security Variables

For production, ensure:
- `APP_DEBUG=false`
- `APP_ENV=production`
- Strong database passwords
- Secure `APP_KEY`

---

## Running the Application

### Development Server

Start the Laravel development server:

```bash
php artisan serve
```

The application will be available at `http://localhost:8000`

**With asset watching (for development):**
```bash
# Terminal 1: Laravel server
php artisan serve

# Terminal 2: Asset compilation (watch mode)
npm run dev
```

**Using Composer dev script (all-in-one):**
```bash
composer dev
```

This runs server, queue, logs, and Vite concurrently.

### Queue Worker (for background jobs)

If using queues:
```bash
php artisan queue:work
```

### Web Server Configuration

#### Apache Configuration

Create a virtual host in `httpd-vhosts.conf`:

```apache
<VirtualHost *:80>
    ServerName bayanihan.local
    DocumentRoot "C:/path/to/samaritan-bayanihan/public"
    
    <Directory "C:/path/to/samaritan-bayanihan/public">
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

Enable mod_rewrite and restart Apache.

#### Nginx Configuration

```nginx
server {
    listen 80;
    server_name bayanihan.local;
    root /path/to/samaritan-bayanihan/public;

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

---

## Production Deployment

### Pre-Deployment Checklist

- [ ] Set `APP_ENV=production`
- [ ] Set `APP_DEBUG=false`
- [ ] Update `APP_URL` with production domain
- [ ] Configure production database
- [ ] Set up SSL certificate (HTTPS)
- [ ] Configure mail settings
- [ ] Set up Pusher credentials
- [ ] Configure file storage
- [ ] Set up backup system
- [ ] Configure queue workers
- [ ] Set up monitoring

### Deployment Steps

1. **Pull latest code:**
```bash
git pull origin main
```

2. **Install/Update dependencies:**
```bash
composer install --optimize-autoloader --no-dev
npm install
npm run build
```

3. **Run migrations:**
```bash
php artisan migrate --force
```

4. **Optimize application:**
```bash
php artisan config:cache
php artisan route:cache
php artisan view:cache
php artisan event:cache
```

5. **Set permissions:**
```bash
chmod -R 755 storage bootstrap/cache
chown -R www-data:www-data storage bootstrap/cache
```

**Windows:** Ensure IIS_IUSRS has write permissions to `storage` and `bootstrap/cache`

6. **Set up queue worker (Supervisor recommended):**

Create `/etc/supervisor/conf.d/bayanihan-worker.conf`:

```ini
[program:bayanihan-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /path/to/samaritan-bayanihan/artisan queue:work --sleep=3 --tries=3 --max-time=3600
autostart=true
autorestart=true
stopasgroup=true
killasgroup=true
user=www-data
numprocs=2
redirect_stderr=true
stdout_logfile=/path/to/samaritan-bayanihan/storage/logs/worker.log
stopwaitsecs=3600
```

7. **Set up cron job for scheduler:**

Add to crontab (`crontab -e`):
```
* * * * * cd /path/to/samaritan-bayanihan && php artisan schedule:run >> /dev/null 2>&1
```

### Performance Optimization

- Enable OPcache in PHP
- Use Redis for cache and sessions
- Use database query caching
- Enable gzip compression
- Use CDN for static assets
- Optimize images
- Enable HTTP/2

---

## Troubleshooting

### Common Issues

#### 1. "Class not found" errors

**Solution:**
```bash
composer dump-autoload
php artisan config:clear
php artisan cache:clear
```

#### 2. Permission denied errors

**Linux/macOS:**
```bash
chmod -R 775 storage bootstrap/cache
chown -R www-data:www-data storage bootstrap/cache
```

**Windows:** Check folder permissions in Properties > Security

#### 3. Database connection errors

- Verify database credentials in `.env`
- Ensure MySQL service is running
- Check database exists
- Verify user has proper permissions

#### 4. Asset compilation errors

```bash
rm -rf node_modules package-lock.json
npm install
npm run build
```

#### 5. Storage link issues

**Windows:** Manually create shortcut or use:
```bash
php artisan storage:link --force
```

#### 6. Session/cache issues

```bash
php artisan cache:clear
php artisan config:clear
php artisan route:clear
php artisan view:clear
```

#### 7. Migration errors

```bash
php artisan migrate:fresh --seed
```

**Warning:** This deletes all data!

### Debug Mode

For development, enable debug mode in `.env`:
```env
APP_DEBUG=true
```

Check logs in `storage/logs/laravel.log`

### Getting Help

- Check Laravel documentation: [laravel.com/docs](https://laravel.com/docs)
- Review application logs: `storage/logs/laravel.log`
- Use Laravel Tinker for debugging:
```bash
php artisan tinker
```

---

## Maintenance

### Regular Tasks

1. **Backup database:**
```bash
mysqldump -u root -p bayanihan_db > backup_$(date +%Y%m%d).sql
```

2. **Clear old logs:**
```bash
php artisan log:clear
```

3. **Update dependencies:**
```bash
composer update
npm update
```

4. **Check for security updates:**
```bash
composer audit
npm audit
```

### Monitoring

- Monitor `storage/logs/laravel.log`
- Set up error tracking (Sentry, Bugsnag, etc.)
- Monitor server resources
- Set up database backups
- Monitor queue workers

### Updates

1. Backup database and files
2. Pull latest code
3. Run migrations: `php artisan migrate`
4. Update dependencies: `composer install && npm install && npm run build`
5. Clear caches: `php artisan optimize:clear && php artisan optimize`
6. Test thoroughly

---

## Additional Resources

- **Laravel Documentation:** [laravel.com/docs](https://laravel.com/docs)
- **Composer Documentation:** [getcomposer.org/doc](https://getcomposer.org/doc/)
- **MySQL Documentation:** [dev.mysql.com/doc](https://dev.mysql.com/doc/)
- **Tailwind CSS Documentation:** [tailwindcss.com/docs](https://tailwindcss.com/docs)

---

## Support

For technical support:
- Check application logs: `storage/logs/laravel.log`
- Review this manual
- Contact the development team

---

**Document Version:** 1.0  
**Last Updated:** December 2025

