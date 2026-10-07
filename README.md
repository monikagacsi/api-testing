# API Testing Project 

## Overview
This repository contains two API testing portfolio projects built to demonstrate automated API testing skills using Postman, Newman, and structured quality assurance methodologies.

Each project includes a complete collection, environment configuration, test design strategy, and execution report.

---

## Project Structure

```api-testing/
├── README.md
├── test-strategy.md
├── petstore/
│   ├── petstore-collection.postman.json
│   ├── petstore-environment.postman.json
│   ├── petstore-test-design.md
│   └── petstore-test-report.md
└── restful-broker/
    ├── restful-collection.postman.json
    ├── restful-environment.postman.json
    ├── restful-test-design.md
    └── restful-test-report.md
```
---

## Projects Included

### 1. Petstore API (/petstore)
* Target API: Swagger Petstore v2
* Description: Comprehensive API test suite covering Pet, Store, and User endpoints, including positive, negative, boundary, and security test cases.
* Key Files:
  * petstore-collection.postman.json
  * petstore-environment.postman.json
  * petstore-test-design.md
  * petstore-test-report.md

### 2. Restful-Booker API (/restful-broker)
* Target API: Restful-Booker
* Description: Automated API test suite covering authentication token generation, booking CRUD operations, advanced query filtering, boundaries, and security scenarios.
* Key Files:
  * restful-collection.postman.json
  * restful-environment.postman.json
  * restful-test-design.md
  * restful-test-report.md

---

## Tech Stack
* Testing Tool: Postman (Collection v2.1.0)
* Assertions: Chai assertion library (`pm.test()`, `pm.expect()`) via Postman test scripts
* CLI / Runner: Newman (for command-line and CI/CD execution)

---

## How to Run the Tests

You can run these tests either manually via Postman or headlessly using the command line with Newman.

### Option A: Run via Terminal (Newman)

1. Make sure Node.js is installed.
2. Run Newman via npx for either project from the repository root:

* Petstore Tests:
  ```bash
  npx newman run ./petstore/petstore-collection.postman.json -e ./petstore/petstore-environment.postman.json --reporters cli,json
  ```

* Restful-Booker Tests:
  ```bash
  npx newman run ./restful-broker/restful-collection.postman.json -e ./restful-broker/restful-environment.postman.json --reporters cli,json
  ```

---

### Option B: Run Manually in Postman

1. Open Postman.
2. Click Import and drag/drop the collection and environment JSON files for the project you want to test (`petstore/` or `restful-broker/`).
3. Select the corresponding environment from the top-right environment dropdown.
4. Open the Collection Runner, select the collection, and click Run.
