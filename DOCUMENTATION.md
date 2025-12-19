# User Management CRUD API - Documentation

## Title Page

### User Management CRUD API Project

Capstone-Aligned Project  
Laravel API Development  
User Management System  
[Date: 2024]

---

## Introduction

This document provides comprehensive documentation for the User Management CRUD API project, developed as part of a capstone project requirement. The API is built using Laravel framework and provides complete Create, Read, Update, and Delete (CRUD) operations for user management.

### Project Overview

The User Management CRUD API is a RESTful web service that serves as a core backend module for capstone projects. It provides essential functionality for managing user accounts with role-based access control, supporting the Starbike project's user roles: **Admin** (full system access and user management), **Staff** (manage bookings and handle customer requests), and **Customer** (browse and book vehicles).

### Project Objectives

- Develop a complete CRUD API using Laravel framework
- Implement user management with proper validation
- Support multiple user roles for capstone project integration
- Provide consistent JSON response format
- Ensure secure password handling
- Create comprehensive API documentation

### Scope

This API provides the following core functionalities:

- Create new users with validation
- List all users in the system
- Retrieve specific user details by ID
- Update existing user information
- Delete users from the system

---

## Development Environment

### Required Software and Tools

The following software and tools were used in the development of this project:

- **PHP**: Version 8.2.12
- **Composer**: Version 2.9.2 (PHP dependency manager)
- **Laravel Framework**: Version 12.11.0
- **Database**: SQLite (default development database)
- **Postman**: For API testing and documentation
- **Git**: Version control system
- **Operating System**: Windows 10/11

### System Requirements

- **Operating System**: Windows 10/11, macOS, or Linux
- **Web Server**: PHP built-in development server (or Apache/Nginx for production)
- **PHP Extensions Required**:
  - OpenSSL
  - PDO
  - Mbstring
  - Tokenizer
  - XML
  - Ctype
  - JSON
  - BCMath

### Environment Setup

1. **PHP Installation Verification**

   ```bash
   php -v
   # Output: PHP 8.2.12
   ```

2. **Composer Installation Verification**

   ```bash
   composer --version
   # Output: Composer version 2.9.2
   ```

3. **Laravel Project Creation**

   ```bash
   composer create-project laravel/laravel user-management-api
   cd user-management-api
   ```

4. **Environment Configuration**

   ```bash
   # Copy environment file
   cp .env.example .env
   
   # Generate application key
   php artisan key:generate
   ```

5. **Database Setup (SQLite)**

   ```bash
   # Create SQLite database file
   touch database/database.sqlite
   ```

   Update `.env` file:

   ```env
   DB_CONNECTION=sqlite
   DB_DATABASE=C:\Users\ACER\Crisvin\projects\Starbike\starbike-frontend\user-management-api\database\database.sqlite
   ```

6. **Run Database Migrations**

   ```bash
   php artisan migrate
   ```

7. **Start Development Server**

   ```bash
   php artisan serve
   ```

   The API will be available at `http://localhost:8000`

---

## Step-by-Step Development Process

### Step 1: Database Schema Design

The users table was designed with the following structure to meet the project requirements:

#### Users Table Structure

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| id | bigint | Primary Key, Auto Increment | Unique identifier |
| name | varchar(255) | Required | User's full name |
| email | varchar(255) | Unique, Nullable | User's email address |
| username | varchar(255) | Unique, Nullable | User's username |
| password | varchar(255) | Required, Hashed | User's password (bcrypt hashed) |
| role | varchar(255) | Required | User role (admin, staff, customer) - Admin: full system access, Staff: manage bookings, Customer: browse and book vehicles |
| contact_number | varchar(20) | Nullable | Contact phone number |
| created_at | timestamp | Auto | Record creation timestamp |
| updated_at | timestamp | Auto | Record update timestamp |

**Important Note**: Either email or username must be provided (at least one is required for user creation).

### Step 2: Migration Creation

A migration file was created to define the users table structure:

**File Location**: `database/migrations/0001_01_01_000000_create_users_table.php`

**Migration Code**:

```php
Schema::create('users', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->string('email')->unique()->nullable();
    $table->string('username')->unique()->nullable();
    $table->string('password');
    $table->string('role');
    $table->string('contact_number')->nullable();
    $table->timestamps();
});
```

**Execution**:

```bash
php artisan migrate
```

### Step 3: User Model Development

The User model was updated to include all necessary fields and configurations:

**File Location**: `app/Models/User.php`

**Key Features**:

- Mass assignable fields: `name`, `email`, `username`, `password`, `role`, `contact_number`
- Password hashing: Automatic via Laravel's `casts` method
- Hidden fields: `password`, `remember_token` (for security)

**Model Code**:

```php
protected $fillable = [
    'name',
    'email',
    'username',
    'password',
    'role',
    'contact_number',
];

protected $hidden = [
    'password',
    'remember_token',
];

protected function casts(): array
{
    return [
        'email_verified_at' => 'datetime',
        'password' => 'hashed',
    ];
}
```

### Step 4: Controller Implementation

A UserController was created with all CRUD operations:

**File Location**: `app/Http/Controllers/UserController.php`

**Controller Methods**:

1. **index()** - Retrieves all users (GET /api/users)
2. **store()** - Creates a new user (POST /api/users)
3. **show($id)** - Retrieves a specific user (GET /api/users/{id})
4. **update($id)** - Updates an existing user (PUT/PATCH /api/users/{id})
5. **destroy($id)** - Deletes a user (DELETE /api/users/{id})

**Key Implementation Features**:

- Input validation using Laravel Validator
- Consistent JSON response format with `success`, `message`, and `data` fields
- Error handling with try-catch blocks
- Password hashing using Hash facade
- Proper HTTP status codes (200, 201, 404, 422, 500)

**Example Controller Method (store)**:

```php
public function store(Request $request)
{
    try {
        $validator = Validator::make($request->all(), [
            'name' => 'required|string|max:255',
            'email' => 'nullable|email|unique:users,email',
            'username' => 'nullable|string|unique:users,username',
            'password' => 'required|string|min:6',
            'role' => 'required|string|in:admin,staff,customer',
            'contact_number' => 'nullable|string|max:20',
        ]);

        if (empty($request->email) && empty($request->username)) {
            return response()->json([
                'success' => false,
                'message' => 'Either email or username must be provided',
                'errors' => ['email' => ['Either email or username is required']]
            ], 422);
        }

        if ($validator->fails()) {
            return response()->json([
                'success' => false,
                'message' => 'Validation failed',
                'errors' => $validator->errors()
            ], 422);
        }

        $user = User::create([
            'name' => $request->name,
            'email' => $request->email,
            'username' => $request->username,
            'password' => Hash::make($request->password),
            'role' => $request->role,
            'contact_number' => $request->contact_number,
        ]);

        return response()->json([
            'success' => true,
            'message' => 'User created successfully',
            'data' => $user
        ], 201);
    } catch (\Exception $e) {
        return response()->json([
            'success' => false,
            'message' => 'Failed to create user',
            'error' => $e->getMessage()
        ], 500);
    }
}
```

### Step 5: API Routes Configuration

API routes were configured to handle all CRUD operations:

**File Location**: `routes/api.php`

**Routes Defined**:

```php
Route::prefix('users')->group(function () {
    Route::get('/', [UserController::class, 'index']);           // List Users
    Route::post('/', [UserController::class, 'store']);           // Create User
    Route::get('/{id}', [UserController::class, 'show']);         // Show User
    Route::put('/{id}', [UserController::class, 'update']);      // Update User
    Route::patch('/{id}', [UserController::class, 'update']);    // Update User (Alternative)
    Route::delete('/{id}', [UserController::class, 'destroy']);  // Delete User
});
```

**Bootstrap Configuration**:

**File Location**: `bootstrap/app.php`

API routes were registered in the application bootstrap:

```php
->withRouting(
    web: __DIR__.'/../routes/web.php',
    api: __DIR__.'/../routes/api.php',  // API routes added
    commands: __DIR__.'/../routes/console.php',
    health: '/up',
)
```

### Step 6: Response Format Standardization

All API responses follow a consistent JSON format:

**Success Response Format**:

```json
{
    "success": true,
    "message": "Operation successful message",
    "data": { ... }
}
```

**Error Response Format**:

```json
{
    "success": false,
    "message": "Error message",
    "errors": {
        "field": ["Error details"]
    }
}
```

### Step 7: API Endpoints Implementation

All required CRUD endpoints were implemented:

#### POST /api/users - Create User

**Request Example**:

```json
{
    "name": "John Doe",
    "email": "john@example.com",
    "username": "johndoe",
    "password": "password123",
    "role": "admin",
    "contact_number": "+1234567890"
}
```

**Response (201 Created)**:

```json
{
    "success": true,
    "message": "User created successfully",
    "data": {
        "id": 1,
        "name": "John Doe",
        "email": "john@example.com",
        "username": "johndoe",
        "role": "admin",
        "contact_number": "+1234567890",
        "created_at": "2024-01-01T00:00:00.000000Z",
        "updated_at": "2024-01-01T00:00:00.000000Z"
    }
}
```

#### GET /api/users - List Users

**Response (200 OK)**:

```json
{
    "success": true,
    "message": "Users retrieved successfully",
    "data": [
        {
            "id": 1,
            "name": "John Doe",
            "email": "john@example.com",
            "username": "johndoe",
            "role": "admin",
            "contact_number": "+1234567890",
            "created_at": "2024-01-01T00:00:00.000000Z",
            "updated_at": "2024-01-01T00:00:00.000000Z"
        }
    ]
}
```

#### GET /api/users/{id} - Show User

**Response (200 OK)**:

```json
{
    "success": true,
    "message": "User retrieved successfully",
    "data": {
        "id": 1,
        "name": "John Doe",
        "email": "john@example.com",
        "username": "johndoe",
        "role": "admin",
        "contact_number": "+1234567890",
        "created_at": "2024-01-01T00:00:00.000000Z",
        "updated_at": "2024-01-01T00:00:00.000000Z"
    }
}
```

#### PUT /api/users/{id} - Update User

**Request Example**:

```json
{
    "name": "John Doe Updated",
    "email": "john.updated@example.com",
    "role": "staff"
}
```

**Response (200 OK)**:

```json
{
    "success": true,
    "message": "User updated successfully",
    "data": {
        "id": 1,
        "name": "John Doe Updated",
        "email": "john.updated@example.com",
        "role": "staff",
        ...
    }
}
```

#### DELETE /api/users/{id} - Delete User

**Response (200 OK)**:

```json
{
    "success": true,
    "message": "User deleted successfully",
    "data": null
}
```

---

## Screenshots

**Note**: Screenshots are required for each step of the development process to document the implementation.

### Development Environment Screenshots

#### Screenshot 1: PHP and Composer Installation Verification

[Insert screenshot showing:

- Terminal/Command Prompt with `php -v` command output showing PHP 8.2.12
- `composer --version` command output showing Composer version 2.9.2]

**Description**: Verifies that PHP and Composer are properly installed and configured for the development environment.

#### Screenshot 2: Laravel Project Creation

[Insert screenshot showing:

- Terminal with `composer create-project laravel/laravel user-management-api` command execution
- Successful project creation output]

**Description**: Shows the Laravel project being created successfully using Composer.

#### Screenshot 3: Environment Configuration

[Insert screenshot showing:

- `.env` file opened in editor showing database configuration
- `php artisan key:generate` command execution in terminal]

**Description**: Demonstrates the environment file setup and application key generation.

### Database Development Screenshots

#### Screenshot 4: Migration File Creation

[Insert screenshot showing:

- File explorer/IDE showing the migration file: `database/migrations/0001_01_01_000000_create_users_table.php`
- Migration file code with the Schema::create() method visible in the editor]

**Description**: Shows the migration file location and structure for creating the users table.

#### Screenshot 5: Migration Execution

[Insert screenshot showing:

- Terminal with `php artisan migrate` command execution
- Successful migration output showing "Users table created successfully"]

**Description**: Demonstrates running the migration to create the users table in the database.

#### Screenshot 6: Database Structure

[Insert screenshot showing:

- Database viewer (DB Browser for SQLite or similar) displaying the users table structure
- Table showing all columns: id, name, email, username, password, role, contact_number, created_at, updated_at
- OR SQLite database file structure]

**Description**: Displays the actual database structure and users table schema after migration.

### Model Development Screenshots

#### Screenshot 7: User Model File

[Insert screenshot showing:

- File explorer/IDE showing `app/Models/User.php` file
- Model code visible showing `$fillable`, `$hidden`, and `casts()` method]

**Description**: Shows the User model implementation with mass assignable fields, hidden fields, and password hashing configuration.

### Controller Development Screenshots

#### Screenshot 8: UserController File Structure

[Insert screenshot showing:

- File explorer/IDE showing `app/Http/Controllers/UserController.php` file
- Controller class with all five methods visible: index(), store(), show(), update(), destroy()]

**Description**: Displays the UserController file location and method structure.

#### Screenshot 9: Controller Implementation (store method)

[Insert screenshot showing:

- UserController.php open in editor
- The store() method code visible showing validation, error handling, and response formatting]

**Description**: Shows detailed implementation of the create user functionality with validation and error handling.

### Routes Configuration Screenshots

#### Screenshot 10: API Routes File

[Insert screenshot showing:

- File explorer/IDE showing `routes/api.php` file
- Routes code visible showing all CRUD endpoints with Route::prefix('users')]

**Description**: Displays the API routes configuration with all CRUD endpoints defined.

#### Screenshot 11: Bootstrap Configuration

[Insert screenshot showing:

- File explorer/IDE showing `bootstrap/app.php` file
- Code visible showing `->withRouting()` method with API routes registration]

**Description**: Shows the bootstrap configuration where API routes are registered.

#### Screenshot 12: Route List Verification

[Insert screenshot showing:

- Terminal with `php artisan route:list --path=api` command execution
- Output showing all registered API routes: GET, POST, PUT, PATCH, DELETE for /api/users]

**Description**: Verifies that all API routes are properly registered and accessible.

### API Testing Screenshots (Postman)

#### Screenshot 13: Postman Collection Structure

[Insert screenshot showing:

- Postman application open with collection "User Management CRUD API (Capstone)" visible
- Folder "Users Module (Capstone)" expanded showing all 5 CRUD endpoints:
  - POST - Create User
  - GET - List Users
  - GET - Show User
  - PUT - Update User
  - DELETE - Delete User]

**Description**: Shows the complete Postman collection structure with all endpoints organized in the "Users Module (Capstone)" folder.

#### Screenshot 14: Create User (POST) Request and Response

[Insert screenshot showing:

- Postman with POST /api/users request open
- Request URL: <http://localhost:8000/api/users>
- Request method: POST selected
- Headers tab showing Content-Type: application/json
- Body tab (raw JSON) showing request data:

  ```json
  {
      "name": "John Doe",
      "email": "john@example.com",
      "password": "password123",
      "role": "admin",
      "contact_number": "+1234567890"
  }
  ```

- Response panel showing:
  - Status: 201 Created
  - Response body with success: true, message, and user data]

**Description**: Demonstrates the Create User endpoint with complete request body and successful 201 response.

#### Screenshot 15: List Users (GET) Request and Response

[Insert screenshot showing:

- Postman with GET /api/users request open
- Request URL: <http://localhost:8000/api/users>
- Request method: GET selected
- Response panel showing:
  - Status: 200 OK
  - Response body with success: true, message, and array of user objects]

**Description**: Shows the List Users endpoint returning all users in the system with 200 OK response.

#### Screenshot 16: Show User (GET by ID) Request and Response

[Insert screenshot showing:

- Postman with GET /api/users/1 request open
- Request URL: <http://localhost:8000/api/users/1>
- Request method: GET selected
- Response panel showing:
  - Status: 200 OK
  - Response body with success: true, message, and single user object with all fields]

**Description**: Demonstrates retrieving a specific user by ID with 200 OK response showing complete user data.

#### Screenshot 17: Update User (PUT) Request and Response

[Insert screenshot showing:

- Postman with PUT /api/users/1 request open
- Request URL: <http://localhost:8000/api/users/1>
- Request method: PUT selected
- Body tab (raw JSON) showing updated data:

  ```json
  {
      "name": "John Doe Updated",
      "role": "staff"
  }
  ```

- Response panel showing:
  - Status: 200 OK
  - Response body with success: true, message, and updated user data]

**Description**: Shows the Update User endpoint with request body containing updated fields and successful 200 response with updated user data.

#### Screenshot 18: Delete User (DELETE) Request and Response

[Insert screenshot showing:

- Postman with DELETE /api/users/1 request open
- Request URL: <http://localhost:8000/api/users/1>
- Request method: DELETE selected
- Response panel showing:
  - Status: 200 OK
  - Response body with success: true, message: "User deleted successfully", and data: null]

**Description**: Demonstrates the Delete User endpoint with successful 200 response confirming user deletion.

#### Screenshot 19: Validation Error Response

[Insert screenshot showing:

- Postman with POST /api/users request open
- Request body with missing required fields (e.g., missing "name" or "password")
- Response panel showing:
  - Status: 422 Unprocessable Entity
  - Response body with success: false, message: "Validation failed", and errors object showing field-specific error messages]

**Description**: Shows proper validation error handling when required fields are missing or invalid, displaying 422 status with detailed error messages.

#### Screenshot 20: Not Found Error Response

[Insert screenshot showing:

- Postman with GET /api/users/999 request open (non-existent user ID)
- Response panel showing:
  - Status: 404 Not Found
  - Response body with success: false, message: "User not found"]

**Description**: Demonstrates error handling for non-existent resources with 404 Not Found response.

### Server and Development Screenshots

#### Screenshot 21: Laravel Development Server Running

[Insert screenshot showing:

- Terminal with `php artisan serve` command execution
- Server output showing "Laravel development server started: <http://127.0.0.1:8000>"
- Server running and listening on port 8000]

**Description**: Shows the Laravel development server successfully started and running.

#### Screenshot 22: API Base URL Test

[Insert screenshot showing:

- Browser or Postman accessing the API base URL
- OR Terminal showing curl command testing the API
- Successful connection to <http://localhost:8000/api>]

**Description**: Verifies that the API server is accessible and responding to requests.

---

## Problems Encountered and Solutions

### Problem 1: API Routes Returning 404 Errors

**Issue**: When testing the API endpoints, all routes were returning 404 Not Found errors.

**Root Cause**: In Laravel 12, API routes need to be explicitly registered in the `bootstrap/app.php` file. The default installation did not include API route registration.

**Solution**: Updated the `bootstrap/app.php` file to include API routes:

```php
return Application::configure(basePath: dirname(__DIR__))
    ->withRouting(
        web: __DIR__.'/../routes/web.php',
        api: __DIR__.'/../routes/api.php',  // Added this line
        commands: __DIR__.'/../routes/console.php',
        health: '/up',
    )
```

**Result**: All API routes became accessible and functional.

### Problem 2: Email or Username Validation Requirement

**Issue**: The requirement states that either email or username must be provided, but Laravel's standard validation doesn't handle "either/or" scenarios easily.

**Root Cause**: Standard Laravel validation rules check each field independently, not the relationship between fields.

**Solution**: Added custom validation logic in the `store()` method to check if at least one field is provided:

```php
if (empty($request->email) && empty($request->username)) {
    return response()->json([
        'success' => false,
        'message' => 'Either email or username must be provided',
        'errors' => ['email' => ['Either email or username is required']]
    ], 422);
}
```

**Result**: The API now properly validates that at least one authentication field is provided.

### Problem 3: Password Not Being Hashed

**Issue**: Initially, passwords were being stored in plain text in the database, which is a security risk.

**Root Cause**: The password was being assigned directly without hashing.

**Solution**: Used Laravel's Hash facade to hash passwords before storing:

```php
'password' => Hash::make($request->password)
```

Additionally, configured the User model to automatically hash passwords using Laravel's casting:

```php
protected function casts(): array
{
    return [
        'password' => 'hashed',
    ];
}
```

**Result**: All passwords are now securely hashed using bcrypt before storage.

### Problem 4: Unique Constraint Validation on Update

**Issue**: When updating a user, the unique validation for email and username was failing because it was checking against all users, including the current user being updated.

**Root Cause**: The unique validation rule was not excluding the current record from the uniqueness check.

**Solution**: Modified the validation rule to exclude the current user's ID:

```php
'email' => 'nullable|email|unique:users,email,' . $id,
'username' => 'nullable|string|unique:users,username,' . $id,
```

**Result**: Users can now update their information without validation errors when keeping the same email or username.

### Problem 5: Inconsistent Response Format

**Issue**: Initially, responses did not follow a consistent format, making it difficult for frontend integration.

**Root Cause**: Different controller methods were returning responses in different formats.

**Solution**: Standardized all responses to follow a consistent structure:

- Success responses: `{ "success": true, "message": "...", "data": {...} }`
- Error responses: `{ "success": false, "message": "...", "errors": {...} }`

**Result**: All API responses now follow a consistent format, making frontend integration easier.

### Problem 6: Missing Error Handling

**Issue**: Unhandled exceptions were causing 500 errors without proper error messages.

**Root Cause**: Controller methods did not have try-catch blocks to handle exceptions.

**Solution**: Wrapped all controller methods in try-catch blocks:

```php
try {
    // Controller logic
} catch (\Exception $e) {
    return response()->json([
        'success' => false,
        'message' => 'Failed to perform operation',
        'error' => $e->getMessage()
    ], 500);
}
```

**Result**: All errors are now properly caught and returned in a consistent format.

---

## Conclusion

### Project Summary

The User Management CRUD API project has been successfully completed, meeting all the requirements specified in the capstone project instructions. This API is specifically designed for the **Starbike** (E-bike and Motorcycle Rental System) capstone project, supporting the three core user roles: **Admin** (full system access), **Staff** (booking management), and **Customer** (browse and book vehicles). The API provides complete CRUD functionality for user management with proper validation, error handling, and security measures.

### Key Achievements

1. **Complete CRUD Operations**: All five required endpoints (Create, Read, Update, Delete, List) have been successfully implemented and tested.

2. **Proper Validation**: Comprehensive input validation ensures data integrity, including:
   - Required field validation
   - Email format validation
   - Unique constraint validation
   - Custom validation for email/username requirement

3. **Consistent Response Format**: All API responses follow a standardized JSON format with `success`, `message`, and `data` fields, making frontend integration straightforward.

4. **Security Implementation**: Passwords are securely hashed using bcrypt before storage, ensuring user data security.

5. **Error Handling**: Comprehensive error handling with try-catch blocks ensures all exceptions are properly caught and returned in a user-friendly format.

6. **Documentation**: Complete Postman collection and documentation have been created for easy API testing and integration.

### Technical Implementation Highlights

- **Framework**: Laravel 12.11.0
- **Database**: SQLite for development
- **API Style**: RESTful API with proper HTTP methods and status codes
- **Validation**: Laravel Validator with custom validation rules
- **Security**: Password hashing, input sanitization, and proper error messages

### Project Deliverables

All required deliverables have been completed:

- ✅ Laravel API project with complete CRUD functionality
- ✅ Postman collection with all endpoints documented
- ✅ Word documentation (this document)
- ✅ Deployment configuration files for multiple platforms
- ✅ README and setup instructions

### Future Enhancements

While the current implementation meets all requirements, potential future enhancements could include:

- JWT authentication and authorization
- User profile image upload functionality
- Email verification system
- Password reset functionality
- User search and filtering capabilities
- Pagination for large user lists
- Rate limiting for API protection
- API versioning for backward compatibility

### Learning Outcomes

This project provided valuable experience in:

- Laravel framework development and best practices
- RESTful API design principles
- Database migrations and model relationships
- Input validation and error handling
- API testing with Postman
- Documentation and deployment strategies

### Final Notes

The User Management CRUD API is production-ready and can be easily integrated into any capstone project requiring user management functionality. The code follows Laravel best practices and is well-documented for future maintenance and enhancement.

---

### End of Documentation
