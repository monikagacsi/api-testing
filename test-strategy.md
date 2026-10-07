# API Testing Strategy

## 1. Executive Summary & Testing Philosophy
This portfolio features an automated API testing framework built to go far beyond basic HTTP status code checks. It focuses on shift-left testing, contract validation, and state resilience—implementing deep programmatic checks for data schemas, business logic rules, security permissions, and error-handling edge cases.

---

## 2. Goals & Objectives
* **Comprehensive Functional Coverage:** Validate 100% of core resource lifecycles (CRUD operations), parameterized queries, and state transitions across backend endpoints.
* **Robust Negative & Boundary Testing:** Ensure target APIs degrade gracefully, rejecting malformed payloads, out-of-bounds parameters, and invalid authentication states with appropriate error codes.
* **Security & Access Control Enforcement:** Verify that role-based permissions, token authentication, and header constraints are strictly enforced across protected routes.
* **Portability & CI/CD Readiness:** Decouple environment configurations from test logic to enable seamless execution across local development, sandbox, and headless continuous integration pipelines.

---

## 3. Scope of Testing

### In-Scope
* **REST API Endpoints:** Resource creation, retrieval, updates (full PUT and partial PATCH), and deletion.
* **Authentication & Authorization:** Token generation, session cookies, credential validation, and unauthenticated/unauthorized access blocking.
* **Data Validation & Payload Integrity:** Content-Type headers, JSON schema contracts, array/list batch ingestions, and multi-part form data uploads.
* **Query Parameter Filtering:** Multi-criteria search filters and boundary condition handling.

### Out-of-Scope
* Front-end UI rendering and cross-browser visual validation (handled via separate UI test layers).
* Third-party downstream microservice infrastructure (mocked or handled via sandbox environments).

---

## 4. Test Design & Methodologies

The test suite incorporates QA best practices to ensure deterministic, repeatable, and maintainable automation:

### A. Test Categorization Matrix
* **Positive Tests:** Validates expected behavior under normal operating parameters (e.g., creating a valid booking or user profile).
* **Negative Tests:** Evaluates system behavior against invalid data, missing mandatory headers, or non-existent resource IDs.
* **Boundary Value Analysis (BVA):** Tests edge cases such as extreme numerical price limits, zero quantities, or inverted temporal dates.
* **Security Tests:** Probes authorization enforcement by attempting data mutations without active tokens or with improper content types.

### B. Dynamic State Management & Chaining
To mirror true user journeys, tests avoid static hardcoding of dependency IDs. Upstream identifiers (such as generated petId, orderId, bookingId, or token strings) are dynamically extracted at runtime using test scripts and automatically injected into downstream requests.

---

## 5. Entry and Exit Criteria

### Entry Criteria
* API endpoints are deployed, reachable, and stable within the target environment (e.g., Swagger Petstore or Restful-Booker sandbox).
* API documentation (Swagger/OpenAPI specifications or functional requirements) is available and reviewed.
* Postman collections, environment configuration templates, and test scripts are fully written and peer-reviewed.

### Exit Criteria
* 100% execution of the designed test suite with zero unexpected blocking failures.
* All critical functional, negative, and security test assertions pass consistently.
* Execution reports generated and reviewed, confirming contract compliance across all modules.

---

## 6. Test Environment & Configuration Management
* **Environment Abstraction:** No sensitive credentials or hardcoded base URLs reside inside collection definitions. All configurations are externalized into environment JSON files.
* **Data Hygiene:** Tests are structured with cleanup teardown steps to maintain sandbox hygiene, though shared public environments account for ephemeral data volatility.

---

## 7. Execution Workflow & Automation Tools
* **Testing Engine:** Postman (Collection v2.1.0 format) utilizing the Chai assertion library for programmatic assertion blocks.
* **CLI / Pipeline Runner:** Newman is utilized for headless command-line execution, enabling integration into CI/CD workflows:
  ```
  npx newman run ./collection.postman.json -e ./environment.postman.json --reporters cli,json
  ```

---

## 8. Traceability
* **Standardized Naming Convention:** All test cases follow a rigorous tracking structure: MODULE-NUM: [Type] Description (e.g., `PET-001: Add a New Pet` or `AUTH-002: [Negative] Generate Token`).
* **Requirements Linkage:** Test case identifiers ensure full traceability between source test files, execution reports, and portfolio deliverables.

---

## 9. Reporting Standards
* **Quality Reporting:** Each execution cycle concludes with a formal test execution report documenting pass rates, module coverage breakdowns, and environmental caveats.
