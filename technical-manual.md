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
│   │   ├── services/           # Firebase services
│   │   └── utils/              # Utility functions
│   ├── public/                 # Static assets
│   └── dist/                   # Production build output
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
┌─────────────────────┐
│   React Frontend    │
│   (Port 5173)       │
│   Vite Dev Server   │
└──────────┬──────────┘
           │
           │ Firebase SDK
           ▼
┌──────────────────────┐
│   Firebase Services  │
├──────────────────────┤
│  • Authentication    │
│  • Firestore DB      │
│  • Storage           │
│  • Cloud Functions   │
└──────────────────────┘
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

# Check Git installation
git --version
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

Create `.env` file in `starbike-frontend/` (optional):

```env
VITE_APP_NAME=Starbike
VITE_FIREBASE_API_KEY=your_api_key
VITE_FIREBASE_AUTH_DOMAIN=your_auth_domain
VITE_FIREBASE_PROJECT_ID=your_project_id
```

**Note**: For security in production, consider using environment variables instead of hardcoding Firebase config. However, Firebase client-side keys are safe to expose as they're protected by Firebase security rules.

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

---

## Running the Application

### Development Mode

**Start the development server:**

```bash
cd starbike-frontend
npm run dev
```

The application will start at `http://localhost:5173`

### Access the Application

- **Frontend**: http://localhost:5173
- **Firebase Console**: https://console.firebase.google.com (for database/auth management)

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

### Firebase Testing

**Test Firebase Connection:**
```bash
cd starbike-frontend
# The app includes Firebase test utilities in src/utils/
# Run the dev server and check browser console for connection status
npm run dev
```

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


### Post-Deployment Checklist

- [ ] Update Firebase config with production URLs
- [ ] Configure Firebase security rules for production
- [ ] Set up SSL certificates (automatic with most hosts)
- [ ] Set environment variables (if using)
- [ ] Test all authentication flows
- [ ] Verify Firestore access
- [ ] Check error logging
- [ ] Set up monitoring (Firebase Analytics)
- [ ] Configure Firebase backups
- [ ] Update DNS records

---

## Security Considerations

### Frontend Security

1. **Environment Variables**: Never commit Firebase config or API keys
2. **Authentication**: Always verify user roles before rendering protected content
3. **Input Validation**: Sanitize all user inputs
4. **XSS Prevention**: Use React's built-in escaping
5. **HTTPS**: Always use HTTPS in production


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

#### Issue 1: Frontend Can't Connect to Firebase

**Symptoms**: Authentication fails, Firestore errors

**Solutions**:
```bash
# 1. Check Firebase config in src/firebase/config.js
# Verify all credentials are correct

# 2. Check Firebase Console
# - Authentication is enabled
# - Firestore is created
# - Security rules are configured

# 3. Check browser console for specific errors

# 4. Clear browser cache and cookies
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

#### Issue 3: Firestore Data Not Saving

**Symptoms**: Data doesn't persist, permission errors

**Solutions**:
1. **Check Security Rules**:
   - Go to Firebase Console → Firestore → Rules
   - Verify rules allow authenticated users to write
   
2. **Check Authentication**:
   - User must be logged in
   - Verify auth state in browser console
   
3. **Check Collection Names**:
   - Ensure correct collection names in code
   - Case-sensitive

4. **Check Browser Console**:
   - Look for specific Firestore errors
   - May indicate quota limits or network issues

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

**Symptoms**: "Port 5173 already in use"

**Solutions**:
```bash
# Find process using port (Linux/Mac)
lsof -ti:5173 | xargs kill -9

# Windows PowerShell
netstat -ano | findstr :5173
taskkill /PID <PID> /F

# Or use different port
npm run dev -- --port 3000
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

# Try building with verbose output
npm run build -- --debug

# Check for circular dependencies
npm list --depth=0
```

### Getting Help

1. **Check Logs**:
   - Browser Console: F12 → Console tab
   - Vite Logs: Terminal output
   - Firebase Console: Firebase Console → Usage and billing → Usage

2. **Debug Mode**:
   ```bash
   # React DevTools: Install browser extension
   # Firebase Debug: Enable in browser console
   localStorage.setItem('debug', 'firebase:*')
   ```

3. **Community Support**:
   - React Documentation: https://react.dev
   - Vite Documentation: https://vitejs.dev
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

### Backup Strategies

#### Firestore Backup

**Automated Backups** (Firebase Blaze plan):
1. Go to Firebase Console → Firestore → Backups
2. Enable automatic backups
3. Schedule: Daily, weekly, or monthly
4. Retention period: Configure as needed

**Manual Backup via Firebase CLI:**
```bash
# Install Firebase CLI
npm install -g firebase-tools

# Login
firebase login

# Export Firestore data
firebase firestore:export gs://your-bucket/backups/$(date +%Y%m%d)
```

**Import from Backup:**
```bash
# Restore Firestore data
firebase firestore:import gs://your-bucket/backups/20251222
```

#### Code Backup

- Use Git for version control
- Push regularly to remote repository
- Tag releases: `git tag -a v1.0.0 -m "Release 1.0.0"`

### Monitoring

#### Application Monitoring

- **Frontend**: Firebase Performance Monitoring
- **Analytics**: Firebase Analytics or Google Analytics
- **Uptime**: UptimeRobot, Pingdom, or StatusCake
- **Errors**: Sentry, Bugsnag, or Rollbar
- **Firebase**: Built-in monitoring in Firebase Console

#### Key Metrics to Monitor

- Page load times
- Error rates
- User authentication success/failure
- Firestore read/write operations
- Firebase Storage usage
- Firebase quota usage
- User engagement metrics

---

## Appendix

### Useful Commands Reference

#### npm Scripts

```bash
npm run dev                         # Start dev server
npm run build                       # Build for production
npm run preview                     # Preview production build
npm run lint                        # Run linter
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
