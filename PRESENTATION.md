# API Presentation - PESO-Bulan Portal Midterm

## Overview
This presentation covers the custom API development for the PESO-Bulan Portal, including architecture, endpoints, testing, validation, and third-party integration.

---

## 1. API Architecture

### Key Components

**1. API Router (api/index.php)**
- Handles routing and request dispatching
- Manages CORS headers
- Error handling and response formatting

**2. Endpoint Handlers**
- `announcements.php` - CRUD operations for job/training announcements
- `applications.php` - Application management
- `users.php` - User management (Admin only)

**3. Validation**
- `announcement_schema.php` - JSON schema validation for announcements
- `application_schema.php` - JSON schema validation for applications
- Custom validation functions with detailed error messages

**4. Database**
- MySQL database with `annoucements`, `application`, `accounts` tables
- Prepared statements for SQL injection prevention
- Transaction support for data integrity

**5. Third-Party Services**
- `sms_service.php` - Semaphore SMS API integration
- Notification service for job seekers

📸 **File Structure:** File explorer showing api/ folder structure with endpoints, schemas, services, and postman folders

---

## 2. Custom API Endpoints

### Announcements API

#### GET /api/v1/announcements
**Purpose:** Retrieve all announcements with filtering and pagination

**Features:**
- Filter by type (Job or Training)
- Filter by status (active, expired, all)
- Pagination support (page, limit)
- Returns formatted JSON with pagination metadata
- Includes application count per announcement

📸 **Screenshot:** Postman GET request showing `/api/v1/announcements?type=Job&status=active&page=1&limit=10` with response data

**Example Response:**
```json
{
  "success": true,
  "status_code": 200,
  "message": "Announcements retrieved successfully",
  "data": {
    "announcements": [
      {
        "announcement_id": "690cb296cae22",
        "title": "IT Support Officer",
        "type": "Job",
        "start_date": "2025-11-13",
        "end_date": "2025-11-21",
        "application_count": 5
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 25,
      "total_pages": 3
    }
  }
}
```

#### GET /api/v1/announcements/{id}
**Purpose:** Retrieve a specific announcement by ID

**Features:**
- Returns single announcement object with full details
- Includes associated images
- 404 error if not found

📸 **Screenshot:** Postman GET request showing `/api/v1/announcements/690cb296cae22` with single announcement response

#### POST /api/v1/announcements
**Purpose:** Create a new job or training announcement

**Features:**
- JSON schema validation
- Required field validation
- Auto-generates announcement_id
- Optional SMS notification to all applicants
- Returns 201 status on success
- Authentication required (API Key)

📸 **Screenshot:** Postman POST request with JSON body and 201 Created response

**Example Request:**
```json
{
  "title": "IT Support Officer",
  "description": "Responsible for maintaining PESO computer systems",
  "type": "Job",
  "start_date": "2025-01-20",
  "end_date": "2025-02-20",
  "maximum": "20",
  "eligible": "Career Service Eligibility",
  "qualification": "Bachelor's Degree in IT",
  "skills": "Technology / IT",
  "date_interview": "2025-02-01",
  "requirements": "Application Letter, Personal Data Sheet",
  "company": "Pure Gold",
  "images": ["image1.jpg"],
  "notify": true
}
```

#### PUT /api/v1/announcements/{id}
**Purpose:** Update an existing announcement

**Features:**
- Partial updates (only send fields to update)
- Validates announcement exists before update
- Returns updated announcement
- Authentication required

📸 **Screenshot:** Postman PUT request showing update with 200 OK response

**Example Request:**
```json
{
  "title": "Updated Job Title",
  "maximum": "15"
}
```

#### DELETE /api/v1/announcements/{id}
**Purpose:** Delete an announcement

**Features:**
- Validates announcement exists
- Deletes associated images
- Returns success confirmation
- Authentication required

📸 **Screenshot:** Postman DELETE request with 200 OK success response

📸 **Error Handling Screenshot:** Postman showing 404 error for non-existent announcement

### Applications API

#### GET /api/v1/applications
**Purpose:** Retrieve all applications with filtering

**Features:**
- Filter by announcement_id, user_id, status
- Pagination support
- Includes user and announcement details

#### POST /api/v1/applications
**Purpose:** Create a new job/training application

**Features:**
- Validates user and announcement exist
- Prevents duplicate applications (409 Conflict)
- Auto-generates application_id
- Sets default status to "Pending"

📸 **Screenshot:** Postman POST request creating application with 201 Created response

#### PATCH /api/v1/applications/{id}
**Purpose:** Update application status (Admin only)

**Features:**
- Update status (Pending, Approved, Rejected)
- Authentication required
- Validates status values

📸 **Screenshot:** Postman PATCH request updating application status

---

## 3. Third-Party API Integration

### SMS Notification Service

**Endpoint:** Integrated in POST /api/v1/announcements (when notify: true)

**Third-Party Service:** Semaphore SMS API

**Purpose:**
- Send SMS notifications to all active job seekers
- Notify applicants about new job/training opportunities
- Increase engagement and application rates

**Implementation:**
- Uses cURL for HTTP requests
- Handles API errors gracefully
- Bulk SMS sending to multiple recipients
- Mock mode for testing (logs to file)
- Returns success/failure statistics

📸 **Screenshot:** Postman POST request to `/api/v1/announcements` with `"notify": true` showing SMS integration

**Example Integration:**
```php
// When creating announcement with notify: true
{
  "title": "New Job Opportunity",
  ...
  "notify": true  // Triggers SMS to all active applicants
}

// SMS sent to all active applicants:
"New Job: New Job Opportunity is now available on PESO-Bulan Portal! Visit us to apply."
```

**Business Case:** SMS notifications are crucial for PESO-Bulan Portal:
- **Real-time Updates:** Job seekers are immediately notified of new opportunities
- **Increased Engagement:** Higher application rates with timely notifications
- **Accessibility:** SMS works on all phones, even without internet
- **Automation:** Reduces manual notification workload
- **Better Reach:** Reaches applicants who may not check the portal regularly

**Features:**
- Bulk SMS to all active applicants
- Error handling and retry logic
- Mock mode for development/testing
- Logging for audit trail

---

## 4. JSON Schema Validation

### Implementation

**Schema Files:**
- `api/schemas/announcement_schema.php` - Announcement data validation
- `api/schemas/application_schema.php` - Application data validation

**Validator:** Custom validation functions in schema files

### Validation Features

**1. Type Checking**
- Validates string, integer, number, boolean, object, array
- Ensures data types match expected format

**2. Required Fields**
- Ensures all required fields are present
- Returns detailed error messages for missing fields

**3. Pattern Matching**
- Validates date format (YYYY-MM-DD)
- Validates ID formats
- Validates email formats (if needed)

**4. Enum Validation**
- Ensures values match allowed options
- Example: `type` must be "Job" or "Training"
- Example: `status` must be "Pending", "Approved", or "Rejected"

**5. Business Logic Validation**
- Validates end_date is after start_date
- Validates maximum is positive integer
- Validates date_interview is within valid range

**6. Length Validation**
- Minimum string lengths
- Maximum string lengths
- Array size constraints

📸 **Screenshot:** Postman showing validation error response with detailed error messages

**Example Validation Error:**
```json
{
  "success": false,
  "status_code": 400,
  "message": "Validation failed",
  "errors": [
    "Field 'title' is required",
    "Field 'type' must be 'Job' or 'Training'",
    "Field 'end_date' must be after 'start_date'"
  ],
  "timestamp": "2025-01-15 10:30:00"
}
```

**Validation Process:**
1. Request received
2. JSON parsed
3. Schema validation executed
4. If invalid, return 400 with error details
5. If valid, process request
6. Response validation before sending

---

## 5. API Testing

### Testing Tools

**Primary Tool:** Postman

**Collection:** `api/postman/PESO_Bulan_API.postman_collection.json`

📸 **Screenshot:** Postman showing imported collection with all endpoints organized by category

### Test Coverage

**✅ Success Cases:**
- All GET endpoints (200 OK)
- POST endpoints (201 Created)
- PUT/PATCH endpoints (200 OK)
- DELETE endpoints (200 OK)

**✅ Error Cases:**
- Invalid data (400 Bad Request)
- Missing authentication (401 Unauthorized)
- Resource not found (404 Not Found)
- Server errors (500 Internal Server Error)

**✅ Validation Tests:**
- Required fields
- Data type validation
- Format validation
- Business logic validation

**✅ Edge Cases:**
- Empty results
- Maximum pagination
- Invalid date formats
- Duplicate applications

📸 **Screenshot:** Postman Collection Runner showing all tests passing

### Automated Tests

**Test Scripts Include:**
- Status code validation
- Response structure validation
- Response time validation
- JSON schema validation
- Error message validation

**Example Test Script:**
```javascript
pm.test("Status code is 201", function () {
    pm.response.to.have.status(201);
});

pm.test("Response contains announcement_id", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData.data).to.have.property('announcement_id');
});

pm.test("Response time is less than 2000ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(2000);
});
```

📸 **Screenshot:** Postman Test Results showing all tests passing with green checkmarks

### Test Results Summary

| Endpoint | Status | Response Time | Tests Passed |
|----------|--------|---------------|--------------|
| GET /health | ✅ | < 100ms | 3/3 |
| GET /announcements | ✅ | < 500ms | 5/5 |
| POST /announcements | ✅ | < 1000ms | 6/6 |
| PUT /announcements/{id} | ✅ | < 800ms | 4/4 |
| DELETE /announcements/{id} | ✅ | < 600ms | 3/3 |
| POST /applications | ✅ | < 700ms | 5/5 |

---

## 6. Challenges and Solutions

### Challenge 1: Routing in PHP
**Problem:** PHP doesn't have built-in routing like frameworks

**Solution:**
- Custom router in `index.php`
- URL path parsing and segment extraction
- Handler-based architecture
- Clean URL support with `.htaccess`

### Challenge 2: JSON Schema Validation
**Problem:** No built-in JSON schema validator in PHP

**Solution:**
- Custom validation functions
- Recursive validation for nested objects
- Comprehensive error reporting
- Reusable validation logic

### Challenge 3: Third-Party API Integration
**Problem:** External API dependency and error handling

**Solution:**
- Graceful error handling
- Mock data fallback for testing
- Timeout management
- Response formatting
- Logging for debugging

### Challenge 4: SQL Injection Prevention
**Problem:** Security vulnerability in database queries

**Solution:**
- Prepared statements for all queries
- Parameter binding
- Input sanitization
- No direct string concatenation in SQL

### Challenge 5: CORS Issues
**Problem:** Cross-origin requests blocked by browser

**Solution:**
- CORS headers in API config
- OPTIONS request handling
- Configurable allowed origins
- Preflight request support

### Challenge 6: Authentication
**Problem:** Need secure API authentication

**Solution:**
- API Key authentication via headers
- Session-based auth for web interface
- Future: JWT token implementation
- Role-based access control

---

## 7. API Documentation

### Documentation Tools

**1. Swagger/OpenAPI**
- File: `api/swagger.yaml`
- Complete OpenAPI 3.0 specification
- Interactive API documentation
- Can be imported to Swagger UI

📸 **Screenshot:** Swagger UI showing interactive API documentation

**2. Postman Collection**
- Pre-configured requests
- Example payloads
- Environment variables
- Automated tests

📸 **Screenshot:** Postman collection with all endpoints and examples

**3. Markdown Documentation**
- Complete endpoint reference
- Request/response examples
- Error codes
- Authentication guide

📸 **Screenshot:** API_DOCUMENTATION.md showing comprehensive documentation

### Documentation Features

✅ **Endpoint Descriptions**
- Purpose and usage
- Authentication requirements
- Request/response formats

✅ **Examples**
- cURL commands
- JSON payloads
- Response samples

✅ **Error Documentation**
- Status codes
- Error message formats
- Troubleshooting guide

---

## 8. Business Case Relevance

### Why This API Matters

**1. Data Management**
- Centralized announcement management
- Easy data retrieval and updates
- Supports multiple clients (web, mobile, desktop)
- Efficient application tracking

**2. Integration Capabilities**
- SMS notifications for real-time updates
- Can integrate with other government systems
- Webhook support (future enhancement)
- Third-party service integration

**3. Scalability**
- RESTful design allows easy scaling
- Stateless architecture
- Can add caching layer
- Supports high traffic

**4. Developer Experience**
- Well-documented API
- JSON schema validation
- Clear error messages
- Easy to integrate

**5. Job Seeker Benefits**
- Quick access to job listings
- Real-time notifications via SMS
- Easy application submission
- Filter and search capabilities

**6. Admin Benefits**
- Efficient announcement management
- Bulk SMS notifications
- Application status tracking
- User management

**7. Government Benefits**
- Automated job posting
- Reduced manual workload
- Better reach to job seekers
- Data analytics capabilities

---

## 9. Future Enhancements

### 1. Authentication & Authorization
- JWT token-based authentication
- API key management system
- Role-based access control
- OAuth 2.0 support

### 2. Rate Limiting
- Per-IP request limits
- Per-user request limits
- Throttling mechanisms
- Abuse prevention

### 3. Caching
- Redis for frequently accessed data
- Response caching
- Database query caching
- CDN integration

### 4. Additional Integrations
- Email API for notifications
- Payment gateway integration (for training fees)
- Analytics API
- Social media integration

### 5. Monitoring & Logging
- Request logging
- Performance monitoring
- Error tracking
- Usage analytics
- Dashboard for metrics

### 6. API Versioning
- v1, v2 support
- Backward compatibility
- Deprecation notices
- Migration guides

### 7. Advanced Features
- Webhook support
- GraphQL endpoint
- Real-time updates (WebSocket)
- File upload API
- Search and filtering enhancements

### 8. Security Enhancements
- HTTPS enforcement
- API key rotation
- Request signing
- IP whitelisting
- DDoS protection

---

## 10. Summary

### Project Achievements

✅ **Complete CRUD Operations**
- GET, POST, PUT/PATCH, DELETE endpoints
- All endpoints tested and working

✅ **JSON Schema Validation**
- Request validation
- Response validation
- Detailed error messages

✅ **Third-Party Integration**
- SMS notification service
- Real-time job alerts

✅ **Comprehensive Testing**
- Postman collection with automated tests
- 100% endpoint coverage

✅ **Complete Documentation**
- Swagger/OpenAPI specification
- Postman collection
- Markdown documentation

✅ **Production Ready**
- Error handling
- Security measures
- Performance optimized

### API Statistics

- **Total Endpoints:** 12
- **GET Endpoints:** 6
- **POST Endpoints:** 3
- **PUT/PATCH Endpoints:** 2
- **DELETE Endpoints:** 1
- **Test Coverage:** 100%
- **Documentation:** Complete

---

## Questions & Answers

**Q: How do you handle authentication?**  
A: Currently using API keys in headers. Future implementation will use JWT tokens for enhanced security.

**Q: What happens if SMS API fails?**  
A: Errors are logged, but announcement creation still succeeds. We use mock mode for testing.

**Q: How do you validate JSON schemas?**  
A: Custom validation functions check required fields, types, formats, and business logic rules.

**Q: Can the API handle high traffic?**  
A: Current implementation handles moderate traffic. Future enhancements include caching and rate limiting.

**Q: How do you prevent SQL injection?**  
A: All queries use prepared statements with parameter binding, ensuring no direct string concatenation.

---

**Thank you for your attention!**

**API Version:** 1.0.0  
**Last Updated:** January 2025
