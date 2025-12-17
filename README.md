# Autodesk Construction Cloud (ACC) API Console

This repository contains an OpenAPI 3.1.0 specification for interacting with the Autodesk Construction Cloud API. The schema enables structured access to ACC project data and cost management features.

## Base URL
```
https://developer.api.autodesk.com/
```

## Supported APIs

### Admin APIs

#### Projects
- **GET** `/construction/admin/v1/accounts/{accountId}/projects`
  - Retrieves all projects for a specified account
  - Requires: Account ID (UUID format)
  - Authentication: OAuth2

#### Project Users
- **GET** `/construction/admin/v1/projects/{projectId}/users`
  - Retrieves all users for a specified project
  - Requires: Project ID (UUID format)

---

### Cost Management APIs

#### Budget Code Templates
- **GET** `/cost/v1/containers/{containerId}/templates`
  - Retrieves a list of budget code templates for the specified container
  - Templates include IDs and names used to structure cost data

#### Budget Code Segments
- **GET** `/cost/v1/containers/{containerId}/templates/{templateId}/segments`
  - Retrieves a list of budget code segments for a specific template
  - Segments define the structure of budget codes
  
- **POST** `/cost/v1/containers/{containerId}/templates/{templateId}/segments`
  - Creates a new budget code segment for a template
  - Configurable properties: name, type (code/column/info), delimiter, length, position, sample code

#### Budget Code Segment Values
- **GET** `/cost/v1/containers/{containerId}/segment-values`
  - Retrieves all budget code segment values for a container
  
- **POST** `/cost/v1/containers/{containerId}/segment-values`
  - Creates a new budget code segment value
  - Requires: segment ID (query parameter), code, description
  - Supports hierarchical structure with parent IDs

#### Budgets
- **GET** `/cost/v1/containers/{containerId}/budgets`
  - Retrieves budgets with extensive filtering and pagination
  - Filters: rootId, id, lastModifiedSince, externalSystem, externalId, code
  - Include options: subitems, attributes, contract, mainContract, segments, compounded
  
- **POST** `/cost/v1/containers/{containerId}/budgets`
  - Creates a new budget line item
  - Required: code
  - Optional: name, description, quantity, unitPrice, unit, actualQuantity, actualUnitPrice, actualCost
  
- **PATCH** `/cost/v1/containers/{containerId}/budgets/{budgetId}`
  - Updates an existing budget line item
  - Supports scope definition (budgetOnly, budgetAndCost)
  - Can update parent relationships, codes, quantities, prices
  
- **DELETE** `/cost/v1/containers/{containerId}/budgets/{budgetId}`
  - Deletes a budget line item

#### Contracts
- **GET** `/cost/v1/containers/{containerId}/contracts`
  - Retrieves contracts with comprehensive filtering
  - Filters: id, source, code, status, type, externalSystem, externalId, lastModifiedSince
  - Include options: budgets, attributes, scheduleOfValues, compounded
  - Status options: draft, pending, submitted, revise, sent, signed, executed, closed, inReview
  
- **POST** `/cost/v1/containers/{containerId}/contracts`
  - Creates a new contract
  - Required: name
  - Extensive properties: code, description, company info, type, contacts, retention percentages, status, currency, exchange rates, ERP integration fields
  
- **PATCH** `/cost/v1/containers/{containerId}/contracts/{contractId}`
  - Updates an existing contract
  - All contract properties are updatable
  
- **DELETE** `/cost/v1/containers/{containerId}/contracts/{contractId}`
  - Deletes a contract

#### Budget-Contract Links
- **POST** `/cost/v1/containers/{containerId}/budgets-contracts:link`
  - Links budgets to contracts or removes existing links
  - Supports batch operations with `create` and `remove` arrays

#### Schedule of Values (SOV)
- **GET** `/cost/v1/containers/{containerId}/schedule-of-values/{id}`
  - Retrieves a specific schedule of values entry
  
- **POST** `/cost/v1/containers/{containerId}/schedule-of-values`
  - Creates a new SOV item
  - Required: code, name
  - Supports hierarchical structure, quantities, pricing, bulk conversions, exchange rates

#### Change Orders
- **POST** `/cost/v1/containers/{containerId}/change-orders/{changeOrder}`
  - Creates a new change order
  - Change order types: pco, rfq, rco, oco, sco
  - Properties: name, description, scope (out/in/tbd/budgetOnly/contingency), scopeOfWork, notes
  - Supports Tiptap formatted rich text for scope of work and notes

---

### Issues APIs

#### Issue Types
- **GET** `/construction/issues/v1/projects/{projectId}/issue-types`
  - Retrieves issue types for a project
  - Optional: include=subtypes to get nested type hierarchies

---

## Container ID

In ACC projects, the **container ID** is the same as the **project ID**. Use the Projects endpoint to obtain the project/container ID needed for Cost Management API calls.

## Authentication

Most endpoints require OAuth2 authentication. Ensure you have valid access tokens before making API calls.

## Response Format

All successful responses return a `200 OK` status code. Refer to the OpenAPI schema for detailed request/response structures.

## Version

Current API specification version: **1.0.0**

---

## Notes

- This schema is continuously evolving as new endpoints are added
- All IDs are in UUID format unless otherwise specified
- Pagination is supported on list endpoints with `offset` and `limit` parameters
- External ERP system integration is supported for budgets and contracts
