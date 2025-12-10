
# **Problem Statement: Banking Transaction Processing System (Service + DTO + Controller Layers)**

Your task is to design and implement the **Service layer**, **DTO layer**, and **REST Controller layer** for a simplified **Banking Transaction Processing System**.
This system supports customers, bank accounts, and financial transactions (debits/credits).

Entity definitions are already provided to you:

* `Customer`
* `BankAccount`
* `Transaction`
* `AuditLog`
* `Branch`
* `BaseEntity` (with auditing & optimistic locking)

Your responsibility is to **build all application logic above the entity and repository layers**, ensuring correctness, transactional integrity, and clean API design.

---

# **Key Requirements**

## **1. Service Layer (Business Logic)**

You must implement the following services:

### **A) AccountService**

Responsible for account-level operations.
Must include:

1. `deposit(BankAccount account, BigDecimal amount, String description)`
2. `withdraw(BankAccount account, BigDecimal amount, String description)`

   * Must validate available balance
   * Must raise a domain exception if insufficient
3. Automatic creation of `Transaction` records
4. Must be transactional
5. Must use domain methods like `account.deposit()` and `account.withdraw()`

---

### **B) BankingService (Fund Transfer Workflow)**

Implements an **atomic fund transfer** between accounts:

1. Load source and destination accounts
2. Validate business rules
3. Withdraw from source
4. Deposit into destination
5. Record audit logs (via AuditService)
6. Must be fully transactional (all or nothing)
7. Must be resilient to concurrent updates using optimistic locking

This method must accept:

* `fromAccountId`
* `toAccountId`
* `amount`
* `performedBy`

Audit logs must record:

* Type of action
* Who performed it
* Details of transfer
* Timestamp

---

### **C) AuditService**

A simple service responsible for:

* Writing `AuditLog` records
* Capturing the performer (e.g., "SYSTEM" or username)
* Running independently from business logic

---

# **2. DTO Layer**

You must design request and response DTOs to ensure **clean separation** between:

* External API shape
* Internal entity structure

Minimum required DTOs:

### **TransferRequest**

```
fromAccountId  
toAccountId  
amount  
performedBy
```

### **TransferResponse**

```
message  
fromAccountId  
toAccountId  
amount  
timestamp
```

### **AccountResponse**

```
id  
accountNumber  
balance  
customerName  
status
```

### **TransactionResponse**

```
id  
type  
amount  
description  
timestamp
```

### **CustomerResponse**

```
id  
fullName  
email  
phone  
branchName
```

### **ApiResponse<T>**

A standardized response wrapper.

---

# **3. Controller Layer**

You must expose REST APIs for:

## **A) Fund Transfer API**

`POST /api/banking/transfer`

* Accepts `TransferRequest`
* Calls `BankingService.transfer()`
* Returns `TransferResponse` wrapped in `ApiResponse`

---

## **B) Account APIs**

### `GET /api/accounts/{id}`

Returns account details.

### `GET /api/accounts/customer/{customerId}`

Returns all accounts belonging to a customer.

Both must return `AccountResponse`.

---

## **C) Transaction APIs**

### `GET /api/transactions/account/{accountId}`

Returns recent transactions (top N based on requirement)
Returns `TransactionResponse` list.

---

## **D) Customer APIs**

### `GET /api/customers/{id}`

Returns details of a customer using `CustomerResponse`.

---

# **Constraints & Expectations**

### Use Spring Boot, Spring Data JPA, and standard patterns
### No business logic inside controllers or repositories
### Handle errors using custom exceptions
### Build reusable, well-structured services
### Apply @Transactional at correct service-level boundaries
### Include domain exceptions such as:

* `InsufficientBalanceException`
* `AccountNotFoundException`

### Use clean DTOs — never expose JPA entities directly
### Use JPA auditing fields already defined in BaseEntity
### Code must be production-quality
### Controller responses should return proper HTTP status codes

---

# Additional Challenge

1. Add pagination & sorting to transaction retrieval.
2. Add `/api/transactions/top` to fetch highest-value transactions.

