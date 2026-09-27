# OneCare Clinic Management System — Coding Standards

**Project:** OneCare Clinic Management System  
**Document:** Coding Standards  
**Version:** 1.0  
**Status:** Active  
**Applies To:** Backend, Frontend, Database, API, Testing, Git, and Infrastructure

---

## 1. Purpose

This document defines the coding and development standards for the OneCare Clinic Management System.

The main goals are:

- Keep the code consistent across all team members.
- Make the code easy to read and maintain.
- Reduce bugs caused by inconsistent implementation.
- Make team collaboration easier.
- Ensure backend and frontend code follow the same architectural principles.
- Make code reviews faster and more effective.
- Maintain security and quality standards.

All team members must follow these standards when creating or modifying code.

---

# 2. General Principles

All developers should follow these principles:

### 2.1 Keep It Simple

Prefer simple and understandable code over unnecessarily complex solutions.

### 2.2 Do Not Repeat Yourself

Avoid duplicated business logic.

If the same logic is required in multiple places, consider creating a reusable method, service, component, or utility.

### 2.3 Single Responsibility

Each class, method, component, or module should have one clear responsibility.

Bad:

```java
public void createPatientAndSendEmailAndGenerateInvoice() {
    // Too many responsibilities
}
```

Better:

```java
createPatient();
sendRegistrationEmail();
generateInvoice();
```

### 2.4 Do Not Over-Engineer

Do not introduce additional frameworks, patterns, libraries, or abstractions unless they solve an actual project requirement.

### 2.5 Readability Over Cleverness

Code should be understandable by another team member without requiring extensive explanation.

---

# 3. Project Architecture

OneCare follows a layered architecture.

## Backend

The backend should follow:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
Database
```

Supporting layers may include:

```text
Controller
Service
Repository
Entity
DTO
Mapper
Security
Exception
Configuration
```

### Controller

Responsible for:

- Receiving HTTP requests.
- Validating request input.
- Calling services.
- Returning HTTP responses.

Controllers must not contain business logic.

### Service

Responsible for:

- Business logic.
- Business validation.
- Coordinating repositories and other services.
- Transaction management where required.

### Repository

Responsible for:

- Database access.
- Query operations.
- Persistence operations.

Repositories must not contain business logic.

### Entity

Responsible for:

- Representing database entities.
- Defining relationships between entities.

Entities should not be used directly as API request/response objects unless there is a specific reason.

### DTO

DTOs should be used to transfer data between the API and client.

Examples:

```text
CreatePatientRequest
UpdatePatientRequest
PatientResponse
LoginRequest
LoginResponse
```

---

# 4. Backend Coding Standards

## 4.1 Technology

The OneCare backend uses:

- Java
- Spring Boot
- Spring Security
- Spring Data JPA
- MySQL
- Maven

Follow the versions defined by the project's build configuration.

---

## 4.2 Package Naming

Package names must use lowercase.

Correct:

```text
com.onecare.backend.controller
com.onecare.backend.service
com.onecare.backend.repository
```

Incorrect:

```text
com.onecare.backend.Controller
com.onecare.backend.Services
```

---

## 4.3 Class Naming

Use `PascalCase`.

Correct:

```java
PatientController
PatientService
PatientRepository
PatientResponse
CreatePatientRequest
```

Avoid:

```java
patientController
patient_service
PATIENTSERVICE
```

---

## 4.4 Method Naming

Use `camelCase`.

Correct:

```java
createPatient()
getPatientById()
updatePatient()
deletePatient()
findAvailableMedicine()
```

Methods should use meaningful names.

Avoid:

```java
doIt()
process()
handle()
test()
data()
```

unless the meaning is obvious from context.

---

## 4.5 Variable Naming

Use `camelCase`.

Correct:

```java
patientId
patientName
appointmentDate
prescriptionItems
```

Avoid:

```java
patient_id
PatientName
PID
data1
x
```

Use short variable names only when their meaning is obvious.

---

# 5. Controller Standards

Controllers must be thin.

Example:

```java
@RestController
@RequestMapping("/api/patients")
public class PatientController {

    private final PatientService patientService;

    @GetMapping("/{id}")
    public ResponseEntity<ApiResponse<PatientResponse>> getPatient(
            @PathVariable Long id) {

        return ResponseEntity.ok(
                ApiResponse.success(patientService.getPatientById(id))
        );
    }
}
```

### Controllers must:

- Define API routes.
- Receive request data.
- Perform request-level validation.
- Call services.
- Return appropriate HTTP responses.

### Controllers must NOT:

- Directly access repositories.
- Contain complex business logic.
- Perform database queries.
- Contain large calculations or workflows.

Bad:

```java
@GetMapping("/{id}")
public Patient getPatient(@PathVariable Long id) {
    return patientRepository.findById(id).orElseThrow();
}
```

Prefer:

```text
Controller → Service → Repository
```

---

# 6. Service Standards

Business logic belongs in services.

Example:

```java
@Service
public class PatientService {

    public PatientResponse createPatient(CreatePatientRequest request) {

        // Validate business rules

        // Create entity

        // Save entity

        // Return response
    }
}
```

Services should:

- Validate business rules.
- Coordinate repositories.
- Handle transactions.
- Convert entities to DTOs where required.
- Throw appropriate business exceptions.

Avoid placing business logic inside controllers.

---

# 7. Repository Standards

Repositories should focus only on data access.

Example:

```java
public interface PatientRepository
        extends JpaRepository<Patient, Long> {

    Optional<Patient> findByNic(String nic);

    boolean existsByNic(String nic);
}
```

Use Spring Data query methods where practical.

Create custom queries only when necessary.

---

# 8. DTO Standards

Do not expose unnecessary entity fields through the API.

Use separate DTOs for different operations.

Example:

```text
CreatePatientRequest
UpdatePatientRequest
PatientResponse
PatientSummaryResponse
```

Do not create one large DTO containing every possible field for every operation.

---

# 9. API Standards

All APIs should follow a consistent REST structure.

Base path:

```text
/api
```

Examples:

```text
GET    /api/patients
GET    /api/patients/{id}
POST   /api/patients
PUT    /api/patients/{id}
DELETE /api/patients/{id}
```

Use plural resource names.

Correct:

```text
/api/patients
/api/appointments
/api/medicines
/api/prescriptions
```

Avoid:

```text
/api/getPatient
/api/createPatient
/api/deletePatient
```

---

# 10. HTTP Status Codes

Use appropriate HTTP status codes.

| Status | Usage |
|---|---|
| `200 OK` | Successful request |
| `201 Created` | Resource successfully created |
| `204 No Content` | Successful request with no response body |
| `400 Bad Request` | Invalid request |
| `401 Unauthorized` | Authentication required/failed |
| `403 Forbidden` | User does not have permission |
| `404 Not Found` | Resource does not exist |
| `409 Conflict` | Resource/state conflict |
| `500 Internal Server Error` | Unexpected server error |

Do not return `200 OK` for every situation.

---

# 11. API Response Format

The API should use a consistent response structure.

Example:

```json
{
  "success": true,
  "message": "Patient created successfully",
  "data": {
    "id": 101,
    "name": "John Doe"
  }
}
```

Error example:

```json
{
  "success": false,
  "message": "Patient not found",
  "data": null
}
```

The exact structure must remain consistent throughout the backend.

---

# 12. Exception Handling

Do not expose internal exceptions or stack traces to clients.

Use centralized exception handling.

Example:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    
}
```

Use meaningful custom exceptions where appropriate.

Examples:

```text
PatientNotFoundException
MedicineNotFoundException
AppointmentNotFoundException
UnauthorizedException
BusinessException
```

Avoid:

```java
throw new RuntimeException("Something went wrong");
```

when a more meaningful exception can be used.

---

# 13. Validation

Validate incoming data before processing it.

Example:

```java
@NotBlank
private String name;

@NotNull
private LocalDate dateOfBirth;

@Email
private String email;
```

Use Jakarta Bean Validation where appropriate.

Common annotations:

```text
@NotNull
@NotBlank
@NotEmpty
@Email
@Size
@Min
@Max
@Pattern
```

Validation should exist at the appropriate layer.

---

# 14. Security Standards

Security-related code must receive additional review.

## 14.1 Authentication

Authentication must be handled through the project's configured authentication mechanism.

Do not manually implement authentication in individual controllers.

## 14.2 Authorization

Use the project's RBAC and permission system.

Example:

```java
@PreAuthorize("hasAuthority('PATIENT_READ')")
```

Do not rely only on frontend role restrictions.

Frontend restrictions improve UX, but backend authorization is mandatory.

## 14.3 Passwords

Passwords must never be stored as plain text.

Use the project's approved password hashing mechanism.

Never log:

```text
password
passwordHash
JWT
refreshToken
```

---

# 15. Logging Standards

Use the project's logging framework instead of `System.out.println()`.

Avoid:

```java
System.out.println("Patient created");
```

Use:

```java
log.info("Patient created successfully: {}", patientId);
```

Do not log sensitive information.

Never log:

- Passwords
- JWT tokens
- Refresh tokens
- Sensitive patient information
- Authentication credentials

---

# 16. Database Standards

## 16.1 Table Naming

Use consistent naming.

Preferred:

```text
users
patients
medicines
appointments
prescriptions
prescription_items
audit_log
```

Use lowercase snake_case for database tables and columns.

---

## 16.2 Primary Keys

Use the project's standard ID strategy consistently.

Do not create different ID strategies without a technical reason.

---

## 16.3 Relationships

Use appropriate JPA relationships:

```text
@OneToOne
@OneToMany
@ManyToOne
@ManyToMany
```

Avoid unnecessary bidirectional relationships because they can create serialization and performance problems.

---

## 16.4 Database Changes

Database schema changes must be documented and reviewed.

Do not manually modify the shared database and assume the change is complete.

Schema changes must be reproducible for other developers and environments.

---

# 17. Frontend Coding Standards

## 17.1 Technology

The OneCare frontend uses:

- React
- JavaScript
- Vite
- React Router
- Axios
- Redux Toolkit where required
- Tailwind CSS where applicable

---

## 17.2 Component Naming

Use `PascalCase`.

Correct:

```text
PatientTable.jsx
PatientForm.jsx
AppointmentCard.jsx
Dashboard.jsx
```

Avoid:

```text
patienttable.jsx
patient_form.jsx
dashboardcomponent.jsx
```

---

## 17.3 Component Structure

Keep components focused.

Example:

```jsx
function PatientList() {
    // state
    // hooks
    // handlers
    // rendering
}
```

Do not create extremely large components.

If a component becomes difficult to understand, split it into smaller components.

---

# 18. React Rules

## 18.1 Do Not Duplicate BrowserRouter

The application should have one main routing configuration.

Do not place multiple `BrowserRouter` components unnecessarily.

## 18.2 Reusable Components

Common UI elements should be reusable.

Examples:

```text
Button
Modal
Input
Table
Pagination
LoadingSpinner
ErrorMessage
```

Do not duplicate the same component implementation across multiple pages.

---

# 19. API Calls

API communication should use the configured API client.

Example:

```javascript
api.get("/patients");
api.post("/patients", patientData);
```

Do not hard-code API URLs throughout components.

Avoid:

```javascript
axios.get("http://localhost:8080/api/patients");
```

in multiple files.

Centralize API configuration.

---

# 20. Authentication on Frontend

Authentication state should be managed centrally.

The frontend should:

- Store authentication state using the approved approach.
- Attach access tokens through the configured interceptor.
- Handle token expiration consistently.
- Redirect unauthorized users appropriately.

Do not duplicate authentication logic in every page.

---

# 21. Role-Based UI

Frontend role restrictions are for user experience.

Example:

```text
ADMIN
DOCTOR
PHARMACIST
SUPER_ADMIN
```

However:

> Frontend authorization must never replace backend authorization.

The backend must always verify permissions.

---

# 22. JavaScript Standards

Use:

```javascript
const
let
```

Avoid `var`.

Prefer meaningful names.

Good:

```javascript
const patientId = patient.id;
```

Bad:

```javascript
const x = patient.id;
```

Use strict equality:

```javascript
if (role === "ADMIN") {
}
```

Avoid:

```javascript
if (role == "ADMIN") {
}
```

---

# 23. CSS / Tailwind Standards

Prefer reusable utility classes and project-defined design patterns.

Avoid creating inconsistent styles for the same UI element.

For example, buttons should follow a consistent design system rather than every developer creating a different button style.

---

# 24. Testing Standards

Code that contains business logic should have appropriate tests.

Testing should cover:

- Services
- Controllers
- Security
- Important repositories
- Critical frontend components
- Important user workflows

Prioritize tests for critical functionality such as:

```text
Authentication
Authorization
Patient registration
Appointments
Prescriptions
Medicine stock
Dispensing
Billing
```

---

# 25. Test Naming

Test names should clearly describe the expected behavior.

Good:

```java
shouldCreatePatientWhenValidRequestIsProvided()
```

```java
shouldRejectPatientCreationWhenNicAlreadyExists()
```

Avoid:

```java
testPatient()
test1()
works()
```

---

# 26. Git Standards

## 26.1 Branch Naming

Use the project's branch naming convention.

Feature:

```text
feature/25-patient-api
```

Bug fix:

```text
fix/25-patient-validation
```

Hotfix:

```text
hotfix/25-login-error
```

---

# 27. Commit Messages

Commit messages should clearly describe the change.

Preferred format:

```text
feat: add patient CRUD APIs
fix: validate duplicate patient NIC
test: add prescription service tests
refactor: simplify appointment service
docs: update API documentation
chore: update dependencies
```

Avoid:

```text
update
changes
done
final
final2
new
test
```

---

# 28. Pull Request Standards

Every Pull Request should:

- Reference the related DDP/ticket.
- Have a clear title.
- Explain what was changed.
- Explain important implementation decisions.
- Include testing information.
- Pass CI checks.
- Have no unnecessary unrelated changes.
- Be reviewed before merging.

Example title:

```text
feat: implement prescription CRUD APIs (#25)
```

---

# 29. Code Review Standards

Reviewers should check:

### Functionality

- Does the implementation solve the ticket?
- Does it handle expected edge cases?

### Code Quality

- Is the code readable?
- Is business logic in the correct layer?
- Is there unnecessary duplication?

### Security

- Are authorization checks present?
- Is sensitive information protected?
- Are inputs validated?

### Testing

- Are important scenarios tested?
- Do existing tests still pass?

### API

- Are HTTP methods and status codes correct?
- Is the response format consistent?

---

# 30. File Organization

Backend example:

```text
src/main/java/com/onecare/backend/
├── controller/
├── service/
├── repository/
├── entity/
├── dto/
├── security/
├── exception/
├── config/
└── mapper/
```

Frontend example:

```text
src/
├── components/
├── pages/
├── layouts/
├── services/
├── hooks/
├── store/
├── utils/
├── routes/
└── assets/
```

Do not create random folders without a clear purpose.

---

# 31. Configuration Standards

Do not hard-code environment-specific values.

Avoid:

```java
String databasePassword = "password123";
```

Avoid:

```javascript
const API_URL = "http://localhost:8080";
```

Use environment/configuration files.

Examples:

```text
application.properties
application.yml
.env
.env.example
```

Never commit real secrets.

---

# 32. Environment Variables

Required environment variables should be documented in `.env.example`.

Example:

```text
DB_HOST=
DB_PORT=
DB_NAME=
DB_USERNAME=
DB_PASSWORD=

JWT_SECRET=
```

`.env` files containing real secrets must not be committed.

---

# 33. Comments

Comments should explain **why**, not simply repeat **what** the code does.

Bad:

```java
// Get patient by ID
Patient patient = patientRepository.findById(id);
```

Better:

```java
// Prevent updating archived patients because they are read-only.
validatePatientIsActive(patient);
```

Do not leave unnecessary commented-out code.

Bad:

```java
// patientRepository.delete(patient);
// old implementation
```

Delete unused code instead.

---

# 34. Magic Numbers and Strings

Avoid unexplained values.

Bad:

```java
if (failedAttempts >= 5) {
}
```

Prefer:

```java
private static final int MAX_LOGIN_ATTEMPTS = 5;
```

This improves readability and maintainability.

---

# 35. Null Handling

Avoid unnecessary `null` values.

Use appropriate approaches such as:

```java
Optional<T>
```

where appropriate.

Always consider whether a value can be missing before using it.

---

# 36. Performance

Do not optimize prematurely.

However, developers should avoid obvious performance problems.

Examples:

- Unnecessary database queries.
- Loading large datasets without pagination.
- N+1 query problems.
- Repeated API calls.
- Unnecessary React re-renders.
- Loading data that is not required.

For large lists, use pagination where appropriate.

---

# 37. API Documentation

All important APIs should be documented.

Documentation should include:

- Endpoint
- HTTP method
- Authentication requirement
- Required permissions
- Request body
- Response
- Possible errors

Example:

```text
POST /api/patients

Permission:
PATIENT_CREATE

Request:
CreatePatientRequest

Response:
PatientResponse
```

Swagger/OpenAPI should be kept consistent with the implementation.

---

# 38. Naming Consistency

Use the same terminology throughout the project.

For example, if the system uses:

```text
patient
prescription
appointment
medicine
pharmacist
```

do not randomly switch to:

```text
client
medicalOrder
booking
drug
chemist
```

unless the domain specifically requires it.

Consistent naming is especially important between:

```text
Database
Backend
API
Frontend
Documentation
```

---

# 39. Dependency Rules

Before adding a new dependency:

1. Check whether the project already provides the required functionality.
2. Check whether an existing dependency can solve the problem.
3. Consider security and maintenance.
4. Discuss significant dependencies with the team.
5. Update documentation if necessary.

Do not add libraries simply because they are convenient.

---

# 40. Security Checklist

Before creating a Pull Request, check:

- [ ] No passwords committed.
- [ ] No JWT tokens committed.
- [ ] No API keys committed.
- [ ] User input is validated.
- [ ] Authentication is checked where required.
- [ ] Authorization is checked where required.
- [ ] Sensitive information is not logged.
- [ ] SQL injection risks are avoided.
- [ ] Error messages do not expose internal information.
- [ ] Frontend restrictions are not treated as backend authorization.

---

# 41. Definition of Done for Code

A coding task should not be considered complete only because the code works locally.

Before marking a ticket as complete:

- [ ] Code follows this coding standard.
- [ ] Correct branch is used.
- [ ] Code compiles/builds successfully.
- [ ] Tests are added or updated where required.
- [ ] Existing tests pass.
- [ ] API documentation is updated where necessary.
- [ ] No debug code remains.
- [ ] No secrets are committed.
- [ ] Code has been reviewed.
- [ ] CI checks pass.
- [ ] Pull Request is linked to the relevant ticket.

---

# 42. Developer Rule

The following rule applies to the entire OneCare team:

> **Write code as if another developer will maintain it tomorrow.**

Code must be:

```text
Readable
Consistent
Testable
Secure
Maintainable
```

A feature is not considered high quality simply because it works.

The implementation must also fit the architecture and standards of the OneCare project.

---

# 43. Quick Reference

## Backend

```text
Controller → Service → Repository → Database
```

## Frontend

```text
Page → Component → Service/API → Backend
```

## Naming

```text
Classes       → PascalCase
Methods       → camelCase
Variables     → camelCase
Packages      → lowercase
DB tables     → snake_case
DB columns    → snake_case
Components    → PascalCase
```

## Git

```text
feature/XX-description
fix/XX-description
hotfix/XX-description
```

## Commits

```text
feat:
fix:
test:
refactor:
docs:
chore:
```

## Golden Rule

```text
No business logic in Controllers.
No database logic in Controllers.
No authorization only in Frontend.
No secrets in Git.
No unnecessary duplication.
No "final_final_v2" code.
```

---

# 44. Document Maintenance

This document should be updated when the project introduces a major architectural or development-process change.

Any significant change to these standards should be discussed by the development team before adoption.

**Current Version:** 1.0
**Status:** Active