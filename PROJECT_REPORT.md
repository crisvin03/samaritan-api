# Final Project Report

**Project Title**: Advanced RESTful API - Laravel Backend  
**Course**: IT Specialization Track 4 - Advanced Web Applications  
**Group Members**: [List all 6 members with their roles]  
**Date**: [Insert Date]

---

## 1. Introduction & Objectives

### 1.1 Introduction

This project implements a RESTful API using Laravel 10 for user authentication and post management. The API uses Laravel Sanctum for token-based authentication, follows RESTful principles, and includes comprehensive documentation via Swagger/OpenAPI. All endpoints are tested using PHPUnit feature tests, and the application is configured for cloud deployment.

### 1.2 Objectives

- Design and develop a RESTful API using Laravel
- Implement secure authentication using Laravel Sanctum
- Create comprehensive API documentation using Swagger/OpenAPI
- Develop feature tests for at least 3 endpoint categories
- Deploy the API to a cloud platform (Railway/Vercel/AWS)
- Demonstrate advanced web development concepts and best practices

### 1.3 Scope

**Included:** User authentication, post CRUD operations, token-based API authentication, input validation, authorization, API documentation, feature tests, Postman collection, deployment configuration.

**Excluded:** Frontend application, email verification, password reset, rate limiting, real-time features, file uploads.

---

## 2. ERD (Entity Relationship Diagram)

### 2.1 Database Schema

**Entities:**
- **Users**: User authentication and profile information
- **Posts**: Blog posts/content created by users
- **Personal Access Tokens**: Authentication tokens for API access

**Relationships:**
- User has many Posts (One-to-Many)
- User has many Personal Access Tokens (One-to-Many)
- Post belongs to User (Many-to-One)

### 2.2 ERD Diagram

**[INSERT ERD DIAGRAM HERE - Use draw.io, Lucidchart, or MySQL Workbench]**

**Table Structures:**

**users**
- id (PK), name, email (Unique), email_verified_at, password, remember_token, timestamps

**posts**
- id (PK), user_id (FK → users.id), title, content, timestamps

**personal_access_tokens**
- id (PK), tokenable_type, tokenable_id (FK), name, token (Unique), abilities, last_used_at, expires_at, timestamps

---

## 3. Architecture Diagram

### 3.1 System Architecture

**[INSERT ARCHITECTURE DIAGRAM HERE - Show Client Layer, Laravel Application, Authentication Layer, Database Layer, Documentation Layer]**

**Architecture Components:**
1. **Client Layer**: Frontend, Postman, Mobile Apps → HTTP/HTTPS requests
2. **Laravel Application**: Routes → Middleware → Controllers → Models
3. **Authentication Layer (Sanctum)**: Token validation and route protection
4. **Database Layer**: MySQL/PostgreSQL storing users, posts, tokens
5. **Documentation Layer**: Swagger UI for interactive API documentation

### 3.2 Technology Stack

- **Backend Framework**: Laravel 10
- **PHP Version**: 8.1+
- **Database**: MySQL/PostgreSQL
- **Authentication**: Laravel Sanctum
- **API Documentation**: L5-Swagger (OpenAPI 3.0)
- **Testing**: PHPUnit
- **Deployment**: Railway/Vercel/AWS

### 3.3 Request Flow

1. Client sends HTTP request → 2. Middleware (CORS, Auth) → 3. Route dispatches to Controller → 4. Controller validates data → 5. Controller interacts with Model/Database → 6. JSON response returned to client

---

## 4. API Endpoints Table

| Method | Endpoint | Description | Auth | Request Body | Status | Response |
|--------|----------|------------|------|--------------|--------|----------|
| POST | `/api/register` | Register new user | No | name, email, password, password_confirmation | 201 | User + token |
| POST | `/api/login` | User login | No | email, password | 200 | User + token |
| POST | `/api/logout` | User logout | Yes | - | 200 | Success message |
| GET | `/api/user` | Get authenticated user | Yes | - | 200 | User object |
| GET | `/api/posts` | Get all posts (paginated) | Yes | page (query) | 200 | Paginated posts |
| POST | `/api/posts` | Create new post | Yes | title, content | 201 | Post object |
| GET | `/api/posts/{id}` | Get specific post | Yes | - | 200 | Post object |
| PUT | `/api/posts/{id}` | Update post | Yes | title, content (optional) | 200 | Updated post |
| DELETE | `/api/posts/{id}` | Delete post | Yes | - | 200 | Success message |

**Authentication**: Bearer token in Authorization header

---

## 5. Screenshots

### 5.1 API Testing Screenshots

#### 5.1.1 Postman Collection
**[SCREENSHOT: Postman collection with all endpoints organized in folders]**

#### 5.1.2 Authentication Flow
- **[SCREENSHOT: Register endpoint - POST /api/register with 201 response]**
- **[SCREENSHOT: Login endpoint - POST /api/login with 200 response and token]**
- **[SCREENSHOT: Logout endpoint - POST /api/logout with Bearer token]**

#### 5.1.3 Post Management
- **[SCREENSHOT: Get all posts - GET /api/posts with paginated response]**
- **[SCREENSHOT: Create post - POST /api/posts with 201 response]**
- **[SCREENSHOT: Get specific post - GET /api/posts/{id}]**
- **[SCREENSHOT: Update post - PUT /api/posts/{id} with 200 response]**
- **[SCREENSHOT: Delete post - DELETE /api/posts/{id} with 200 response]**

#### 5.1.4 Error Handling
- **[SCREENSHOT: Validation errors - 422 response with error messages]**
- **[SCREENSHOT: Unauthorized access - 401 response without token]**
- **[SCREENSHOT: Forbidden access - 403 response when modifying other user's post]**
- **[SCREENSHOT: Not found - 404 response for non-existent post]**

### 5.2 API Documentation Screenshots

#### 5.2.1 Swagger UI
- **[SCREENSHOT: Swagger homepage at /api/documentation]**
- **[SCREENSHOT: Authentication endpoints expanded in Swagger]**
- **[SCREENSHOT: Posts endpoints expanded in Swagger]**
- **[SCREENSHOT: Try it out feature with executed request/response]**

### 5.3 Testing Screenshots

#### 5.3.1 Feature Tests
- **[SCREENSHOT: Terminal showing `php artisan test` command]**
- **[SCREENSHOT: Test results showing all 11 tests passed (3 test files)]**
- **[SCREENSHOT: Individual test file results (e.g., ApiPostTest)]**

### 5.4 Deployment Screenshots

#### 5.4.1 Deployment Platform
- **[SCREENSHOT: Deployment dashboard (Railway/Heroku/AWS)]**
- **[SCREENSHOT: Environment variables configuration]**
- **[SCREENSHOT: Deployment logs showing successful build]**
- **[SCREENSHOT: Live API endpoint response from production URL]**

#### 5.4.2 Production API
- **[SCREENSHOT: Production API health check response]**
- **[SCREENSHOT: Production Swagger documentation accessible]**
- **[SCREENSHOT: API response from production environment]**

---

## 6. Reflection on Challenges & Solutions

### 6.1 Challenges Encountered

#### Challenge 1: Authentication Implementation
**Description**: Missing `config/auth.php` file caused "Auth guard [] is not defined" error, preventing Sanctum from authenticating users.

**Solution**: Created `config/auth.php` with proper guard configuration (web and sanctum guards) and user provider setup.

**Learning**: Configuration files are essential for Laravel's authentication system. Always verify configuration when encountering auth errors.

#### Challenge 2: API Documentation
**Description**: Learning OpenAPI annotation syntax and ensuring all endpoints were properly documented with L5-Swagger.

**Solution**: Systematically added `@OA\` annotations to all controller methods and configured L5-Swagger to scan the app directory. Documentation regenerated using `php artisan l5-swagger:generate`.

**Learning**: Documenting APIs during development ensures accuracy. Swagger UI serves as both documentation and testing tool.

#### Challenge 3: Testing
**Description**: Writing comprehensive feature tests for authentication, CRUD operations, and authorization scenarios.

**Solution**: Used `RefreshDatabase` trait, created factories for test data, used Sanctum's `createToken()` for authentication in tests, and followed Arrange-Act-Assert pattern.

**Learning**: Well-written tests serve as documentation and prevent regressions. Testing authorization is crucial for security.

#### Challenge 4: Deployment
**Description**: Configuring environment variables, database connections, and ensuring production-ready settings for cloud deployment.

**Solution**: Created `railway.json` with build/start commands, documented required environment variables, and configured production settings (APP_DEBUG=false, optimized autoloader).

**Learning**: Understanding environment differences between development and production is essential. Proper configuration ensures successful deployment.

### 6.2 Best Practices Applied

- RESTful API design with proper HTTP methods and status codes
- Input validation using Laravel validation rules
- Comprehensive error handling with meaningful messages
- Security: password hashing (bcrypt), token-based auth, authorization checks
- Code organization: separation of concerns (Controllers, Models, Middleware)
- API documentation: OpenAPI annotations, Swagger UI, Postman collection
- Testing: Feature tests covering authentication, CRUD, and authorization

### 6.3 Future Improvements

- Email verification and password reset functionality
- Rate limiting to prevent abuse
- Caching strategies (Redis/Memcached)
- Additional features: comments, user profiles, categories, search
- Real-time features with WebSockets
- API versioning
- Advanced filtering and sorting
- Soft deletes for data recovery

---

## 7. Conclusion

This project successfully demonstrates a production-ready RESTful API using Laravel 10. All objectives were achieved: secure authentication with Sanctum, comprehensive API documentation, thorough feature testing (11 tests across 3 categories), and deployment-ready configuration.

The project showcases best practices in API design, security, validation, and testing. Challenges in authentication configuration, documentation setup, testing, and deployment were overcome, providing valuable learning experiences in modern web development.

The API serves as a solid foundation for web applications, with proper separation of concerns, security measures, and comprehensive documentation. The knowledge gained is directly applicable to real-world software development scenarios.

---

## 8. References

- Laravel Documentation: https://laravel.com/docs/10.x
- Laravel Sanctum: https://laravel.com/docs/10.x/sanctum
- OpenAPI Specification: https://swagger.io/specification/
- L5-Swagger: https://github.com/DarkaOnLine/L5-Swagger
- PHPUnit: https://phpunit.de/documentation.html
- Railway Deployment: https://docs.railway.app/

---

## 9. Appendices

### Appendix A: Database Dump
Database schema available in `database/migrations/` directory. Generate dump using:
```bash
php artisan migrate
mysqldump -u username -p database_name > database_dump.sql
```

### Appendix B: Postman Collection
Postman collection file: `postman_collection.json` (available in project root)

### Appendix C: GitHub Repository
[Insert GitHub repository link here]

### Appendix D: Deployed API Link
[Insert deployed API URL here after deployment]  
Swagger Documentation: [Insert production Swagger URL]

---

**Report Prepared By**: [Group Name]  
**Total Pages**: 5+
