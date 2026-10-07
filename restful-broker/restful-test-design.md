# API Test Design & Strategy for Restful-Booker

## 1. Introduction & Objectives
This document outlines the strategic test design, methodology, and coverage framework for the automated API test suite created for the **Restful-Booker** public REST API. 

The primary goals of this test architecture are:
* **Comprehensive Coverage:** Ensure every active endpoint across core functional modules (`Auth`, `Booking`, `Ping`) is verified through positive, negative, boundary, and security test cases.
* **Maintainability & Portability:** Decouple environment configuration (`baseUrl`, credentials, tokens, dynamic IDs) from test definitions using centralized environment files.
* **State Management:** Demonstrate advanced API testing patterns like dynamic data extraction and variable chaining (e.g., capturing a generated `bookingId` from a `POST` response and passing it directly into subsequent `GET`, `PUT`, `PATCH`, and `DELETE` requests).
* **CI/CD Readiness:** Design tests within a structured Postman Collection format that can be executed headlessly via CLI runners (Newman).

---

## 2. Test Design Principles & QA Best Practices

The test suite adheres to industry-standard Software Quality Assurance (SQA) principles:

1. **Idempotency & Isolation:** Tests are structured with unique runtime data generation and concluding lifecycle teardowns (`DELETE` requests) to maintain sandbox hygiene.
2. **Deterministic Assertions:** Every test goes beyond simple status code checks. They validate data integrity, payload schemas, and specific key-value responses using automated JavaScript assertions (`pm.test()`).
3. **Standardized Naming Convention:** All test cases follow a rigorous tracking format: `MODULE-SEQ: Description [Type]`, facilitating seamless traceability between execution logs and quality reports.
4. **Environment Abstraction:** No hardcoded endpoints or secrets exist in the request definitions. All environments leverage parameterized keys (`{{baseUrl}}`, `{{username}}`, `{{password}}`, `{{token}}`, `{{bookingId}}`) to support multi-environment execution.
5. **Data-Driven Testing (DDT):** Leverages external CSV or JSON data files during collection runs to validate multiple data permutations (such as various pricing boundaries or negative date formats) through a single test definition.

### Test Data Management & Resilience
* **Dynamic Teardown Handling:** All created records are tracked in memory during runtime and cleaned up via lifecycle teardowns to prevent sandbox pollution.
* **Mock Server Compatibility:** Collection environments can be seamlessly swapped to point to local Postman Mock Servers for offline development or contract testing.

---

## 3. Test Coverage Mapping Matrix

The test suite is partitioned into three core domain modules, encompassing 14 distinct automated test scenarios. Below is the functional coverage breakdown:

| Module | Test ID | Endpoint & Method | Test Objective / Strategy |
| :--- | :--- | :--- | :--- |
| **Auth** | `AUTH-001` | `POST /auth` | Creates a valid authentication token for subsequent protected CRUD operations. |
| | `AUTH-002` | `POST /auth` | Negative test: verifies error handling and rejection when invalid credentials are supplied. |
| **Booking** | `BOOKING-001` | `GET /booking` | Retrieves all booking IDs; validates array response structure. |
| | `BOOKING-002` | `GET /booking` | Validates multi-parameter query filtering (`firstname` and `lastname`). |
| | `BOOKING-003` | `POST /booking` | Creates a new booking; captures and saves `bookingId` dynamically to environment variables. |
| | `BOOKING-004` | `GET /booking/{id}` | Validates retrieval accuracy of the specific booking using the chained `bookingId`. |
| | `BOOKING-005` | `PUT /booking/{id}` | Verifies full data update operations on an existing booking using token authorization. |
| | `BOOKING-006` | `PATCH /booking/{id}` | Verifies partial data update operations (modifying specific fields like total price or firstname). |
| | `BOOKING-007` | `DELETE /booking/{id}` | Tests resource deletion with mandatory cookie/token authentication and subsequent cleanup. |
| | `BOOKING-008` | `GET /booking/{id}` | Negative test: validates handling of non-existent booking IDs (expects `404 Not Found`). |
| | `BOOKING-009` | `PUT /booking/{id}` | Security test: attempts to update a booking without providing a valid auth token (expects authorization failure). |
| | `BOOKING-010` | `POST /booking` | Boundary test: evaluates system behavior with extreme or massive numerical pricing values. |
| | `BOOKING-011` | `POST /booking` | Negative test: verifies rejection of inverted or invalid check-in/check-out dates. |
| **Ping** | `PING-001` | `GET /ping` | Health check endpoint verifying service availability and operational status. |

---

## 4. Execution & Automation Workflow

To validate this suite locally or integrate it into a CI/CD pipeline (like GitHub Actions), follow these strategic guidelines:

* **Manual Exploratory / Regression:** Import `restful-booker.postman_collection.json` and `restful-booker.env.json` into Postman, select the environment, and run via the Collection Runner.
* **Headless / Pipeline Execution:** Use **Newman** to run the suite programmatically in your terminal or deployment pipeline:
  ```bash
  npx newman run ./collections/restful-booker.postman_collection.json -e ./environments/restful-booker.env.json --reporters cli,json
  ```
* **Headless / Pipeline Execution & HTML Reporting:** Use **Newman** combined with `newman-reporter-htmlextra` to run the suite programmatically and generate visual execution reports for CI/CD pipeline artifacts:
  ```bash
  npx newman run ./collections/restful-booker.postman_collection.json -e ./environments/restful-booker.env.json -r cli,htmlextra --reporter-htmlextra-export ./reports/booker-report.html
  ```