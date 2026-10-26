# 🏦 Easy Bank

> A Core Java 21 banking application built from scratch using JDBC and PostgreSQL, designed to demonstrate modern Java features from Java 8 through Java 21 and all five GoF Creational Design Patterns.

---

## 📌 Project Overview

**EasyBank** is a console-based banking application developed using **pure Core Java 21**.

The project intentionally avoids application frameworks such as:

* Spring Boot
* Spring Data JPA
* Hibernate
* Lombok
* Maven
* Gradle

The application uses:

```text
Java 21
   ↓
Core Java APIs
   ↓
JDBC
   ↓
PostgreSQL
```

The main objective is to understand how a real Java application can be designed using:

1. Object-Oriented Programming
2. Collections
3. Exception Handling
4. Functional Programming
5. JDBC
6. PostgreSQL
7. Creational Design Patterns
8. Modern Java 8–21 language/API features

---

# 🎯 Project Goals

This project has four major goals.

### 1. Core Java

Build a complete application without relying on frameworks.

### 2. Database Programming

Use JDBC directly to communicate with PostgreSQL.

### 3. Design Patterns

Implement all five GoF Creational Design Patterns:

```text
Singleton
Factory Method
Abstract Factory
Builder
Prototype
```

### 4. Modern Java

Use meaningful Java features introduced from Java 8 through Java 21.

The goal is **not** to artificially use every feature. Features are included only where they make sense in the application.

---

# 🛠️ Technology Stack

| Technology             | Usage                   |
| ---------------------- | ----------------------- |
| Java 21                | Application development |
| Core Java              | Business logic          |
| JDBC                   | Database connectivity   |
| PostgreSQL             | Persistent storage      |
| PostgreSQL JDBC Driver | PostgreSQL connectivity |
| JUnit 5                | Testing                 |
| Git                    | Version control         |
| GitHub                 | Source code hosting     |

> Maven and Gradle are intentionally not used.

---

# 🧱 Architecture

```text
                         EasyBank
                            │
                            ▼
                     Console Application
                            │
                            ▼
                       Service Layer
                            │
             ┌──────────────┼──────────────┐
             │              │              │
             ▼              ▼              ▼
        Creational       Validation      Business
        Patterns                         Logic
             │
             ▼
                      Repository Layer
                            │
                            ▼
                           JDBC
                            │
                            ▼
                       PostgreSQL
```

---

# 📂 Project Structure

```text
EasyBank/
│
├── lib/
│   └── postgresql-jdbc-driver.jar
│
├── sql/
│   └── schema.sql
│
├── src/
│   └── com/
│       └── easybank/
│           │
│           ├── app/
│           │   └── EasyBank.java
│           │
│           ├── config/
│           │   └── DatabaseConfig.java
│           │
│           ├── domain/
│           │   ├── Account.java
│           │   ├── Customer.java
│           │   ├── Transaction.java
│           │   └── Type.java
│           │
│           ├── repository/
│           │   ├── AccountRepository.java
│           │   ├── CustomerRepository.java
│           │   └── TransactionRepository.java
│           │
│           ├── service/
│           │   ├── BankService.java
│           │   └── impl/
│           │       └── BankServiceImpl.java
│           │
│           ├── patterns/
│           │   │
│           │   ├── singleton/
│           │   │   └── DatabaseConnectionManager.java
│           │   │
│           │   ├── factory/
│           │   │   ├── TransactionProcessor.java
│           │   │   ├── DepositProcessor.java
│           │   │   ├── WithdrawalProcessor.java
│           │   │   ├── TransferProcessor.java
│           │   │   └── TransactionProcessorFactory.java
│           │   │
│           │   ├── abstractfactory/
│           │   │   ├── NotificationFactory.java
│           │   │   ├── EmailNotification.java
│           │   │   ├── SmsNotification.java
│           │   │   ├── ConsoleNotificationFactory.java
│           │   │   └── EmailNotificationFactory.java
│           │   │
│           │   ├── builder/
│           │   │   └── TransactionBuilder.java
│           │   │
│           │   └── prototype/
│           │       ├── TransactionTemplate.java
│           │       └── TransactionTemplateRegistry.java
│           │
│           ├── util/
│           │   └── Validation.java
│           │
│           └── exceptions/
│               ├── ValidationException.java
│               ├── AccountNotFoundException.java
│               └── InsufficientFundsException.java
│
├── test/
│   └── com/
│       └── easybank/
│           └── patterns/
│
├── .gitignore
└── README.md
```

---

# 🗄️ Database

EasyBank uses PostgreSQL for persistent data storage.

## Database

```text
easybank
```

## Tables

```text
customers
accounts
transactions
```

---

# 🔗 Database Relationship

```text
                    customers
                        │
                        │ 1
                        │
                        │
                        │ *
                    accounts
                        │
                        │ 1
                        │
                        │
                        │ *
                  transactions
```

### Customer

A customer can have multiple bank accounts.

### Account

An account belongs to one customer and can contain multiple transactions.

### Transaction

A transaction belongs to one account.

---

# 🗃️ Database Schema

## Customers

```sql
CREATE TABLE customers (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(150) UNIQUE NOT NULL,
    phone VARCHAR(15),
    created_at TIMESTAMP NOT NULL
        DEFAULT CURRENT_TIMESTAMP
);
```

---

## Accounts

```sql
CREATE TABLE accounts (
    id BIGSERIAL PRIMARY KEY,
    account_number VARCHAR(20) UNIQUE NOT NULL,
    customer_id BIGINT NOT NULL,
    account_type VARCHAR(20) NOT NULL,
    balance NUMERIC(15, 2) NOT NULL DEFAULT 0.00,
    active BOOLEAN NOT NULL DEFAULT TRUE,
    created_at TIMESTAMP NOT NULL
        DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_account_customer
        FOREIGN KEY (customer_id)
        REFERENCES customers(id)
);
```

---

## Transactions

```sql
CREATE TABLE transactions (
    id BIGSERIAL PRIMARY KEY,
    account_id BIGINT NOT NULL,
    type VARCHAR(30) NOT NULL,
    amount NUMERIC(15, 2) NOT NULL,
    description VARCHAR(255),
    reference VARCHAR(100) UNIQUE,
    created_at TIMESTAMP NOT NULL
        DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_transaction_account
        FOREIGN KEY (account_id)
        REFERENCES accounts(id)
);
```

---

# 🔌 JDBC Architecture

EasyBank does not use JPA or Hibernate.

The database flow is:

```text
BankService
     │
     ▼
Repository
     │
     ▼
DatabaseConnectionManager
     │
     ▼
JDBC
     │
     ├── Connection
     ├── PreparedStatement
     └── ResultSet
     │
     ▼
PostgreSQL
```

---

# 🔐 Database Connection

The PostgreSQL JDBC driver is the only external runtime dependency required for database connectivity.

Connection is created using standard JDBC:

```java
Connection connection =
        DriverManager.getConnection(
                url,
                username,
                password
        );
```

The project deliberately uses:

```java
java.sql.Connection
java.sql.DriverManager
java.sql.PreparedStatement
java.sql.ResultSet
java.sql.SQLException
```

instead of an ORM.

---

# 🧩 Creational Design Patterns

EasyBank implements all five major GoF Creational Design Patterns.

---

# 1️⃣ Singleton Pattern

## Purpose

Ensure that the application has a single shared database connection manager.

## Implementation

```text
patterns/
└── singleton/
    └── DatabaseConnectionManager.java
```

Architecture:

```text
CustomerRepository
        │
AccountRepository
        │
TransactionRepository
        │
        ▼
DatabaseConnectionManager
        │
        ▼
      JDBC
        │
        ▼
   PostgreSQL
```

The Singleton is implemented manually using Core Java.

---

# 2️⃣ Factory Method Pattern

## Purpose

Create the appropriate transaction processor based on transaction type.

Supported transaction types:

```text
DEPOSIT
WITHDRAWAL
TRANSFER
```

Architecture:

```text
              TransactionProcessor
                       │
            ┌──────────┼──────────┐
            │          │          │
            ▼          ▼          ▼
         Deposit   Withdrawal   Transfer
        Processor   Processor    Processor
```

Factory:

```text
TransactionProcessorFactory
```

Example:

```java
TransactionProcessor processor =
        TransactionProcessorFactory.create(
                Type.DEPOSIT
        );
```

---

# 3️⃣ Abstract Factory Pattern

## Purpose

Create families of related notification objects.

Example:

```text
NotificationFactory
       │
       ├── EmailNotification
       │
       └── SmsNotification
```

Possible implementations:

```text
Console Notification
Email Notification
SMS Notification
```

The pattern allows the application to switch notification families without changing the business logic.

---

# 4️⃣ Builder Pattern

## Purpose

Create complex transaction objects in a readable and controlled way.

Instead of:

```java
new Transaction(
    account,
    amount,
    type,
    description,
    reference,
    timestamp
);
```

we use:

```java
Transaction transaction =
        TransactionBuilder.builder()
                .account(account)
                .amount(amount)
                .type(Type.TRANSFER)
                .description("Fund Transfer")
                .reference("TXN-10001")
                .build();
```

This demonstrates the Builder Pattern using pure Java.

---

# 5️⃣ Prototype Pattern

## Purpose

Clone reusable transaction templates.

Example:

```text
Monthly Rent Template
        │
        ├── August Rent
        ├── September Rent
        └── October Rent
```

Another example:

```text
Electricity Bill Template
        │
        ├── August Bill
        ├── September Bill
        └── October Bill
```

This avoids repeatedly constructing similar transaction templates.

---

# ☕ Java 8 → Java 21 Features

The project also demonstrates meaningful Java language and API evolution.

---

## Java 8

### Lambda Expressions

```java
accounts.stream()
        .filter(account ->
                account.getBalance()
                        .compareTo(BigDecimal.ZERO) > 0)
        .toList();
```

### Functional Interfaces

```java
@FunctionalInterface
public interface Validation<T> {

    boolean validate(T value);
}
```

### Stream API

Used for:

* Transaction reports
* Account filtering
* Transaction aggregation
* Sorting
* Searching

### Optional

Used for repository lookup results:

```java
Optional<Account>
```

### Method References

```java
transactions
        .stream()
        .map(Transaction::getAmount)
        .toList();
```

### `java.time`

Used for:

```text
Transaction timestamps
Account creation dates
Transaction dates
```

---

# Java 9

### Immutable Collection Factory Methods

```java
List.of(...)
Set.of(...)
Map.of(...)
```

### Private Interface Methods

Used where appropriate for shared interface-level validation logic.

---

# Java 10

### Local Variable Type Inference

```java
var transaction =
        transactionRepository
                .findById(id);
```

`var` will be used selectively where it improves readability.

---

# Java 11

### String API Improvements

Examples:

```java
String.isBlank()
String.strip()
String.lines()
```

Useful for:

* Customer input validation
* Statement processing
* File processing

### `Files.readString()`

Used where file-based configuration or report processing is appropriate.

---

# Java 12–14

### Switch Expressions

Transaction processing can use:

```java
String description = switch (type) {

    case DEPOSIT ->
            "Money deposited";

    case WITHDRAWAL ->
            "Money withdrawn";

    case TRANSFER ->
            "Money transferred";
};
```

Switch expressions became permanent in Java 14.

---

# Java 15

### Text Blocks

JDBC SQL queries will use Java text blocks:

```java
String sql = """
        SELECT id,
               account_number,
               balance
        FROM accounts
        WHERE account_number = ?
        """;
```

Text Blocks became permanent in Java 15.

---

# Java 16

### Records

Used for immutable data-transfer structures:

```java
public record AccountSummary(
        String accountNumber,
        BigDecimal balance
) {}
```

### Pattern Matching for `instanceof`

```java
if (processor instanceof DepositProcessor deposit) {
    deposit.process();
}
```

Records and pattern matching for `instanceof` became permanent in Java 16.

---

# Java 17

### Sealed Classes

The transaction processor hierarchy can use:

```java
public sealed interface TransactionProcessor
        permits DepositProcessor,
                WithdrawalProcessor,
                TransferProcessor {

    void process(Transaction transaction);
}
```

This restricts which classes can implement the hierarchy.

Sealed classes became permanent in Java 17.

---

# Java 18

The application benefits from Java 18's UTF-8-related platform behavior when handling text/files, particularly for customer and transaction data.

---

# Java 19–20

Some features that appeared as previews during Java 19 and 20 will be demonstrated using their finalized Java 21 forms rather than enabling historical preview features.

This keeps the project compileable as a normal Java 21 application.

---

# Java 21

## Record Patterns

Used for clean extraction of record components:

```java
if (summary instanceof AccountSummary(
        String accountNumber,
        BigDecimal balance
)) {

    System.out.println(accountNumber);
    System.out.println(balance);
}
```

Record Patterns became permanent in Java 21.

---

## Pattern Matching for `switch`

Example:

```java
String processorName =
        switch (processor) {

            case DepositProcessor d ->
                    "Deposit Processor";

            case WithdrawalProcessor w ->
                    "Withdrawal Processor";

            case TransferProcessor t ->
                    "Transfer Processor";
        };
```

Pattern Matching for `switch` became permanent in Java 21.

---

## Virtual Threads

Virtual Threads can be used for independent operations such as notifications and audit processing:

```java
try (var executor =
        Executors.newVirtualThreadPerTaskExecutor()) {

    executor.submit(() -> sendEmail());
    executor.submit(() -> sendSms());
    executor.submit(() -> writeAuditLog());
}
```

Virtual Threads were finalized in Java 21.

---

## Sequenced Collections

Transaction history can take advantage of ordered collection APIs:

```java
SequencedCollection<Transaction> history =
        new ArrayList<>();
```

For example:

```java
Transaction latest =
        history.getLast();

Transaction first =
        history.getFirst();
```

Sequenced Collections were introduced in Java 21.

---

# 💳 Banking Operations

EasyBank will support:

## Customer Management

```text
Create Customer
Find Customer
Update Customer
Delete Customer
List Customers
```

---

## Account Management

```text
Create Account
Find Account
Close Account
Check Balance
List Customer Accounts
```

---

## Transactions

```text
Deposit
Withdrawal
Transfer
Transaction History
Transaction Details
```

---

## Validation

Examples:

```text
Invalid customer
Invalid account
Invalid amount
Insufficient funds
Inactive account
Duplicate email
Duplicate account number
Invalid transaction type
```

---

# 🔄 Transaction Flow

Example: Deposit

```text
User
 │
 ▼
EasyBank CLI
 │
 ▼
BankService
 │
 ▼
TransactionBuilder
 │
 ▼
Transaction
 │
 ▼
TransactionProcessorFactory
 │
 ▼
DepositProcessor
 │
 ▼
AccountRepository
 │
 ▼
JDBC
 │
 ▼
PostgreSQL
 │
 ▼
NotificationFactory
 │
 ▼
Notification
```

---

# 💰 Withdrawal Flow

```text
User
 │
 ▼
BankService
 │
 ▼
Validate Account
 │
 ▼
Check Balance
 │
 ▼
TransactionBuilder
 │
 ▼
Factory Method
 │
 ▼
WithdrawalProcessor
 │
 ▼
PostgreSQL
```

If balance is insufficient:

```text
InsufficientFundsException
```

---

# 💸 Transfer Flow

```text
Source Account
       │
       ▼
Validate Source
       │
       ▼
Check Balance
       │
       ▼
Validate Destination
       │
       ▼
Create Transaction
       │
       ▼
TransferProcessor
       │
       ├──────────────► Debit Source
       │
       └──────────────► Credit Destination
```

The transfer operation will eventually be implemented using a JDBC database transaction so that the debit and credit are treated atomically.

---

# 🧪 Testing Strategy

Testing will happen at multiple levels.

## Pattern Tests

```text
Singleton
Factory Method
Abstract Factory
Builder
Prototype
```

## Repository Tests

```text
CustomerRepository
AccountRepository
TransactionRepository
```

## Service Tests

```text
Deposit
Withdrawal
Transfer
```

## Database Integration Tests

```text
INSERT
SELECT
UPDATE
DELETE
Transactions
Rollback
Commit
```

---

# 🧪 Example Singleton Test

```java
DatabaseConnectionManager first =
        DatabaseConnectionManager.getInstance();

DatabaseConnectionManager second =
        DatabaseConnectionManager.getInstance();

assert first == second;
```

Expected:

```text
true
```

---

# 🧪 Example Factory Test

```java
TransactionProcessor processor =
        TransactionProcessorFactory.create(
                Type.DEPOSIT
        );
```

Expected:

```text
DepositProcessor
```

---

# 🧪 Example Builder Test

```java
Transaction transaction =
        TransactionBuilder.builder()
                .amount(new BigDecimal("5000"))
                .type(Type.DEPOSIT)
                .description("Salary")
                .build();
```

Expected:

```text
Amount      : 5000
Type        : DEPOSIT
Description : Salary
```

---

# 🧪 Example Prototype Test

```text
Original Template
        │
        ├── Clone
        │
        ▼
New Transaction Template
```

The clone must be a different object:

```java
assert original != clone;
```

---

# 🖥️ CLI

The application will initially use a console interface.

Example:

```text
========================================
              EASYBANK
========================================

1. Create Customer
2. Create Account
3. Deposit
4. Withdraw
5. Transfer
6. Check Balance
7. Transaction History
8. Transaction Templates
9. Customer Details
0. Exit

Enter choice:
```

---

# 🔐 Security Considerations

This is an educational banking application and is **not intended for real financial use**.

The project will nevertheless follow good programming practices:

* `PreparedStatement` for SQL parameters
* Input validation
* Custom exceptions
* Database transactions
* No SQL string concatenation for user input
* No plaintext credentials committed to Git
* Proper resource management
* Clear separation of responsibilities

---

# 🧹 JDBC Resource Management

JDBC resources will use **try-with-resources**:

```java
try (
    PreparedStatement statement =
            connection.prepareStatement(sql);
    ResultSet resultSet =
            statement.executeQuery()
) {

    // Process result

}
```

This prevents resources from being left open.

---

# 🧠 Design Principles

The project will also demonstrate:

```text
OOP
Encapsulation
Abstraction
Inheritance
Polymorphism
Composition
Interfaces
Generics
Immutability
Separation of Concerns
SOLID principles
```

The design patterns will be used to solve actual design problems rather than being added only for demonstration.

---

# 📋 Implementation Roadmap

## Phase 1 — Project Setup

* [ ] Install Java 21
* [ ] Configure JDK
* [ ] Create project structure
* [ ] Download PostgreSQL JDBC driver
* [ ] Configure PostgreSQL
* [ ] Create `easybank` database
* [ ] Create database schema
* [ ] Verify JDBC connectivity

---

## Phase 2 — Domain Model

* [ ] Create `Customer`
* [ ] Create `Account`
* [ ] Create `Transaction`
* [ ] Create `Type`
* [ ] Add validation
* [ ] Add domain behavior

---

## Phase 3 — Singleton

* [ ] Create `DatabaseConnectionManager`
* [ ] Implement thread-safe Singleton
* [ ] Test Singleton
* [ ] Connect repositories to JDBC

---

## Phase 4 — Repository Layer

* [ ] Customer CRUD
* [ ] Account CRUD
* [ ] Transaction CRUD
* [ ] PreparedStatements
* [ ] ResultSet mapping
* [ ] JDBC exception handling

---

## Phase 5 — Builder

* [ ] Create `TransactionBuilder`
* [ ] Build deposit transaction
* [ ] Build withdrawal transaction
* [ ] Build transfer transaction
* [ ] Add Builder tests

---

## Phase 6 — Factory Method

* [ ] Create `TransactionProcessor`
* [ ] Create `DepositProcessor`
* [ ] Create `WithdrawalProcessor`
* [ ] Create `TransferProcessor`
* [ ] Create `TransactionProcessorFactory`
* [ ] Integrate with service layer

---

## Phase 7 — Abstract Factory

* [ ] Create notification interfaces
* [ ] Create Email notification
* [ ] Create SMS notification
* [ ] Create notification factories
* [ ] Integrate with transaction processing

---

## Phase 8 — Prototype

* [ ] Create `TransactionTemplate`
* [ ] Implement cloning
* [ ] Create template registry
* [ ] Save templates
* [ ] Clone templates
* [ ] Create recurring transactions

---

## Phase 9 — Modern Java

* [ ] Lambda expressions
* [ ] Streams
* [ ] Optional
* [ ] Functional interfaces
* [ ] Method references
* [ ] `var`
* [ ] Text Blocks
* [ ] Records
* [ ] Sealed classes
* [ ] Pattern matching
* [ ] Switch expressions
* [ ] Record patterns
* [ ] Virtual Threads
* [ ] Sequenced Collections

---

## Phase 10 — Database Transactions

* [ ] JDBC `commit()`
* [ ] JDBC `rollback()`
* [ ] Transfer transaction
* [ ] Failure recovery
* [ ] Atomic debit/credit

---

## Phase 11 — Testing

* [ ] Unit tests
* [ ] Pattern tests
* [ ] Repository tests
* [ ] Service tests
* [ ] Database integration tests
* [ ] Exception tests
* [ ] Transaction rollback tests

---

## Phase 12 — Documentation

* [ ] Architecture diagram
* [ ] Database ER diagram
* [ ] Design Pattern documentation
* [ ] Java feature documentation
* [ ] Setup instructions
* [ ] CLI examples
* [ ] Test examples
* [ ] Screenshots
* [ ] Resume project description

---

# 📊 Final Feature Matrix

| Area             | Technologies / Features                               |
| ---------------- | ----------------------------------------------------- |
| Language         | Java 21                                               |
| Fundamentals     | Core Java                                             |
| OOP              | Encapsulation, inheritance, polymorphism, abstraction |
| Functional       | Lambda, Streams, Functional Interfaces                |
| Collections      | List, Set, Map, Sequenced Collections                 |
| Modern Java      | `var`, Text Blocks, Records                           |
| Type System      | Sealed Classes                                        |
| Pattern Matching | `instanceof`, `switch`, Record Patterns               |
| Concurrency      | Virtual Threads                                       |
| Database         | PostgreSQL                                            |
| Persistence      | JDBC                                                  |
| SQL              | PostgreSQL SQL                                        |
| Design Patterns  | Singleton                                             |
| Design Patterns  | Factory Method                                        |
| Design Patterns  | Abstract Factory                                      |
| Design Patterns  | Builder                                               |
| Design Patterns  | Prototype                                             |
| Exceptions       | Custom exceptions                                     |
| Testing          | JUnit 5                                               |
| Version Control  | Git                                                   |
| Hosting          | GitHub                                                |

---

# 🎓 Learning Outcomes

After completing EasyBank, the developer should be able to explain:

### Core Java

* How Java manages objects
* Interfaces vs abstract classes
* Generics
* Collections
* Streams
* Lambdas
* Optional
* Exception handling
* Immutability

### JDBC

* How JDBC works
* Connection management
* PreparedStatement
* ResultSet
* Transactions
* Commit and rollback
* Resource management

### Design Patterns

* When Singleton is appropriate
* When Factory Method is appropriate
* Difference between Factory Method and Abstract Factory
* Why Builder is useful
* When Prototype is useful

### Modern Java

* Records
* Sealed classes
* Pattern matching
* Text blocks
* Switch expressions
* Virtual threads
* Sequenced collections

---

# 🚀 Project Philosophy

EasyBank is intentionally built **without frameworks first**.

The objective is to understand what frameworks such as Spring eventually automate.

Instead of:

```text
Spring
 ↓
Repository
 ↓
JPA
 ↓
Database
```

we build:

```text
Core Java
 ↓
Service
 ↓
Repository
 ↓
JDBC
 ↓
PostgreSQL
```

This makes the underlying Java concepts explicit.

---

# ⚠️ Disclaimer

EasyBank is an educational project.

It is **not suitable for handling real money, real customer information, or production banking operations**.

Real banking software requires significantly stronger security, auditing, authorization, encryption, compliance, reliability, and operational controls.

---

# 📚 Java Reference

The Java feature plan is based on the official Java SE documentation. Java 21 made Record Patterns and Pattern Matching for `switch` permanent, while Records and Pattern Matching for `instanceof` became permanent in Java 16 and Sealed Classes in Java 17.

---

# 📌 Current Project Status

```text
Project              : EasyBank
Java                 : 21
Architecture         : Core Java
Database             : PostgreSQL
Persistence          : JDBC
Build Tool           : None
Framework            : None

Creational Patterns
-------------------
Singleton            : Planned
Factory Method       : Planned
Abstract Factory     : Planned
Builder              : Planned
Prototype            : Planned

Java 8–21 Features
-------------------
Status               : Planned

Database
-------------------
Schema               : Planned
JDBC Connection      : Planned
Repositories         : Planned
Transactions         : Planned
```

---

# ⭐ Final Goal

The completed project should demonstrate:

```text
                    EASYBANK
                       │
       ┌───────────────┼────────────────┐
       │               │                │
    CORE JAVA       DESIGN PATTERNS    JDBC
       │               │                │
   Java 8 → 21       5 Creational    PostgreSQL
       │               │
       ├─ Lambda       ├─ Singleton
       ├─ Streams      ├─ Factory Method
       ├─ Optional     ├─ Abstract Factory
       ├─ Records      ├─ Builder
       ├─ Sealed       └─ Prototype
       ├─ Pattern Matching
       ├─ Text Blocks
       ├─ Switch Expressions
       ├─ Virtual Threads
       └─ Sequenced Collections
```

**Target:** A complete Core Java banking application that demonstrates both **strong Java fundamentals and practical design-pattern usage**, with PostgreSQL persistence through JDBC.
