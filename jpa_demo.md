# **Problem Statement: Employee Information Management System (EIMS)**

*(Based on JPA Core + Entity Mapping Fundamentals)*

## **Problem Context**

A training and consulting organization needs a lightweight **Employee Information Management System (EIMS)** built using **pure JPA (no Spring, no frameworks)**.
The system must store employee details, track their departments, record personal information, and support queries for reporting.

The solution must rely on:

* `persistence.xml` configuration
* JPA entity mappings (field-level access)
* CRUD operations using `EntityManager`
* Proper lifecycle handling of the persistence context
* Hibernate as the JPA provider

---

# **Functional Requirements**

You must design and implement the following:

---

## ** Employee Registration**

An employee contains:

* **Full Name**
* **Email** *(must be unique)*
* **Address** (Embedded value type)

  * Street
  * City
  * Zipcode
* **Employment Status** (Enum: ACTIVE, INACTIVE, PROBATION)
* **Bio** (CLOB text)
* **Profile Picture** (BLOB)
* **Active Flag** (Boolean stored as `'Y'` or `'N'`)
* **Temporary Note** *(should NOT be stored in DB)*
* **Department** (A simple many-to-one association — *to be added later if needed*)

### Requirements:

* Employee data must be persisted and read using JPA/Hibernate.
* Address must be stored as an embedded component using `@Embeddable` and `@Embedded`.
* Enum must be stored using `EnumType.STRING`.
* Boolean must use a custom converter (`BooleanYNConverter` → `'Y'/'N'` mapping).
* Bio and profile picture must use `@Lob` mappings.

---

## ** Department Management**

A department contains:

* Department ID (auto-generated)
* Department Name

### Requirements:

* Departments must be stored independently.
* System must allow assigning an employee to a department (*if extended to relationships*).

---

## ** Data Integrity Requirements**

* Email field must be **unique**.
* Employee name must be **non-null**.
* Address fields must map to customized column names via `@AttributeOverrides`.

---

## ** CRUD Operations**

Using only `EntityManager` and resource-local transactions, implement:
### Create Employee
### Read Employee by ID
### Update Employee (modify salary, status, or address)
### Delete Employee
### List all employees (optional extension)

Each operation must be executed inside proper JPA transactions.

---

# **Technical Requirements**

1. **Use pure JPA (Jakarta Persistence)**
2. **Use Hibernate as JPA provider**
3. **Use H2 or MySQL as the database**
4. **Store configuration in `META-INF/persistence.xml`**
5. **Implement a static `EntityManagerFactory` in `JPAUtil`**
6. **Demonstrate persistence context behavior**

   * First-level cache
   * Dirty checking
   * Insert, update, and delete triggers during `flush()` or `commit()`

---

# **Deliverables**

Your final solution must include:

### Entity classes with correct mappings
### Attribute converter for Boolean → Y/N
### CRUD operations using `EntityManager`
### A `Main` class demonstrating:

* Creating a Department
* Creating an Employee with embedded Address
* Reading Employee by ID
* Modifying an Employee (triggering dirty checking)
* Deleting an Employee
* Printing SQL output (Hibernate should show SQL)

---

# **Bonus Tasks **

If you want to go deeper:

* Add **OneToMany** / **ManyToOne** relationship between Department and Employees.
* Add **DTO projections** using JPQL.
* Add **pagination** using `setFirstResult` and `setMaxResults`.
* Demonstrate **N+1 problem** and solve with **FETCH JOIN**.
* Add **AttributeConverter** for enum custom mapping.
* Add **lifecycle callbacks** (`@PrePersist`, `@PostUpdate`, etc.).


