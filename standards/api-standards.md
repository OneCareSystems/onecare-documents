# OneCare Clinic Management System - API Standards

**Document:** `api-standards.md`  
**Project:** OneCare Clinic Management System  
**Backend:** Spring Boot REST API  
**Database:** MySQL  
**API Style:** RESTful JSON API  
**Base Path:** `/api`

---

## 1. Purpose

This document defines the standards for designing, implementing, documenting, testing, and consuming APIs in the OneCare Clinic Management System.

All backend developers must follow these standards when creating or modifying API endpoints.

### Goals

- Keep APIs consistent across all modules.
- Make APIs predictable for frontend developers.
- Maintain clear separation between controllers, services, repositories, and entities.
- Provide consistent request and response formats.
- Apply authentication and authorization consistently.
- Make APIs easy to test and document.
- Avoid breaking existing frontend integrations.

---

## 2. General API Principles

All APIs must:

1. Follow REST principles where practical.
2. Use JSON for request and response bodies.
3. Use meaningful resource names.
4. Use plural nouns for resources.
5. Use HTTP methods according to their intended purpose.
6. Return appropriate HTTP status codes.
7. Validate incoming data.
8. Never expose database entities directly as API responses.
9. Use DTOs for API requests and responses.
10. Apply authentication and authorization where required.
11. Return errors using the standard error format.
12. Be documented using OpenAPI/Swagger.
13. Be covered by appropriate tests.
14. Avoid exposing sensitive information.

---

## 3. API Base URL

All application APIs must use the `/api` prefix.

```text
/api
```

Examples:

```text
/api/auth
/api/users
/api/patients
/api/appointments
/api/medicines
/api/prescriptions
/api/invoices
```

The environment-specific host and port must not be hardcoded into frontend API services.

Example development URL:

```text
http://localhost:8080/api
```

Production URLs must be configured through environment-specific configuration.

---

## 4. Resource Naming

### 4.1 Use Plural Nouns

Correct:

```text
/api/users
/api/patients
/api/medicines
/api/appointments
/api/prescriptions
```

Incorrect:

```text
/api/user
/api/patient
/api/getPatients
/api/createMedicine
```

The URL identifies the resource. The HTTP method identifies the action.

---

## 5. HTTP Methods

| Method | Purpose | Example |
|---|---|---|
| `GET` | Retrieve data | `GET /api/patients` |
| `POST` | Create a resource | `POST /api/patients` |
| `PUT` | Replace/update a resource | `PUT /api/patients/{id}` |
| `PATCH` | Partially update a resource | `PATCH /api/patients/{id}` |
| `DELETE` | Delete a resource | `DELETE /api/patients/{id}` |

### Do not put CRUD actions in URLs.

Incorrect:

```text
POST /api/patients/create
GET /api/patients/getAll
PUT /api/patients/update/10
DELETE /api/patients/delete/10
```

Correct:

```text
POST /api/patients
GET /api/patients
PUT /api/patients/10
DELETE /api/patients/10
```

---

## 6. Endpoint Structure

Use the following structure:

```text
/api/{resource}
```

For a specific resource:

```text
/api/{resource}/{id}
```

Examples:

```text
GET    /api/patients
GET    /api/patients/{id}
POST   /api/patients
PUT    /api/patients/{id}
DELETE /api/patients/{id}
```

For nested resources, use nesting only when the relationship is meaningful and stable.

Example:

```text
GET /api/patients/{patientId}/appointments
```

Avoid unnecessarily deep URLs:

```text
/api/doctors/{doctorId}/patients/{patientId}/appointments/{appointmentId}
```

Prefer direct resource access where appropriate:

```text
/api/appointments/{appointmentId}
```

---

## 7. Authentication Endpoints

Authentication endpoints are grouped under:

```text
/api/auth
```

Standard endpoints:

```text
POST /api/auth/login
POST /api/auth/refresh
POST /api/auth/logout
```

Example login request:

```json
{
  "username": "doctor01",
  "password": "password"
}
```

Example login response:

```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "accessToken": "eyJ...",
    "refreshToken": "eyJ...",
    "tokenType": "Bearer",
    "expiresIn": 1800
  }
}
```

`expiresIn` is in seconds and must match the 30-minute session timeout defined in the Security Design (SDS §2.5.1). Do not change this value without updating the SDS and coordinating with the frontend team, since the auto-refresh flow (5 minutes before expiry) depends on it.

### Security Requirements

- Passwords must never be returned.
- Password hashes must never be returned.
- Access tokens must not be logged.
- Refresh tokens must not be logged.
- Sensitive authentication information must not appear in error messages.
- Protected endpoints must require a valid authenticated session/token.
- Authorization must be enforced on the backend, not only in the frontend.

---

## 8. Authorization and RBAC

OneCare uses role-based access control.

Current roles include:

```text
SUPER_ADMIN
ADMIN
DOCTOR
PHARMACIST
```

Authorization must be enforced at the API/backend level.

Example:

```java
@PreAuthorize("hasAnyAuthority('ADMIN', 'SUPER_ADMIN')")
@GetMapping
public ResponseEntity<ApiResponse<List<UserResponse>>> getUsers() {
    ...
}
```

Frontend route protection is useful for user experience but **is not a security boundary**.

A user must not gain access to protected functionality simply by manually calling the API.

---

## 9. Request Body Standards

Request bodies must use DTOs.

Example:

```java
public class CreatePatientRequest {

    private String firstName;
    private String lastName;
    private String dateOfBirth;
    private String phoneNumber;
}
```

Do not accept database entities directly:

```java
@PostMapping
public ResponseEntity<?> createPatient(
        @RequestBody Patient patient) {
    ...
}
```

Prefer:

```java
@PostMapping
public ResponseEntity<ApiResponse<PatientResponse>> createPatient(
        @Valid @RequestBody CreatePatientRequest request) {
    ...
}
```

---

## 10. Response DTO Standards

Database entities must not be returned directly from controllers.

Incorrect:

```java
return ResponseEntity.ok(patientRepository.findById(id));
```

Correct:

```text
Entity
   ↓
Service
   ↓
Response DTO
   ↓
Controller
   ↓
JSON Response
```

Example:

```java
public class PatientResponse {

    private Long id;
    private String firstName;
    private String lastName;
    private String phoneNumber;
}
```

Only expose information required by the client.

---

## 11. Standard Success Response

OneCare APIs should use a consistent response wrapper.

Recommended structure:

```json
{
  "success": true,
  "message": "Patient retrieved successfully",
  "data": {
    "id": 101,
    "firstName": "John",
    "lastName": "Perera"
  }
}
```

For lists:

```json
{
  "success": true,
  "message": "Patients retrieved successfully",
  "data": [
    {
      "id": 101,
      "firstName": "John",
      "lastName": "Perera"
    },
    {
      "id": 102,
      "firstName": "Jane",
      "lastName": "Fernando"
    }
  ]
}
```

---

## 12. Standard API Response Structure

The common response wrapper should follow:

```json
{
  "success": true,
  "message": "Human-readable message",
  "data": {}
}
```

Recommended Java structure:

```java
public class ApiResponse<T> {

    private boolean success;
    private String message;
    private T data;
}
```

### Rules

- `success` indicates whether the request was successfully processed.
- `message` provides a human-readable description.
- `data` contains the actual response payload.
- Do not add unnecessary fields to individual endpoints.
- Keep the structure consistent across modules.

---

## 13. Empty Responses

For operations where no response body is necessary, use the appropriate HTTP status.

Example:

```text
DELETE /api/patients/101
```

Response:

```text
204 No Content
```

Do not return unnecessary data simply to populate a response body.

---

## 14. HTTP Status Codes

Use HTTP status codes correctly.

| Status | Meaning | Typical Usage |
|---|---|---|
| `200 OK` | Request successful | GET, PUT, PATCH |
| `201 Created` | Resource created | POST |
| `204 No Content` | Successful request with no body | DELETE |
| `400 Bad Request` | Invalid request | Validation/input errors |
| `401 Unauthorized` | Authentication required/failed | Missing/invalid token |
| `403 Forbidden` | Authenticated but not permitted | RBAC failure |
| `404 Not Found` | Resource does not exist | Invalid ID |
| `409 Conflict` | Resource conflict | Duplicate data |
| `422 Unprocessable Entity` | Semantically invalid data | Complex validation |
| `429 Too Many Requests` | Rate limit exceeded | See §44 Rate Limiting |
| `500 Internal Server Error` | Unexpected server error | Unhandled server failure |

Do not return `200 OK` for every situation.

Bad:

```text
HTTP 200
{
  "success": false,
  "message": "Patient not found"
}
```

Prefer:

```text
HTTP 404
{
  "success": false,
  "message": "Patient not found",
  "data": null
}
```

---

## 15. Error Response Standard

All API errors must use a consistent structure.

Example:

```json
{
  "success": false,
  "message": "Patient not found",
  "data": null
}
```

For validation errors, additional information may be provided:

```json
{
  "success": false,
  "message": "Validation failed",
  "data": {
    "phoneNumber": "Phone number is required",
    "dateOfBirth": "Date of birth must be valid"
  }
}
```

### Never expose

Do not expose:

- Stack traces
- SQL queries
- Database credentials
- Password hashes
- JWT secrets
- Internal filesystem paths
- Internal class names
- Sensitive system configuration
- Debug information in production

---

## 16. Validation

All incoming request data must be validated.

Use Jakarta Bean Validation where appropriate.

Example:

```java
@NotBlank
private String firstName;

@NotBlank
private String lastName;

@NotNull
private LocalDate dateOfBirth;

@Pattern(regexp = "^[0-9+\\- ]+$")
private String phoneNumber;
```

Controller:

```java
public ResponseEntity<ApiResponse<PatientResponse>> createPatient(
        @Valid @RequestBody CreatePatientRequest request) {
    ...
}
```

Validation belongs at the API boundary.

Business rules must still be validated in the service layer.

---

## 17. Path Parameters

Use path parameters when identifying a specific resource.

Example:

```text
GET /api/patients/101
PUT /api/patients/101
DELETE /api/patients/101
```

Example:

```java
@GetMapping("/{id}")
public ResponseEntity<ApiResponse<PatientResponse>> getPatient(
        @PathVariable Long id) {
    ...
}
```

Use meaningful parameter names.

Prefer:

```text
/patients/{patientId}
```

when multiple IDs exist in the same endpoint.

---

## 18. Query Parameters

Use query parameters for filtering, searching, sorting, and pagination.

Examples:

```text
GET /api/patients?search=john
GET /api/patients?status=ACTIVE
GET /api/appointments?doctorId=10
GET /api/medicines?category=ANTIBIOTIC
```

Do not create separate endpoints for every filter.

Bad:

```text
/api/patients/searchByName
/api/patients/searchByPhone
/api/patients/getActivePatients
```

Prefer:

```text
/api/patients?name=John
/api/patients?phone=0771234567
/api/patients?status=ACTIVE
```

---

## 19. Pagination

List endpoints that may return large datasets should support pagination.

Recommended parameters:

```text
?page=0&size=20
```

Example:

```text
GET /api/patients?page=0&size=20
```

Recommended response:

```json
{
  "success": true,
  "message": "Patients retrieved successfully",
  "data": {
    "content": [
      {
        "id": 101,
        "firstName": "John"
      }
    ],
    "page": 0,
    "size": 20,
    "totalElements": 150,
    "totalPages": 8
  }
}
```

### Rules

- Default page size must be defined by the backend.
- Maximum page size must be limited.
- Never allow unlimited database results through a public endpoint.

---

## 20. Sorting

Use query parameters for sorting.

Example:

```text
GET /api/patients?sort=lastName,asc
```

Multiple sorting fields may be supported:

```text
GET /api/patients?sort=lastName,asc&sort=firstName,asc
```

The backend must validate sortable fields rather than blindly passing client input into database queries.

---

## 21. Searching and Filtering

Use query parameters.

Example:

```text
GET /api/medicines?search=paracetamol
```

Multiple filters:

```text
GET /api/medicines?category=PAINKILLER&status=ACTIVE
```

Keep filtering predictable.

Avoid arbitrary query expressions such as:

```text
/api/patients?query=someRawDatabaseExpression
```

---

## 22. Date and Time Standards

Use ISO 8601 formats.

Date:

```text
2026-09-16
```

Date-time:

```text
2026-09-16T09:30:00
```

For timezone-aware timestamps:

```text
2026-09-16T09:30:00+05:30
```

Recommended Java types:

```java
LocalDate
LocalDateTime
OffsetDateTime
```

Do not use ambiguous formats such as:

```text
16/09/2026
09-16-26
```

---

## 23. Boolean Values

Use JSON boolean values.

Correct:

```json
{
  "active": true
}
```

Incorrect:

```json
{
  "active": "true"
}
```

Do not use inconsistent representations such as:

```json
{
  "isActive": 1
}
```

unless a specific external integration requires it.

---

## 24. Naming Conventions

## JSON

Use `camelCase`.

Correct:

```json
{
  "firstName": "John",
  "dateOfBirth": "2000-05-10",
  "phoneNumber": "0771234567"
}
```

Avoid:

```json
{
  "first_name": "John",
  "date_of_birth": "2000-05-10"
}
```

unless an external API requires snake_case.

---

## 25. CRUD API Pattern

Every standard CRUD resource should follow this pattern where applicable.

### Create

```text
POST /api/{resource}
```

### Get all

```text
GET /api/{resource}
```

### Get by ID

```text
GET /api/{resource}/{id}
```

### Update

```text
PUT /api/{resource}/{id}
```

### Partial update

```text
PATCH /api/{resource}/{id}
```

### Delete

```text
DELETE /api/{resource}/{id}
```

Example for patients:

```text
POST   /api/patients
GET    /api/patients
GET    /api/patients/{id}
PUT    /api/patients/{id}
PATCH  /api/patients/{id}
DELETE /api/patients/{id}
```

---

## 26. Domain-Specific Actions

Not every operation fits pure CRUD.

When an actual business action is required, use a clear action endpoint.

Example:

```text
POST /api/appointments/{id}/cancel
POST /api/prescriptions/{id}/approve
POST /api/invoices/{id}/pay
```

Do not create awkward CRUD operations for domain actions.

Bad:

```text
PUT /api/appointments/{id}?status=CANCELLED
```

if cancellation has additional business rules, validation, audit requirements, or side effects.

Prefer:

```text
POST /api/appointments/{id}/cancel
```

The service layer must enforce the business rules for the action.

---

## 27. Prescription API Example

Recommended endpoints:

```text
GET    /api/prescriptions
GET    /api/prescriptions/{id}
POST   /api/prescriptions
PUT    /api/prescriptions/{id}
DELETE /api/prescriptions/{id}
```

Patient-specific prescriptions:

```text
GET /api/patients/{patientId}/prescriptions
```

Doctor-specific prescriptions:

```text
GET /api/doctors/{doctorId}/prescriptions
```

Example creation request:

```json
{
  "patientId": 101,
  "doctorId": 12,
  "notes": "Follow-up after 7 days",
  "items": [
    {
      "medicineId": 45,
      "quantity": 10,
      "dosage": "1 tablet",
      "frequency": "Twice daily",
      "duration": 5
    }
  ]
}
```

The backend must verify that:

- The patient exists.
- The doctor exists.
- The medicines exist.
- The requested quantities are valid.
- The authenticated user is allowed to create the prescription.
- Prescription business rules are satisfied.

---

## 28. Appointment API Example

Recommended endpoints:

```text
GET    /api/appointments
GET    /api/appointments/{id}
POST   /api/appointments
PUT    /api/appointments/{id}
DELETE /api/appointments/{id}
POST   /api/appointments/{id}/cancel
```

Filtering:

```text
GET /api/appointments?doctorId=12
GET /api/appointments?patientId=101
GET /api/appointments?date=2026-09-16
GET /api/appointments?status=SCHEDULED
```

The backend must validate appointment conflicts and relevant business rules.

---

## 29. Medicine API Example

Recommended endpoints:

```text
GET    /api/medicines
GET    /api/medicines/{id}
POST   /api/medicines
PUT    /api/medicines/{id}
DELETE /api/medicines/{id}
```

Filtering:

```text
GET /api/medicines?search=paracetamol
GET /api/medicines?status=ACTIVE
GET /api/medicines?lowStock=true
```

Stock-related operations should use explicit business actions when required.

Example:

```text
POST /api/medicines/{id}/adjust-stock
```

---

## 30. User API Example

Recommended endpoints:

```text
GET    /api/users
GET    /api/users/{id}
POST   /api/users
PUT    /api/users/{id}
DELETE /api/users/{id}
```

Filtering:

```text
GET /api/users?role=DOCTOR
GET /api/users?status=ACTIVE
```

Never return:

```json
{
  "password": "...",
  "passwordHash": "..."
}
```

User response DTOs must deliberately select safe fields.

---

## 31. Controller Standards

Controllers are responsible for:

- Receiving HTTP requests.
- Validating request DTOs.
- Calling the appropriate service.
- Returning HTTP responses.
- Mapping request/response DTOs where appropriate.

Controllers must **not** contain:

- Business logic.
- Complex database queries.
- Password hashing logic.
- JWT generation logic.
- Large validation algorithms.
- Direct repository manipulation unless there is a documented reason.

Example structure:

```java
@RestController
@RequestMapping("/api/patients")
@RequiredArgsConstructor
public class PatientController {

    private final PatientService patientService;

    @GetMapping("/{id}")
    public ResponseEntity<ApiResponse<PatientResponse>> getPatient(
            @PathVariable Long id) {

        return ResponseEntity.ok(
                ApiResponse<>(
                       true,
                       "Patient retrieved successfully",
                       patientService.getPatient(id)
                )
        );
    }
}
```

---

## 32. Service Layer Standards

Business logic belongs in services.

Example:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
Database
```

The service should handle:

- Business validation.
- Transaction management.
- Entity/DTO conversion where appropriate.
- Authorization-related business checks.
- Coordination between repositories.
- Domain actions.

Example:

```java
@Service
@RequiredArgsConstructor
@Transactional
public class PatientService {

    private final PatientRepository patientRepository;

    public PatientResponse createPatient(
            CreatePatientRequest request) {

        // Business logic

        return ...;
    }
}
```

---

## 33. Repository Standards

Repositories are responsible for data access.

Example:

```java
public interface PatientRepository
        extends JpaRepository<Patient, Long> {

    Optional<Patient> findById(Long id);

    boolean existsByPhoneNumber(String phoneNumber);
}
```

Do not put business logic inside repositories.

---

## 34. Transaction Standards

Operations that modify multiple related records must use appropriate transactions.

Example:

```java
@Transactional
public PrescriptionResponse createPrescription(...) {
    ...
}
```

If a prescription creates multiple prescription items, the operation should not leave partially-created data when an unexpected failure occurs.

---

## 35. API Versioning

The initial OneCare API uses:

```text
/api
```

Do not add version numbers unnecessarily.

If a future breaking change requires versioning, use:

```text
/api/v2
```

Do not create versions merely for small bug fixes or backwards-compatible additions.

---

## 36. Breaking Changes

A change is considered potentially breaking if it:

- Removes an endpoint.
- Changes an HTTP method.
- Renames a response field.
- Removes a response field required by clients.
- Changes a field's data type.
- Changes authentication requirements.
- Changes the meaning of an existing field.
- Changes expected request structure.

Before making a breaking change:

1. Identify affected frontend/client code.
2. Update API documentation.
3. Update tests.
4. Coordinate with frontend developers.
5. Update the relevant ticket/PR.
6. Consider whether API versioning is required.

---

## 37. API Documentation

Every public API endpoint must be documented using OpenAPI/Swagger.

Documentation should include:

- HTTP method.
- Endpoint.
- Description.
- Authentication requirements.
- Required roles/permissions.
- Path parameters.
- Query parameters.
- Request body.
- Successful response.
- Error responses.
- Validation requirements.

Example:

```java
@Operation(
    summary = "Get patient by ID",
    description = "Retrieves a patient using the patient's unique ID."
)
@ApiResponses({
    @ApiResponse(
        responseCode = "200",
        description = "Patient retrieved successfully"
    ),
    @ApiResponse(
        responseCode = "404",
        description = "Patient not found"
    )
})
```

Swagger documentation must remain synchronized with the implementation.

---

## 38. Authentication Header

Protected APIs use the Bearer token scheme.

```http
Authorization: Bearer <access-token>
```

Example:

```http
GET /api/patients/101
Authorization: Bearer eyJ...
```

The frontend must not manually attach tokens differently for individual API calls.

Use a centralized Axios interceptor/authentication mechanism.

---

## 39. CORS

CORS configuration must be centralized in backend security/configuration.

Do not add random `@CrossOrigin` annotations to individual controllers unless there is a specific documented requirement.

Development and production origins must be configurable.

Never use unrestricted production CORS such as:

```text
*
```

when authenticated APIs are involved.

---

## 40. Database IDs

API resource IDs should normally be represented as numeric or UUID values according to the entity design.

Example:

```text
GET /api/patients/101
```

Do not expose internal database implementation details unnecessarily.

The API contract should define the ID type clearly.

---

## 41. Idempotency

The API must respect HTTP method semantics.

Generally:

- `GET` should be safe and read-only.
- `PUT` should be idempotent.
- `DELETE` should be idempotent where practical.
- `POST` is normally non-idempotent.

For operations involving payments, external integrations, or other operations where duplicate requests could cause serious consequences, an idempotency strategy should be considered.

---

## 42. Concurrency and Conflicting Updates

When multiple users can modify the same resource, concurrency must be considered.

Examples include:

- Medicine stock.
- Appointments.
- Prescription status.
- Invoice/payment status.

The backend must prevent invalid state transitions and inconsistent updates.

Do not rely only on the frontend to prevent race conditions.

---

## 43. Audit Logging

Security-sensitive and important business operations should be auditable.

Examples:

```text
User creation
Role changes
Patient record modification
Prescription creation
Prescription approval
Medicine stock adjustment
Invoice/payment changes
Account/security changes
```

Audit records should contain appropriate metadata such as:

```text
actor
action
resource
resourceId
timestamp
```

Do not store passwords, tokens, or other secrets in audit logs.

---

## 44. Rate Limiting

Rate limits must match the values defined in the Security Design (SDS §2.5.4) and be enforced at the API layer, not assumed from documentation alone.

| Endpoint Category | Limit | Enforcement Mechanism |
|---|---|---|
| Authentication endpoints (`/api/auth/*`) | 10 requests per minute per IP | Security filters with distributed counters |
| Public booking endpoints (`/api/appointments` self-service, `/api/chatbot/query`) | 20 requests per minute per IP | API gateway rate limiting |
| General API endpoints (all other authenticated routes) | 100 requests per minute per user | API gateway rate limiting |

Rules:

- A request that exceeds its limit must return `429 Too Many Requests` using the standard error response format (§15).
- Rate limit counters are scoped per IP for unauthenticated endpoints and per authenticated user for general endpoints — do not mix the two scopes.
- Do not invent ad-hoc limits per endpoint; extend this table (and the SDS) first if a new category is needed.

---

## 45. API Security Rules

Never:

- Trust frontend authorization.
- Return passwords.
- Return JWT secrets.
- Log access/refresh tokens.
- Accept arbitrary SQL from clients.
- Build SQL queries using raw user input.
- Disable authentication to make development easier.
- Expose stack traces in production.
- Return unnecessary patient information.

Always:

- Validate input.
- Authenticate protected requests.
- Authorize protected operations.
- Use parameterized/JPA queries.
- Apply least-privilege access.
- Sanitize sensitive logs.
- Use HTTPS in production.

---

## 46. Sensitive Patient Data

Patient-related APIs must follow data-minimization principles.

Only return fields required for the requested operation.

For example, a patient list endpoint should not automatically return every field stored in the patient database.

Prefer:

```json
{
  "id": 101,
  "firstName": "John",
  "lastName": "Perera",
  "phoneNumber": "0771234567"
}
```

over exposing unrelated clinical or administrative information.

Detailed patient information should require appropriate authorization.

---

## 47. API Testing

Every API module must have appropriate tests.

At minimum, test:

### Success Cases

- Create resource.
- Retrieve resource.
- Update resource.
- Delete resource.
- Retrieve list.

### Validation Cases

- Missing required fields.
- Invalid field values.
- Invalid IDs.

### Security Cases

- Unauthenticated request.
- Unauthorized role.
- Authorized role.
- Invalid/expired token where applicable.
- Rate limit exceeded (§44), where applicable.

### Business Cases

- Resource not found.
- Duplicate resource.
- Invalid state transition.
- Business-rule violation.

Example:

```text
POST /api/patients
```

Tests should verify:

```text
201 Created
400 Bad Request
401 Unauthorized
403 Forbidden
409 Conflict
```

where those cases apply.

---

## 48. Integration Testing

Important API flows should be tested against the application context and database.

Examples:

```text
Login
    ↓
Receive JWT
    ↓
Call protected endpoint
    ↓
Validate authorization
    ↓
Read/write database
    ↓
Return response
```

For OneCare, authentication, RBAC, prescription creation, appointment management, and medicine stock operations should have integration coverage where applicable.

---

## 49. Frontend Integration Rules

Frontend developers must consume APIs according to the documented contract.

Do not:

- Duplicate API URLs throughout components.
- Hardcode tokens in components.
- Manually construct authentication headers repeatedly.
- Assume a successful HTTP response means business success without checking the response contract.
- Depend on undocumented fields.

Prefer:

```text
React Component
      ↓
API Service
      ↓
Axios Instance
      ↓
Spring Boot API
```

Example:

```text
src/
└── services/
    ├── apiClient.js
    ├── authService.js
    ├── patientService.js
    ├── appointmentService.js
    ├── medicineService.js
    └── prescriptionService.js
```

---

## 50. API Client Rules

The frontend should have one centralized HTTP client configuration.

Example:

```javascript
const apiClient = axios.create({
    baseURL: import.meta.env.VITE_API_BASE_URL
});
```

Authentication headers should be handled centrally through an interceptor rather than manually inside every service.

---

## 51. API Naming Checklist

Before creating an endpoint, verify:

- [ ] Is the resource name a plural noun?
- [ ] Is the URL under `/api`?
- [ ] Is the HTTP method correct?
- [ ] Is the endpoint RESTful?
- [ ] Does the endpoint already exist?
- [ ] Can an existing endpoint/query parameter solve the requirement?
- [ ] Is the request DTO defined?
- [ ] Is the response DTO defined?
- [ ] Is validation implemented?
- [ ] Is authentication required?
- [ ] Is authorization implemented?
- [ ] Is rate limiting applied per §44?
- [ ] Is the correct HTTP status returned?
- [ ] Is the error response consistent?
- [ ] Is Swagger/OpenAPI documentation updated?
- [ ] Are tests included?

---

## 52. API Implementation Checklist

Before opening a Pull Request:

### Endpoint

- [ ] Correct URL.
- [ ] Correct HTTP method.
- [ ] Correct status codes.
- [ ] No unnecessary action names.
- [ ] Consistent naming.

### Request

- [ ] Request DTO created.
- [ ] Validation added.
- [ ] Invalid input handled.

### Response

- [ ] Response DTO created.
- [ ] Entity is not exposed directly.
- [ ] Standard `ApiResponse` used.
- [ ] Sensitive fields excluded.

### Security

- [ ] Authentication requirement defined.
- [ ] Authorization/RBAC implemented.
- [ ] No sensitive information exposed.
- [ ] Rate limiting applied where required.
- [ ] Security tests added.

### Backend

- [ ] Controller contains no business logic.
- [ ] Service contains business logic.
- [ ] Repository handles persistence.
- [ ] Transactions are correctly applied.

### Documentation

- [ ] Swagger/OpenAPI updated.
- [ ] Request examples added where useful.
- [ ] Response examples added where useful.
- [ ] Error responses documented.

### Testing

- [ ] Unit tests added/updated.
- [ ] Integration tests added where required.
- [ ] Authentication tests added where required.
- [ ] Authorization tests added where required.
- [ ] Validation/error cases tested.

---

## 53. Pull Request API Review Checklist

Reviewers should verify:

```text
API Design
├── Resource naming
├── HTTP method
├── URL structure
└── Query/path parameters

Request
├── DTO
├── Validation
└── Input handling

Response
├── Response DTO
├── ApiResponse
├── HTTP status
└── Sensitive data protection

Security
├── Authentication
├── Authorization
├── RBAC
├── Rate limiting
└── Sensitive logging

Architecture
├── Controller
├── Service
├── Repository
└── Transaction boundaries

Testing
├── Success
├── Validation
├── Error
├── Authentication
└── Authorization

Documentation
└── Swagger/OpenAPI
```

---

## 54. Standard OneCare API Structure

The expected API structure is:

```text
/api
│
├── /auth
│   ├── POST /login
│   ├── POST /refresh
│   └── POST /logout
│
├── /users
│   ├── GET /
│   ├── GET /{id}
│   ├── POST /
│   ├── PUT /{id}
│   └── DELETE /{id}
│
├── /patients
│   ├── GET /
│   ├── GET /{id}
│   ├── POST /
│   ├── PUT /{id}
│   └── DELETE /{id}
│
├── /appointments
│   ├── GET /
│   ├── GET /{id}
│   ├── POST /
│   ├── PUT /{id}
│   ├── DELETE /{id}
│   └── POST /{id}/cancel
│
├── /queue                                    (FR019–021, UC13)
│   ├── GET /                                 — today's queue, optional ?date=
│   ├── POST /{appointmentId}/arrive          — mark patient arrived
│   ├── POST /{appointmentId}/complete        — mark consultation completed
│   └── POST /{appointmentId}/no-show         — mark patient no-show
│
├── /medicines
│   ├── GET /
│   ├── GET /{id}
│   ├── POST /
│   ├── PUT /{id}
│   ├── DELETE /{id}
│   └── POST /{id}/adjust-stock
│
├── /prescriptions
│   ├── GET /
│   ├── GET /{id}
│   ├── POST /
│   ├── PUT /{id}
│   └── DELETE /{id}
│
├── /external-dispensing                      (FR048–052, UC30–32)
│   ├── POST /                                — mark a prescription for external dispensing
│   ├── GET /{id}
│   ├── POST /{id}/verify-identity            — phone / clinic ID / QR verification
│   └── POST /{id}/complete                   — finalize dispense, deduct stock, create delivery record
│
├── /invoices
│   ├── GET /
│   ├── GET /{id}
│   ├── POST /
│   ├── PUT /{id}
│   └── DELETE /{id}
│
├── /chatbot                                  (FR043–047, UC28–29)
│   ├── POST /query                           — patient-facing chatbot interaction (public, rate-limited)
│   ├── GET /knowledge-base                   — list approved knowledge-base documents
│   ├── POST /knowledge-base                  — upload a doctor-approved PDF (Doctor/Super Admin only)
│   └── DELETE /knowledge-base/{id}           — remove a knowledge-base document
│
├── /reports                                  (FR039–041, UC24–25)
│   ├── GET /clinic                           — patient/consultation/queue statistics, ?from=&to=
│   └── GET /pharmacy                         — stock usage, expiry, dispensing history, ?from=&to=
│
└── /audit-logs                               (FR041–042, UC26)
    └── GET /                                 — filterable audit trail, Super Admin only, paginated
```

This is the **baseline structure**, not a requirement to implement every endpoint immediately. The five modules added above (`queue`, `external-dispensing`, `chatbot`, `reports`, `audit-logs`) correspond to functional requirements marked "Fully Met" in the Final Design Report and must not be freelanced into ad-hoc shapes when their tickets come up — follow the naming and action pattern shown here.

Notes on the added modules:

- **`/queue`** actions follow the domain-action pattern from §26 rather than `PUT /queue/{id}?status=...`, since each transition (arrive / complete / no-show) has distinct business rules and audit requirements per UC13.
- **`/external-dispensing`** is a separate resource from `/prescriptions` because UC30–32 describe a three-step workflow (mark → verify identity → complete) with its own audit trail (FR052), not a simple status field on the prescription.
- **`/chatbot/query`** is a public endpoint and falls under the **public booking endpoint** rate limit tier in §44 (20 requests/min/IP), not the general API tier.
- **`/reports`** and **`/audit-logs`** are read-only aggregation endpoints — do not add POST/PUT/DELETE unless a future requirement introduces report scheduling or log annotation.

---

## 55. Definition of Done for an API Endpoint

An API endpoint is considered complete only when:

```text
[ ] Endpoint implemented
[ ] Request DTO implemented
[ ] Response DTO implemented
[ ] Validation implemented
[ ] Service logic implemented
[ ] Repository/database logic implemented where required
[ ] Authentication configured
[ ] Authorization configured
[ ] Rate limiting configured where applicable
[ ] Correct HTTP status codes implemented
[ ] Standard error handling implemented
[ ] Swagger/OpenAPI documentation updated
[ ] Unit tests completed
[ ] Integration/security tests completed where required
[ ] Frontend contract verified
[ ] Code reviewed
[ ] CI pipeline passes
```

---

## 56. Final Rule

**Consistency beats cleverness.**

Before creating a new API endpoint, first ask:

> "Can this requirement be represented using an existing resource, HTTP method, path parameter, query parameter, or domain action?"

If yes, reuse the existing API design.

If no, create the smallest clear endpoint that follows this standard.

Every developer is responsible for keeping the API contract consistent across the OneCare system.