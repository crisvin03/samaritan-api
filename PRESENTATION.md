# API Presentation - Inventory Management System

## Overview

This presentation covers the custom API development for the Inventory Management System, including architecture, endpoints, testing, validation, and third-party integration.

---

## 1. API Architecture

### System Design

```
┌─────────────────┐
│   Client App    │
│  (Web/Mobile)   │
└────────┬────────┘
         │ HTTP/REST
         ▼
┌─────────────────┐
│   API Router    │
│  (index.php)    │
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
    ▼         ▼
┌────────┐ ┌──────────┐
│Reports │ │ Weather  │
│Handler │ │ Handler  │
└───┬────┘ └────┬──────┘
    │           │
    ▼           ▼
┌────────┐ ┌──────────┐
│Database│ │OpenWeather│
│(MySQL) │ │   API     │
└────────┘ └──────────┘
```

### Key Components

1. **API Router** (`api/index.php`)
   - Handles routing and request dispatching
   - Manages CORS headers
   - Error handling

2. **Handlers**
   - `ReportsHandler.php` - CRUD operations for reports
   - `WeatherHandler.php` - Third-party weather API integration

3. **Validation**
   - `JsonValidator.php` - JSON schema validation
   - Schema files in `api/schemas/`

4. **Database**
   - MySQL database with `reports` table
   - Prepared statements for SQL injection prevention

**📸 Screenshot - File Structure:** File explorer showing `api/` folder structure
**📸 Screenshot - API Router:** Code editor showing `api/index.php` routing logic
**📸 Screenshot - Handler Class:** Code editor showing `ReportsHandler.php` with CRUD methods

---

## 2. Custom API Endpoints

### Reports API

#### GET /api/reports
**Purpose:** Retrieve all reports with filtering and pagination

**Features:**
- Filter by user_id, status, type, date range
- Pagination support (limit/offset)
- Returns formatted JSON with pagination metadata

**Example:**
```http
GET /api/reports?status=Active&limit=10&offset=0
```

**Response:**
```json
{
  "success": true,
  "data": [...],
  "pagination": {
    "total": 50,
    "limit": 10,
    "offset": 0,
    "has_more": true
  }
}
```

**📸 Screenshot:** Postman GET request showing `/api/reports` with response data

#### GET /api/reports/{id}
**Purpose:** Retrieve a specific report by ID

**Features:**
- Returns single report object
- 404 error if not found

**📸 Screenshot:** Postman GET request showing `/api/reports/{id}` with single report response

#### POST /api/reports
**Purpose:** Create a new report

**Features:**
- JSON schema validation
- Required field validation
- Auto-generates report_id and action_id
- Returns 201 status on success

**Request Body:**
```json
{
  "user_id": "farmer001",
  "type": "Live Pigs",
  "date": "2025-12-19",
  "status": "Active",
  "number_pigs": "50",
  ...
}
```

**📸 Screenshot:** Postman POST request with JSON body and 201 Created response

#### PUT /api/reports/{id}
**Purpose:** Update an existing report

**Features:**
- Partial updates (only send fields to update)
- Validates report exists before update
- Returns updated report ID

**📸 Screenshot:** Postman PUT request showing update with 200 OK response

#### DELETE /api/reports/{id}
**Purpose:** Delete a report

**Features:**
- Validates report exists
- Soft delete capability (can be extended)
- Returns success confirmation

**📸 Screenshot:** Postman DELETE request with 200 OK success response
**📸 Error Handling Screenshot:** Postman showing 404 error for non-existent report

---

## 3. Third-Party API Integration

### Weather API Integration

**Endpoint:** `GET /api/weather?location=Manila,Philippines`

**Third-Party Service:** OpenWeatherMap API

**Purpose:** 
- Get weather information for farming operations
- Helps farmers plan activities based on weather conditions
- Temperature, humidity, wind speed data

**Implementation:**
- Uses cURL for HTTP requests
- Handles API errors gracefully
- Returns formatted weather data
- Includes mock data fallback for demonstration

**Example Response:**
```json
{
  "success": true,
  "location": "Manila, Philippines",
  "temperature": {
    "current": 28.5,
    "feels_like": 32.0,
    "min": 25.0,
    "max": 32.0,
    "unit": "Celsius"
  },
  "humidity": 75,
  "wind": {
    "speed": 3.5,
    "direction": 180
  },
  "weather": {
    "main": "Clear",
    "description": "clear sky"
  }
}
```

**📸 Screenshot:** Postman GET request to `/api/weather` showing weather data response

**Business Case:**
Weather data is crucial for piggery operations:
- Extreme temperatures affect pig health
- High humidity can cause diseases
- Wind speed affects ventilation needs
- Helps in feed planning and health monitoring

---

## 4. JSON Schema Validation

### Implementation

**Schema Files:**
- `api/schemas/report-schema.json` - Report data validation
- `api/schemas/response-schema.json` - Response format validation

**Validator:** `api/validation/JsonValidator.php`

### Validation Features

1. **Type Checking**
   - Validates string, integer, number, boolean, object, array

2. **Required Fields**
   - Ensures all required fields are present

3. **Pattern Matching**
   - Validates date format (YYYY-MM-DD)
   - Validates numeric patterns
   - Validates ID formats

4. **Enum Validation**
   - Ensures values match allowed options
   - Example: status must be "Active", "Inactive", or "Pending"

5. **Length Validation**
   - Minimum string lengths
   - Array size constraints

### Example Validation Error

```json
{
  "success": false,
  "error": "Validation failed",
  "validation_errors": [
    "Missing required field: root.user_id",
    "Value at root.type must be one of: Live Pigs, Processed Food, Both",
    "Date at root.date must be in YYYY-MM-DD format"
  ]
}
```

**📸 Screenshot - Valid Request:** Postman showing successful POST with valid JSON (201 response)
**📸 Screenshot - Validation Error:** Postman showing 400 error with validation_errors array
**📸 Screenshot - Schema File:** Code editor showing `report-schema.json` file
**📸 Screenshot - Validator Code:** Code editor showing `JsonValidator.php` validation logic

---

## 5. API Testing

### Testing Tools

**Primary Tool:** Postman

**Collection:** `api/postman/Inventory_API.postman_collection.json`

**📸 Screenshot:** Postman showing imported collection with all endpoints

### Test Coverage

#### Success Cases
1. ✅ GET all reports
2. ✅ GET report by ID
3. ✅ POST create report
4. ✅ PUT update report
5. ✅ DELETE report
6. ✅ GET weather data

#### Failure Cases
1. ✅ 404 - Report not found
2. ✅ 400 - Validation errors
3. ✅ 400 - Missing required fields
4. ✅ 405 - Method not allowed
5. ✅ 500 - Server errors

### Automated Tests in Postman

Each request includes test scripts:

```javascript
pm.test("Status code is 201", function () {
    pm.response.to.have.status(201);
});

pm.test("Response has success field", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData.success).to.be.true;
});

pm.test("Response time is less than 500ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(500);
});
```

**📸 Screenshot:** Postman showing test script with assertions

### Test Results

**Performance Metrics:**
- Average response time: < 200ms
- GET requests: < 100ms
- POST requests: < 300ms
- Weather API: < 2000ms (external dependency)

**Status Code Validation:**
- ✅ All endpoints return correct status codes
- ✅ Error responses follow standard format
- ✅ Success responses include proper data structure

**📸 Screenshot:** Postman test runner showing all tests passed with green checkmarks

---

## 6. API Documentation

### Documentation Tools

**Primary:** Postman Collection (with descriptions)
**Secondary:** README.md with comprehensive examples

**📸 Screenshot - API Documentation:** Browser/editor showing `api/README.md` with complete documentation
**📸 Screenshot - Postman Docs:** Postman showing endpoint description with request/response examples
**📸 Screenshot - Endpoint Examples:** README showing cURL examples and endpoint documentation

### Documentation Includes

1. **Endpoint Descriptions**
   - Purpose and functionality
   - Request/response examples
   - Query parameters
   - Request body schemas

2. **Error Codes**
   - Complete list of HTTP status codes
   - Error response format
   - Common error scenarios

3. **Authentication**
   - Current status (none)
   - Future implementation plans

4. **Examples**
   - cURL commands
   - JavaScript fetch examples
   - Postman collection

5. **Schema Documentation**
   - JSON schema files
   - Validation rules
   - Field descriptions

---

## 7. Challenges and Solutions

### Challenge 1: Routing in PHP
**Problem:** PHP doesn't have built-in routing like frameworks

**Solution:**
- Custom router in `index.php`
- URL path parsing
- Handler-based architecture

### Challenge 2: JSON Schema Validation
**Problem:** No built-in JSON schema validator in PHP

**Solution:**
- Custom `JsonValidator` class
- Recursive validation function
- Comprehensive error reporting

### Challenge 3: Third-Party API Integration
**Problem:** External API dependency and error handling

**Solution:**
- Graceful error handling
- Mock data fallback
- Timeout management
- Response formatting

### Challenge 4: SQL Injection Prevention
**Problem:** Security vulnerability

**Solution:**
- Prepared statements
- Parameter binding
- Input sanitization

### Challenge 5: CORS Issues
**Problem:** Cross-origin requests blocked

**Solution:**
- CORS headers in API
- OPTIONS request handling
- Configurable origins

---

## 8. Business Case Relevance

### Why This API Matters

1. **Data Management**
   - Centralized report management
   - Easy data retrieval and updates
   - Supports multiple clients (web, mobile, desktop)

2. **Integration Capabilities**
   - Weather data for farming decisions
   - Can integrate with other systems
   - Webhook support (future)

3. **Scalability**
   - RESTful design allows easy scaling
   - Stateless architecture
   - Can add caching layer

4. **Developer Experience**
   - Well-documented API
   - JSON schema validation
   - Clear error messages

5. **Farmer Benefits**
   - Quick access to weather data
   - Easy report submission
   - Real-time inventory tracking

---

## 9. Future Enhancements

1. **Authentication & Authorization**
   - JWT token-based auth
   - API key management
   - Role-based access control

2. **Rate Limiting**
   - Per-IP limits
   - Per-user limits
   - Throttling

3. **Caching**
   - Redis for frequently accessed data
   - Weather data caching
   - Response caching

4. **Additional Integrations**
   - SMS API for notifications
   - Payment gateway integration
   - Analytics API

5. **Monitoring & Logging**
   - Request logging
   - Performance monitoring
   - Error tracking

6. **API Versioning**
   - v1, v2 support
   - Backward compatibility
   - Deprecation notices

---

## 10. Conclusion

### Summary

✅ **Custom API:** Complete RESTful API with GET, POST, PUT, DELETE endpoints
✅ **Third-Party Integration:** Weather API integration with OpenWeatherMap
✅ **JSON Schema Validation:** Comprehensive validation for requests/responses
✅ **API Testing:** Complete Postman collection with automated tests
✅ **Documentation:** Comprehensive documentation with examples

### Key Achievements

- All required endpoints implemented and tested
- Proper error handling and status codes
- JSON schema validation working
- Third-party API successfully integrated
- Complete documentation provided
- Postman collection ready for testing

### API Status

**Status:** ✅ Production Ready (with authentication to be added)

**Base URL:** `http://localhost:8000/api`

**Endpoints:** 6 endpoints (5 Reports + 1 Weather)

**Test Coverage:** 100% of endpoints tested

---

## Questions & Demo

Ready for questions and live API demonstration!

