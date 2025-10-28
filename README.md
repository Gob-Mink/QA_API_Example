# QA_API_Example

- [Overview](#overview)
- [Endpoints Covered](#endpoints-covered)
- [Folder Structure in Collection](#folder-structure-in-collection)
- [Postman Setup](#postman-setup)
- [Running the Collection](#running-the-collection)
- [Test Coverage](#test-coverage)
- [Notes](#notes)
- [Known bugs and observations](#known-bugs-and-observations-from-testing)

## Overview
This is a backend QA testing project using a simulated Firebase API. The collection includes requests for all endpoints, along with test scripts validating both expected behavior and known bugs. The service contains intentional inconsistencies and bugs, and this collection demonstrates how they are detected via automated test scripts.

##### Base URL https://us-central1-nn-api-challenge.cloudfunctions.net/api


## Endpoints Covered
| Method | Endpoint                    | Description                                                           |
| ------ | --------------------------- | --------------------------------------------------------------------- |
| GET    | /products                   | Returns a list of all products                                        |
| GET    | /products?name=<exact_name> | Finds products by exact name (case-sensitive)                         |
| GET    | /products/:id               | Returns a product by ID; may include description if `isDetailed=true` |
| POST   | /register                   | Registers a new user (`email`, `password`)                            |
| POST   | /login                      | Logs in a registered user and returns a token                         |
| POST   | /orders                     | Creates a new order for the logged-in user                            |
| GET    | /orders                     | Returns all orders of the current user                                |
| GET    | /orders/:id                 | Returns a specific order if it belongs to the current user            |


## Folder Structure in Collection
- Products
  - [GET] /products
  - [GET] /products/:id
  - [GET] /products?name=<exact_name>

- Users
  - [POST] /register
  - [POST] /login

- Orders
  - [POST] /orders
  - [GET] /orders
  - [GET] /orders/:id

Requests are ordered to ensure dependencies run correctly. (e.g., registration/login before order creation)



## Postman Setup
This folder contains:
- `DevQa.postman_collection.json`
- `DevQa.postman_environment.json`

### How to Import
1. Open Postman.
2. Click **Import** → Select both JSON files.
3. Ensure the environment `DevQa` is selected in the top-right of Postman.
4. Run or test any request.

---
| Variable          | Purpose / Notes                                                                                                        |
| ----------------- | ---------------------------------------------------------------------------------------------------------------------- |
| `{{baseUrl}}`     | Base URL for all API requests: `https://us-central1-nn-api-challenge.cloudfunctions.net/api`                           |
| `{{productName}}` | Stores the exact product name to search in **GET /products?name={{productName}}**. Can be set manually or dynamically. |
| `{{productId}}`   | Stores the ID of a product, can be set dynamically from **GET /products** or manually. Used in **GET /products/:id**.  |
| `{{authToken}}`   | Stores the JWT token returned from **POST /login**. Used in headers for authenticat requests. Marked sensitive.                        |
| `{{lastOrderId}}` | Stores the ID of the last order created via **POST /orders**. Used in **GET /orders/:id**.                             |
| `{{login_email}}` | Stores the email of the registered user  **POST /orders**.            |
| `{{login_paswword}}` | Stores the password of the registered user created **POST /orders**. Marked sensitive.


---


## Test Coverage
The collection includes tests for:
* Status codes (e.g., 200, 201, 409)
* Required response fields (id, email, products, total/totalPrice)
* Field validation (stock values ≥ 0, case-sensitive product search)

### Notes:
- Folder ordering ensures logical test flow and avoids dependency issues.
  - POST and GET requests are in separate folders, so they may be run independently.
  - In some scenarios, GET requests may depend on data created by POST requests (e.g., orders created for a specific user), but in other cases, GETs can run without running POSTs first.
- Variables are stored in the environment, not the collection.
- Sensitive values have been marked sensitive.

### Known bugs and observations from testing:
1. Negative stock values: One product had stock = -1, which caused the test “Each item has id, name, price, stock” to fail.
2. GET /products/:id with id=999 caused a 500 Internal Server Error.
3. Missing totalPrice in orders: One order returned total instead of totalPrice, causing “Total price exists” test to fail.
4. Duplicate email during registration: One test returned 409 Conflict because the email already existed. This highlights that the acceptance criteria for POST /register are minimal, and additional validation is recommended to make the endpoint more robust and testable from a QA perspective.
- These bugs are intentionally detected by failing tests to demonstrate QA validation.

---