# API Test Design & Strategy for Swagger Petstore

## 1. Introduction & Objectives
This document outlines the strategic test design, methodology, and coverage framework for the automated API test suite created for the **Swagger Petstore v2** public REST API. 

The primary goals of this test architecture are:
* **Comprehensive Coverage:** Ensure every active endpoint across all functional modules (`Pet`, `Store`, `User`) is verified with non-redundant, deterministic test cases.
* **Maintainability & Portability:** Decouple environment configuration (URLs, secrets) from test definitions using centralized environment files.
* **State Management:** Demonstrate advanced API testing patterns like dynamic data extraction and variable chaining (e.g., passing a generated `petId` from a `POST` response directly into subsequent `GET` and `PUT` requests).
* **CI/CD Readiness:** Design tests within a structured Postman Collection format that can be executed headlessly via CLI runners (Newman).

### Test Data Management & Resilience
* **Dynamic Teardown Handling:** All created records are tracked in memory during runtime and cleaned up via lifecycle teardowns to prevent sandbox pollution.
* **Mock Server Compatibility:** Collection environments can be seamlessly swapped to point to local Postman Mock Servers for offline development or contract testing.

---

## 2. Test Design Principles & QA Best Practices

The test suite adheres to industry-standard Software Quality Assurance (SQA) principles:

1. **Idempotency & Isolation:** Tests are structured to minimize tight coupling where possible, using unique IDs and lifecycle teardowns (such as `DELETE` requests) to clean up test data.
2. **Deterministic Assertions:** Every test goes beyond simple status code checks (`200 OK`). They validate data integrity, payload schemas, and specific key-value responses using automated JavaScript assertions (`pm.test()`).
3. **Standardized Naming Convention:** All test cases follow a rigorous tracking format: `PET-{ID} [Descriptive Title]`, facilitating seamless traceability between bug reports, requirements, and test suites.
4. **Environment Abstraction:** No hardcoded endpoints or credentials exist in the request definitions. All environments leverage parameterized keys (`{{baseUrl}}`, `{{api_key}}`, `{{username}}`) to support multi-environment execution (e.g., QA, Staging, Production).
5. **Data-Driven Testing (DDT):** Leverages external CSV or JSON data files during collection runs to validate multiple data permutations (such as various pet statuses or invalid inventory payloads) through a single test definition.

---

## 3. Test Coverage Mapping Matrix

The test suite is partitioned into three core domain modules. Below is the functional coverage breakdown:

| Module | Test ID | Endpoint & Method | Test Objective / Strategy |
| :--- | :--- | :--- | :--- |
| **Pet** | `PET-001` | `POST /pet` | Creates a new pet; captures and saves `petId` dynamically to environment variables. |
| | `PET-002` | `GET /pet/{petId}` | Validates retrieval accuracy of the specific pet using the chained `petId`. |
| | `PET-003` | `PUT /pet` | Verifies data update operations (modifying status to `sold` and name update). |
| | `PET-004` | `GET /pet/findByStatus` | Validates query parameter filtering functionality (`status=sold`). |
| | `PET-005` | `GET /pet/findByTags` | Validates multi-tag querying mechanisms (`tags=friendly`). |
| | `PET-006` | `POST /pet/{petId}` | Tests form-urlencoded updates (`x-www-form-urlencoded`). |
| | `PET-007` | `POST /pet/{petId}/uploadImage` | Verifies multi-part form data file attachment handling. |
| | `PET-008` | `DELETE /pet/{petId}` | Tests resource deletion with mandatory `api_key` header authentication. |
| | `PET-009` | `GET /pet/999999999` | **[Negative]** Tests retrieval of a non-existent pet ID and verifies a `404` error response. |
| | `PET-010` | `POST /pet` | **[Negative]** Tests adding a pet with a malformed JSON payload. |
| | `PET-011` | `POST /pet` | **[Boundary]** Tests adding a pet with an out-of-bounds negative ID (`-999999`). |
| | `PET-012` | `POST /pet` | **[Security/Negative]** Tests adding a pet with an unsupported `text/plain` content type. |
| **Store** | `STORE-001` | `GET /store/inventory` | Validates inventory mapping structures across status keys with API key auth. |
| | `STORE-002` | `POST /store/order` | Places a purchase order; captures and saves `orderId` dynamically. |
| | `STORE-003` | `GET /store/order/{orderId}` | Confirms order details are correctly mapped by ID using environment variable. |
| | `STORE-004` | `DELETE /store/order/{orderId}` | Tests purchase order cancellation and cleanup by ID. |
| | `STORE-005` | `GET /store/order/999999` | **[Negative]** Validates handling of non-existent purchase orders (expects `404`). |
| | `STORE-006` | `POST /store/order` | **[Boundary]** Tests placing an order with zero quantity. |
| **User** | `USER-001` | `POST /user` | Registers a single core test user profile. |
| | `USER-002` | `POST /user/createWithArray` | Tests bulk user creation payload ingestion via array structure. |
| | `USER-003` | `POST /user/createWithList` | Tests alternative bulk list ingestion mechanisms. |
| | `USER-004` | `GET /user/login` | Validates credential authentication and session establishment. |
| | `USER-005` | `GET /user/{username}` | Retrieves individual user profiles by unique handle matching environment variable. |
| | `USER-006` | `PUT /user/{username}` | Tests user account modification/update workflows. |
| | `USER-007` | `GET /user/logout` | Verifies active session termination protocol. |
| | `USER-008` | `DELETE /user/{username}` | Cleans up user record via account deletion. |
| | `USER-009` | `GET /user/login` | **[Negative]** Tests login authentication failure with an incorrect password. |
| | `USER-010` | `PUT /user/{username}` | **[Security]** Tests updating a user without the required Content-Type header. |

---

## 4. Execution & Automation Workflow

To validate this suite locally or integrate it into a CI/CD pipeline (like GitHub Actions), follow these strategic guidelines:

* **Manual Exploratory / Regression:** Import `petstore.postman_collection.json` and `petstore.env.json` into Postman, select the environment, and run via the Collection Runner.
* **Headless / Pipeline Execution:** Use **Newman** to run the suite programmatically in your terminal or deployment pipeline:
  ```bash
  npx newman run ./collections/petstore.postman_collection.json -e ./environments/petstore.env.json --reporters cli,json
  ```
* **Headless / Pipeline Execution & HTML Reporting:** Use **Newman** combined with `newman-reporter-htmlextra` to run the suite programmatically and generate visual execution reports for CI/CD pipeline artifacts:
  ```bash
  npx newman run ./collections/petstore.postman_collection.json -e ./environments/petstore.env.json -r cli,htmlextra --reporter-htmlextra-export ./reports/petstore-report.html
  ```