# ACC Cost Management Wizard GPT
This repo tracks the "ACC Cost Management Wizard" GPT schema. Pull requests are welcome. Please Slack me @Keenan Bruni with any feedback, requests, bug reports, etc.

Note that custom GPTs are limited to 30 API operations - so not all APIs will be included. GET, POST, and PATCH operations are prioritized over DELETE as this tool is meant for setting up custom demo environments on the fly.

## Supported API Endpoints

### Admin APIs

#### Projects
- **GET** `/construction/admin/v1/accounts/{accountId}/projects`
  - Retrieves all projects for a specified account
  - Requires: Account ID (UUID format)
  - Authentication: OAuth2

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

#### Budget Code Segment Values
- **GET** `/cost/v1/containers/{containerId}/segment-values`
  - Retrieves all budget code segment values for a container

#### Budgets
- **GET** `/cost/v1/containers/{containerId}/budgets`
  - Retrieves budgets with extensive filtering and pagination
  - Filters: rootId, id, lastModifiedSince, externalSystem, externalId, code
  - Include options: subitems, attributes, contract, mainContract, mainContractItem, segments, idOnly, compounded
  
- **POST** `/cost/v1/containers/{containerId}/budgets`
  - Creates a new budget line item
  - Required: code
  - Optional: name, description, quantity, unitPrice, unit, actualQuantity, actualUnitPrice, actualCost, segmentCodeMap, inputQuantity
  
- **PATCH** `/cost/v1/containers/{containerId}/budgets/{budgetId}`
  - Updates an existing budget line item
  - Supports scope definition (budgetOnly, budgetAndCost)
  - Can update parent relationships, codes, quantities, prices, segmentCodeMap

#### Contracts
- **GET** `/cost/v1/containers/{containerId}/contracts`
  - Retrieves contracts with comprehensive filtering
  - Filters: id, source, code, status, type, externalSystem, externalId, lastModifiedSince
  - Include options: budgets, attributes, idOnly, scheduleOfValues, compounded
  - Status options: draft, pending, submitted, revise, sent, signed, executed, closed, inReview
  
- **POST** `/cost/v1/containers/{containerId}/contracts`
  - Creates a new contract
  - Required: name
  - Extensive properties: code, description, company info, type, contacts, retention percentages, status, currency, exchange rates, ERP integration fields
  
- **PATCH** `/cost/v1/containers/{containerId}/contracts/{contractId}`
  - Updates an existing contract
  - All contract properties are updatable

#### Budget-Contract Links
- **POST** `/cost/v1/containers/{containerId}/budgets-contracts:link`
  - Links budgets to contracts or removes existing links
  - Supports batch operations with `create` and `remove` arrays

#### Schedule of Values (SOV)
- **POST** `/cost/v1/containers/{containerId}/schedule-of-values`
  - Creates a new SOV item
  - Required: code, name
  - Supports hierarchical structure, quantities, pricing, bulk conversions, exchange rates

#### Change Orders
- **GET** `/cost/v1/containers/{containerId}/change-orders/{changeOrder}`
  - Retrieves a list of change orders of a specified type
  - Change order types: pco, rfq, rco, oco, sco
  - Filters: id, number, sourceId, contractId, mainContractId, budgetStatus, costStatus, lastModifiedSince, externalSystem, externalId
  - Include options: costItems, costItems[changeOrders], attributes, comments

- **POST** `/cost/v1/containers/{containerId}/change-orders/{changeOrder}`
  - Creates a new change order
  - Change order types: pco, rfq, rco, oco, sco
  - Properties: name, description, scope (out/in/tbd/budgetOnly/contingency), scopeOfWork, note, sourceType
  - Supports Tiptap formatted rich text for scope of work and notes

- **PATCH** `/cost/v1/containers/{containerId}/change-orders/{changeOrder}/{id}`
  - Updates an existing change order
  - Updatable: name, description, type, scope, scheduleChange, proposedRevisedCompletionDate, ownerId, scopeOfWork, note, exchangeRate, company info, architect info, additionalCollaborators, sourceType, ERP integration fields

#### Cost Items
- **GET** `/cost/v1/containers/{containerId}/cost-items`
  - Retrieves all cost items in a container
  - Filters: id, number, changeOrderId, budgetId, contractId, budgetStatus, costStatus, externalSystem, externalId, lastModifiedSince
  - Include options: budget, changeOrders, subCostItems, attributes

- **POST** `/cost/v1/containers/{containerId}/cost-items`
  - Creates a new cost item
  - Required: name
  - Optional: changeOrderId, budgetId, contractId, description, estimated, proposed, submitted, approved, committed, inputQuantity, quantity, unit, exchange rates, locations, ERP integration fields

#### Expenses
- **GET** `/cost/v1/containers/{containerId}/expenses`
  - Retrieves the requested set of expenses in the specified project
  - Filters: id, number, status, mainContractId, budgetPaymentId, createdAt, lastModifiedSince, externalSystem, externalId
  - Include options: expenseItems, mainContract, attributes, externalRelationship, paymentReferences
  - Status options: draft, pending, submitted, revise, sent, signed, executed, closed, inReview

- **POST** `/cost/v1/containers/{containerId}/expenses`
  - Creates an expense in the given project
  - Required: name
  - Optional: supplierId, supplierCompanyUid, supplierName, contractId, number, description, note, term, referenceNumber, type, scope, purchasedBy, status, paymentDue, issuedAt, receivedAt, ERP integration fields

#### Expense Items
- **GET** `/cost/v1/containers/{containerId}/expenses/{expenseId}/items`
  - Retrieves the expense items and subitems of the specified expenses
  - Filters: id, lastModifiedSince, externalSystem, externalId
  - Include options: budget, contract, attributes, externalRelationship

- **POST** `/cost/v1/containers/{containerId}/expenses/{expenseId}/items`
  - Creates an expense item in the specified expense
  - Required: name
  - Optional: budgetId, budgetCode, contractId, number, description, note, scope (full/partial), quantity, unitPrice, unit, amount, exchangeRate, ERP integration fields

---

## Container ID

In ACC projects, the **container ID** is the same as the **project ID**. Use the Projects endpoint to obtain the project/container ID needed for Cost Management API calls.

## Notes

- This schema is continuously evolving as new endpoints are added
- All IDs are in UUID format unless otherwise specified
- Pagination is supported on list endpoints with `offset` and `limit` parameters
