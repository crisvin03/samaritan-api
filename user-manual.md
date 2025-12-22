User Manual - How to Use

Starbike E-Bike and Motorcycle Rental System  
Version: 1.0  
Last Updated: December 2025

________________________________________

Table of Contents

1. Introduction
2. Getting Started
3. Member Guide
4. Staff Guide
5. Admin Guide
6. Common Features
7. Troubleshooting
8. FAQs
9. Getting Help

________________________________________

## Introduction

Starbike is a web-based platform designed to help manage e-bike and motorcycle rentals efficiently. The system provides role-based access for customers, staff, and administrators to streamline the rental process from booking to return.

### System Overview

The system supports three types of users:

• **Customers**: Community members who can browse vehicles, make bookings, and track rental status  
• **Staff**: Rental agents who process booking requests, manage vehicle inventory, and assist customers  
• **Admins**: System administrators with full access to manage users, vehicles, transactions, and system settings

### Key Features

• 🏍️ **Vehicle Booking**: Browse and book motorcycles and e-bikes  
• 📊 **Booking Management**: Track booking status from request to completion  
• 🔔 **Real-time Notifications**: Get instant updates on bookings and status changes  
• 💰 **Payment Tracking**: View transaction history and receipts  
• 👤 **Profile Management**: Update personal information and preferences  
• 📱 **Mobile Friendly**: Access from any device - desktop, tablet, or phone

### System Requirements

• Modern web browser (Chrome 90+, Firefox 88+, Safari 14+, Edge 90+)  
• Internet connection  
• JavaScript and cookies enabled  
• No software installation required

________________________________________

## Getting Started

### Quick Start Guide

**🚀 New Customer? Follow These Steps:**

1. **Create Account** (5 minutes)
   - Click "Register" → Fill in email, password, name
   - Verify email → Log in

2. **Browse Vehicles** (10 minutes)
   - Visit Book page
   - Filter by type, brand, price
   - View vehicle details

3. **Make Booking** (15 minutes)
   - Select vehicle → Choose dates
   - Review price → Confirm
   - Wait for approval

4. **Track Booking**
   - Check Status page
   - Monitor booking progress
   - Prepare for pickup

### Accessing the System

**Step 1: Open Your Browser**
- Launch Chrome, Firefox, Safari, or Edge
- Ensure you're using a recent version

**Step 2: Navigate to Starbike**
- Production: Enter your Starbike URL (provided by your organization)
- Development: Use `http://localhost:5173`
- 💡 Tip: Bookmark for quick access

**Step 3: Explore Public Pages**

Before logging in, you can browse:
- **Home** (`/`) - Landing page with featured vehicles
- **Services** (`/services`) - Available rental services
- **Book** (`/book`) - Vehicle catalog preview
- **About** (`/about`) - About the platform
- **Contact** (`/contact`) - Contact information

### Registration (For Customers)

**Step 1: Start Registration**
1. Click "Login" button in navigation
2. Select "Customer Login"
3. Click "Register" or "Create Account"

**Step 2: Fill Registration Form**

| Field | What to Enter | Example | Requirements |
|-------|---------------|---------|--------------|
| Email | Valid email address | john@email.com | Must be unique |
| Password | Strong password | MySecure!123 | 8+ chars, mixed case, numbers, symbols |
| Confirm Password | Same password | MySecure!123 | Must match |
| Full Name | Your legal name | John Doe | For rental agreements |
| Phone | Contact number | +1-555-0100 | Recommended |

**Password Requirements:**
- ✅ Minimum 8 characters
- ✅ Uppercase letters (A-Z)
- ✅ Lowercase letters (a-z)
- ✅ Numbers (0-9)
- ✅ Special characters (!@#$%^&*)

**Step 3: Accept Terms**
1. Read Terms of Service and Privacy Policy
2. Check: ☑️ "I agree to the Terms and Conditions"
3. Click "Create Account"

**Step 4: Verify Email**
1. Check your email inbox (and spam folder)
2. Look for "Verify your Starbike account" email
3. Click verification link
4. Redirected to login page
5. 💡 If no email after 5 minutes, request new verification

**Step 5: Complete First Login**
1. Go to login page
2. Enter email and password
3. Click "Sign In"
4. 🎉 Welcome to Starbike!

### Logging In

**Customer Login:**
- Navigate to `/login`
- Enter email and password
- Click "Sign In"

**Staff Login:**
- Navigate to `/staff/login`
- Enter staff credentials (provided by admin)
- Click "Sign In"

**Admin Login:**
- Navigate to `/admin/login`
- Enter admin credentials
- Click "Sign In"

### Password Reset

If you forgot your password:

1. Go to any login page
2. Click "Forgot Password?"
3. Enter your email address
4. Check email for reset link (expires in 60 minutes)
5. Click link and enter new password
6. Log in with new password

________________________________________

## Customer Guide

### Dashboard

After logging in, you'll see:
- Active bookings overview
- Quick action buttons
- Recent notifications
- Recommended vehicles

### Available Pages

| Page | Route | Description |
|------|-------|-------------|
| Home | `/` | Landing page |
| Services | `/services` | Browse services |
| Book | `/book` | View and book vehicles |
| About | `/about` | About Starbike |
| Contact | `/contact` | Contact info |
| Profile | `/profile` | Manage profile |
| Settings | `/settings` | Account settings |
| Status | `/status` | Booking status |
| Notifications | `/notifications` | View notifications |

### Profile Management

**View Profile**
1. Click "Profile" in navigation
2. View your information:
   - Personal details
   - Contact information
   - Account status

**Edit Profile**
1. Go to Profile → Edit Profile
2. Update:
   - Phone number
   - Address
   - Profile picture
3. Click "Save Changes"

**Change Password**
1. Go to Profile → Settings → Security
2. Click "Change Password"
3. Enter current password
4. Enter new password
5. Confirm new password
6. Click "Update Password"

### Browsing and Booking Vehicles

**Find the Perfect Vehicle**

**Step 1: Go to Booking Page**
- Click "Book Now" in navigation
- Or visit `/book`
- View all available vehicles

**Step 2: Use Filters**

Filter by:
- **Type**: Motorcycle or E-Bike
- **Brand**: Honda, Yamaha, Kawasaki, etc.
- **Price Range**: Set min/max daily rate
- **Status**: Show available only

**Step 3: View Vehicle Details**
- Click on any vehicle card
- See specifications, features, photos
- Check availability calendar
- Review rental terms

**Making a Booking**

**Step 1: Select Vehicle**
1. Choose your desired vehicle
2. Click "Book This Vehicle"

**Step 2: Choose Dates**
1. Select start date (pickup)
2. Select end date (return)
3. System calculates duration and cost
4. 💡 Only available dates are selectable

**Step 3: Add Details**
- Purpose of rental
- Pickup location
- Return location
- Special requests (optional)

**Step 4: Review Booking**

Verify:
- ✅ Vehicle name and model
- ✅ Dates (double-check!)
- ✅ Locations
- ✅ Total price
- ✅ Cancellation policy

**Step 5: Confirm**
1. Check: ☑️ "I agree to terms and conditions"
2. Click "Confirm Booking"
3. Complete payment (if required)
4. Receive confirmation email

### Tracking Bookings

**View Booking Status**
1. Go to Status page (`/status`)
2. View all your bookings

**Booking Statuses:**

| Status | Meaning | Action Required |
|--------|---------|----------------|
| 🟡 Pending | Awaiting review | Wait for confirmation (24 hrs) |
| 🟢 Confirmed | Approved! | Prepare documents for pickup |
| 🔵 Ready for Pickup | Vehicle ready | Visit pickup location |
| 🟣 Active | Currently rented | Enjoy! Return on time |
| ✅ Completed | Rental finished | View receipt |
| ❌ Cancelled | Booking cancelled | Check refund status |

**View Booking Details**
- Click any booking
- See complete information
- Download documents
- Contact support if needed

### Notifications

**View Notifications**
- Click bell icon (🔔) in navigation
- Or go to `/notifications`

**Notification Types:**
- 🔔 Booking status updates
- 💰 Payment confirmations
- 📝 Status changes
- 📢 System announcements

**Managing Notifications:**
- Click to mark as read
- Filter by type
- Clear all notifications

________________________________________

## Staff Guide

### Dashboard

After logging in at `/staff/login`, you'll see:
- Pending requests count
- Active rentals
- Available vehicles
- Today's schedule

### Available Pages

| Page | Route | Description |
|------|-------|-------------|
| Dashboard | `/staff/dashboard` | Staff overview |
| Requests | `/staff/requests` | Booking requests |
| Details | `/staff/details` | Request details |
| Motorcycles | `/staff/motorcycles` | Vehicle inventory |
| Services | `/staff/services` | Manage services |
| Profile | `/staff/profile` | Staff profile |
| Notifications | `/staff/notifications` | Notifications |

### Processing Booking Requests

**Step-by-Step Guide**

**Step 1: View Requests**
1. Navigate to `/staff/requests`
2. See all pending requests
3. Filter by status if needed

**Step 2: Open Request**
1. Click on a request
2. View at `/staff/details`
3. Review all information

**Step 3: Review Details**

Check:
- ✅ Customer information
- ✅ Vehicle availability
- ✅ Rental dates
- ✅ Pricing
- ✅ Special requests

**Step 4: Take Action**

**Option A: Approve**
1. Click "Approve Request"
2. Add internal notes (optional)
3. Click "Confirm Approval"
4. Customer receives automatic email

**Option B: Reject**
1. Click "Reject"
2. Select rejection reason
3. Write explanation for customer
4. Click "Confirm Rejection"

**Option C: Request Information**
1. Click "Request Information"
2. Specify what's needed
3. Write message to customer
4. Click "Send Request"

### Managing Vehicles

**View Inventory**
1. Go to `/staff/motorcycles`
2. See all vehicles with status

**Vehicle Status:**
- 🟢 Available: Ready to rent
- 🔴 Rented: Currently out
- 🟡 Maintenance: Under repair
- ⚫ Inactive: Not in service

**Update Vehicle Status**
1. Click on vehicle
2. Click "Update Status"
3. Select new status
4. Add notes (for maintenance)
5. Click "Update"

### Managing Services

Navigate to `/staff/services`:
- View all services
- Update availability
- Modify details
- Track bookings

________________________________________

## Admin Guide

### Dashboard

After logging in at `/admin/login`, you'll see:
- Key performance indicators (KPIs)
- Active bookings and requests
- Revenue metrics
- System health status

### Available Pages

| Page | Route | Description |
|------|-------|-------------|
| Dashboard | `/admin/dashboard` | System overview |
| Users | `/admin/users` | User management |
| Motorcycles | `/admin/motorcycles` | Inventory management |
| Services | `/admin/services` | Service management |
| Transactions | `/admin/transactions` | Financial records |
| Requests | `/admin/requests` | All requests |
| Details | `/admin/details` | Detailed view |
| Reports | `/admin/reports` | Analytics |
| Profile | `/admin/profile` | Admin profile |
| Notifications | `/admin/notifications` | Notifications |

### User Management

**View All Users**
1. Navigate to `/admin/users`
2. See all system users
3. Filter by role or status

**Create New User**

**Step 1: Start Creation**
- Click "Add New User"

**Step 2: Fill Information**

| Field | Description | Required |
|-------|-------------|----------|
| Email | User's email | ✅ Yes |
| Full Name | First and last name | ✅ Yes |
| Password | Temporary password | ✅ Yes |
| Phone | Contact number | Optional |
| Role | Access level | ✅ Yes |

**Step 3: Select Role**

| Role | Access Level |
|------|--------------|
| Customer | Browse and book vehicles |
| Staff | Process requests, manage vehicles |
| Technician | Vehicle maintenance focus |
| Manager | Staff access + reports |
| Admin | Full system control |

**Step 4: Create**
1. Review information
2. Click "Create User"
3. User receives welcome email

**Edit User**
1. Click on user name
2. Modify information
3. Click "Save Changes"

**Change User Role**
1. Open user details
2. Click role dropdown
3. Select new role
4. Confirm change
5. ⚠️ User must re-login

**Manage User Status**

- **Activate**: Enable login and access
- **Deactivate**: Disable login (preserves data)
- **Suspend**: Temporary revocation with reason
- **Delete**: Permanent removal (use with caution)

### Inventory Management

**Add New Vehicle**

**Step 1: Click "Add New Vehicle"**

**Step 2: Fill Details**
- Brand, Model, Year, Type
- Specifications (engine, transmission)
- Features and amenities
- Pricing (daily rate, deposit)
- Upload photos

**Step 3: Set Availability**
- Initial status
- Available locations
- Available from date

**Step 4: Save**
- Click "Add Vehicle"
- Vehicle appears in catalog

**Edit Vehicle**
1. Click on vehicle
2. Update details
3. Add/remove photos
4. Adjust pricing
5. Click "Save Changes"

### Service Management

Navigate to `/admin/services`:
- Add new services
- Edit existing services
- Set pricing
- Enable/disable services

### Financial Management

**View Transactions**
1. Go to `/admin/transactions`
2. See all financial records
3. Filter by date, type, status
4. Export to CSV/Excel

**Transaction Types:**
- Payments
- Refunds
- Deposits
- Late fees

**Process Refund**
1. Open transaction
2. Click "Issue Refund"
3. Enter amount
4. Add reason
5. Confirm refund

### Request Management

**View All Requests**
1. Navigate to `/admin/requests`
2. See all booking requests
3. Override staff decisions if needed
4. Reassign requests
5. Add priority flags

### Reports and Analytics

**System Monitoring**
1. Go to `/admin/reports`
2. View system health
3. Check Firebase status
4. Monitor response times
5. Review error rates

**Reports Available:**
- Booking trends
- Revenue reports
- Vehicle utilization
- User activity
- Performance metrics

**Export Reports:**
- PDF format
- Excel format
- CSV format

### System Configuration

**Security Settings:**
- Password requirements
- Two-factor authentication
- Session timeout
- Login attempt limits

**Firebase Settings:**
- Monitor quota usage
- Configure security rules
- Review usage reports

________________________________________

## Common Features

### Notifications System

All users receive real-time notifications for:
- New announcements
- Status changes
- Booking updates
- System messages

**Features:**
- Real-time updates (no refresh needed)
- Mark as read/unread
- Clear notifications
- Notification count badge

### Search and Filters

Most list pages support:
- **Search**: Find by name, email, or ID
- **Filters**: Filter by status, type, date
- **Sorting**: Sort by date, amount, name
- **Pagination**: Navigate multiple pages

### Document Viewing

- Click document link to view
- Opens in new tab
- Supported: PDF, JPG, PNG
- Download option available

### Responsive Design

System works on:
- Desktop computers
- Tablets
- Mobile phones

All features accessible on any device.

________________________________________

## Troubleshooting

### Login Issues

**Problem: Cannot log in**

Solutions:
- Verify email and password (case-sensitive)
- Check correct login page (customer/staff/admin)
- Clear browser cache and cookies
- Try incognito/private mode
- Use password reset if needed
- Contact admin if account suspended

**Problem: Stuck in login loop**

Solutions:
- Clear browser data (cookies and cache)
- Contact admin to verify role
- Try different browser
- Disable browser extensions

### Booking Issues

**Problem: Cannot complete booking**

Solutions:
- Check all required fields filled
- Verify dates valid and vehicle available
- Refresh page and try again
- Try different browser
- Contact support

**Problem: Booking not showing**

Solutions:
- Refresh page (F5)
- Check status filter set to "All"
- Wait 1-2 minutes for sync
- Check email for confirmation
- Contact support with booking reference

### Payment Issues

**Problem: Payment failed**

Solutions:
- Verify card details correct
- Check sufficient funds
- Contact your bank
- Try different payment method
- Wait 5 minutes and retry

### Display Issues

**Problem: Page not loading**

Solutions:
- Check internet connection
- Refresh page (F5 or Ctrl+R)
- Clear browser cache
- Update browser to latest version
- Try different browser

### Firebase Errors

**Problem: "Permission denied" or connection errors**

Solutions:
- Check you're logged in
- Verify correct account role
- Check internet connection
- Try again after few minutes
- Contact admin if persistent

________________________________________

## FAQs

### General Questions

**Q: Do I need to download anything?**  
A: No. Starbike runs in your web browser. No downloads required.

**Q: Which browsers are supported?**  
A: Chrome 90+, Firefox 88+, Safari 14+, Edge 90+. Keep browser updated.

**Q: Can I use it on mobile?**  
A: Yes! The site is fully responsive and works on all devices.

**Q: Is my data secure?**  
A: Yes. We use Firebase Authentication with TLS encryption and industry security standards.

### Account Questions

**Q: How do I reset my password?**  
A: Click "Forgot Password?" on login page, enter email, follow reset link.

**Q: Can I change my email address?**  
A: Yes, in Profile → Edit → Change Email. You'll need to verify new address.

**Q: How do I delete my account?**  
A: Contact support. Note that some data may be retained for legal purposes.

### Booking Questions

**Q: How far in advance can I book?**  
A: Typically 6-12 months in advance, depending on configuration.

**Q: What's the minimum rental period?**  
A: Usually 1 day, but may vary by vehicle. Check vehicle details.

**Q: Can I modify or cancel a booking?**  
A: Yes, if booking is pending or confirmed. Cannot modify active rentals. Check cancellation policy for refunds.

**Q: How do I track my booking?**  
A: Go to Status page (`/status`) to see all bookings with current status.

### Payment Questions

**Q: When do I pay?**  
A: Depends on configuration - either immediately or at pickup. Check during booking.

**Q: What payment methods are accepted?**  
A: Credit cards, debit cards, and digital wallets (varies by gateway).

**Q: Is there a deposit?**  
A: Yes, most rentals require security deposit, refunded after successful return.

**Q: How long do refunds take?**  
A: Credit cards: 5-10 days, Debit cards: 3-7 days, Digital wallets: 1-3 days.

### Staff Questions

**Q: How do I become a staff member?**  
A: Staff accounts created by administrators. Contact your supervisor.

**Q: How do I approve a booking?**  
A: Go to Requests, open request, review details, click "Approve."

### Admin Questions

**Q: How do I add a new user?**  
A: Go to Users → Add New User → Fill details → Assign role → Save.

**Q: How do I add a vehicle?**  
A: Go to Motorcycles → Add New Vehicle → Fill details → Upload photos → Save.

**Q: How do I generate reports?**  
A: Go to Reports → Select type → Set parameters → Generate → Export.

________________________________________

## Getting Help

### Contact Information

- **For Customers**: Contact your rental location or support
- **For Staff**: Contact system administrator
- **For Admins**: Check documentation or contact development team

### Support Resources

- User manual (this document)
- System help tooltips (hover over ? icons)
- FAQ section
- Administrator assistance

### Before Contacting Support

Prepare this information:
- Your email address
- Description of issue
- Screenshots if applicable
- Browser and device information
- Steps to reproduce the problem

________________________________________

## Glossary

**Admin**: System administrator with full access to all features

**Booking**: Rental request or reservation for a vehicle

**Customer**: User who rents vehicles

**Dashboard**: Main landing page after login showing overview

**Firebase**: Google's platform providing authentication and database services

**Firestore**: Cloud-based database used by Starbike

**Inventory**: Complete collection of available vehicles

**Notification**: Alert or message about bookings or system updates

**Profile**: User's personal information and settings page

**Role**: User's access level (Customer, Staff, Technician, Manager, Admin)

**Staff**: Rental agent who processes bookings and manages vehicles

**Status**: Current state of booking (Pending, Confirmed, Active, etc.)

**Technician**: Staff member focused on vehicle maintenance

**Transaction**: Financial operation (payment, refund, deposit)

________________________________________

Document Version: 1.0  
Last Updated: December 2025  
Platform: Starbike E-Bike and Motorcycle Rental System  
Technology: React + Vite + Firebase
