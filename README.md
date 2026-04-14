# Easy Bank

A lightweight, console-based banking application built with Java that demonstrates core banking operations with a clean, layered architecture.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Project Architecture](#project-architecture)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Core Components](#core-components)
- [Getting Started](#getting-started)
- [Usage Guide](#usage-guide)
- [API/Service Methods](#apiservice-methods)
- [Exception Handling](#exception-handling)
- [Code Quality & Validation](#code-quality--validation)
- [Future Enhancements](#future-enhancements)

---

## 🎯 Overview

**Easy Bank** is a simple yet comprehensive banking application that demonstrates object-oriented principles, design patterns, and best practices in Java. It simulates essential banking operations with an interactive CLI interface and implements a clean layered architecture with repositories, services, and domain models.

---

## ✨ Features

### Core Banking Operations
- **Account Management**: Create accounts with SAVINGS or CURRENT types
- **Deposits**: Add funds to accounts with transaction tracking
- **Withdrawals**: Withdraw funds with balance validation
- **Fund Transfers**: Transfer money between accounts with validation
- **Account Statements**: View complete transaction history with timestamps
- **Account Search**: Search accounts by customer name
- **Account Listing**: View all accounts with current balances

### Key Capabilities
- ✅ Input validation for all operations
- ✅ Transaction history tracking with timestamps
- ✅ Custom exception handling
- ✅ Unique account number generation
- ✅ Memory-based data storage (in-memory repositories)
- ✅ Balance verification before withdrawals/transfers
- ✅ Clean separation of concerns

---

## 🏗️ Project Architecture

The application follows a **3-layer Layered Architecture**:

```
┌─────────────────────────────────────────┐
│          Presentation Layer             │
│        (EasyBank.java - CLI UI)         │
└──────────────────┬──────────────────────┘
                   │
┌──────────────────┴──────────────────────┐
│          Service Layer                  │
│  (BankService & BankServiceImpl)         │
│  - Business Logic                       │
│  - Validation                           │
│  - Orchestration                        │
└──────────────────┬──────────────────────┘
                   │
┌──────────────────┴──────────────────────┐
│          Persistence Layer              │
│  (Repositories - AccountRepository,     │
│   CustomerRepository,                   │
│   TransactionRepository)                │
│  - Data Storage & Retrieval             │
└──────────────────┬──────────────────────┘
                   │
┌──────────────────┴──────────────────────┐
│          Domain Layer                   │
│  (POJOs - Account, Customer,            │
│   Transaction, Type)                    │
└─────────────────────────────────────────┘
```

---

## 🛠️ Tech Stack

| Component | Technology |
|-----------|-----------|
| **Language** | Java 11+ |
| **Architecture** | Layered Architecture |
| **Design Patterns** | Service Pattern, Repository Pattern, Functional Interfaces |
| **Data Storage** | In-Memory Collections (HashMap, ArrayList) |
| **UI** | Console-Based (Scanner) |
| **Build** | Maven/Gradle Compatible |

---

## 📁 Project Structure

```
src/
├── app/
│   └── EasyBank.java                 # Main application entry point & CLI UI
│
├── domain/
│   ├── Account.java                  # Account entity
│   ├── Customer.java                 # Customer entity
│   ├── Transaction.java              # Transaction entity
│   └── Type.java                     # Enum for transaction types
│
├── service/
│   ├── BankService.java              # Service interface (contract)
│   └── impl/
│       └── BankServiceImpl.java       # Service implementation (business logic)
│
├── repository/
│   ├── AccountRepository.java        # Account data access
│   ├── CustomerRepository.java       # Customer data access
│   └── TransactionRepository.java    # Transaction data access
│
├── uitl/
│   └── Validation.java               # Functional interface for validation
│
└── exceptions/
    ├── ValidationException.java      # Custom validation exception
    ├── AccountNotFoundException.java  # Account not found exception
    └── InsufficientFundsException.java # Insufficient balance exception
```

---

## 🔧 Core Components

### 1. **Domain Models**

#### Account.java
```
Represents a bank account with:
- accountNumber (unique identifier)
- customerId (link to customer)
- balance (current balance)
- accountType (SAVINGS or CURRENT)
```

#### Customer.java
```
Represents a customer with:
- id (unique identifier)
- name (customer name)
- email (customer email)
```

#### Transaction.java
```
Represents a transaction with:
- id (unique identifier)
- type (DEPOSIT, WITHDRAW, TRANSFER_IN, TRANSFER_OUT)
- accountNumber (associated account)
- amount (transaction amount)
- timeStamp (LocalDateTime - when transaction occurred)
- note (transaction description)
```

#### Type.java (Enum)
```
Transaction types:
- DEPOSIT
- WITHDRAW
- TRANSFER_IN
- TRANSFER_OUT
```

### 2. **Service Layer**

#### BankServiceImpl - Key Methods

| Method | Purpose | Validations |
|--------|---------|-------------|
| `openAccount(name, email, accountType)` | Create new account | Name required, valid email, account type is SAVINGS/CURRENT |
| `deposit(accountNumber, amount, note)` | Deposit funds | Amount >= 0, account exists |
| `withdraw(accountNumber, amount, note)` | Withdraw funds | Amount >= 0, account exists, sufficient balance |
| `transfer(fromAccount, toAccount, amount, note)` | Transfer between accounts | Amount >= 0, both accounts exist, sufficient balance, accounts differ |
| `getStatement(accountNumber)` | Get transaction history | Account exists |
| `listAccount()` | List all accounts | None |
| `searchAccountsByCustomerName(name)` | Search by customer name | None (case-insensitive) |

#### Validation Strategies
Uses functional interface `Validation<T>` with lambda expressions:
```java
- validateName: Checks for null or blank
- validateEmail: Checks for null and "@" symbol
- validateType: Validates SAVINGS or CURRENT
- validateAmountPositive: Ensures amount >= 0
```

### 3. **Repository Layer**

#### AccountRepository
- Stores accounts in `Map<String, Account>` with account number as key
- Methods: `save()`, `findAll()`, `findByNumber()`, `findByCustomerId()`

#### CustomerRepository
- Stores customers in `Map<String, Customer>` with customer ID as key
- Methods: `save()`, `findAll()`

#### TransactionRepository
- Stores transactions in `Map<String, List<Transaction>>` with account number as key
- Methods: `add()`, `findByAccount()`

### 4. **Presentation Layer**

#### EasyBank.java
Interactive CLI menu with 8 options:
1. Open Account
2. Deposit
3. Withdraw
4. Transfer
5. Account Statement
6. List Accounts
7. Search Accounts by Customer Name
8. Exit

---

## 🚀 Getting Started

### Prerequisites
- Java 11 or higher
- Any IDE (IntelliJ IDEA, Eclipse, VS Code) or command line

### Installation & Running

1. **Clone the Repository**
   ```bash
   git clone https://github.com/Shivam-GitLab/Easy-Bank.git
   cd Easy-Bank
   ```

2. **Compile the Code**
   ```bash
   javac -d bin src/**/*.java
   ```

3. **Run the Application**
   ```bash
   java -cp bin app.EasyBank
   ```

---

## 📖 Usage Guide

### Interactive Menu Example

```
Welcome To Easy Bank

Easy Bank :
    1) Open Account
    2) Deposit
    3) Withdraw
    4) Transfer
    5) Account Statement
    6) List Accounts
    7) Search Accounts by Customer Name
    0) Exit

CHOOSE : 1

Customer name: 
John Doe
Customer email: 
john@example.com
Account Type (SAVINGS/CURRENT): 
SAVINGS
Initial deposit (optional, blank for 0): 
5000
Account opened: AC000001
```

### Typical Workflow

```
1. Open Account → Deposit initial funds
2. View all accounts → Confirm creation
3. Perform transactions → Deposit/Withdraw/Transfer
4. Check Statement → View transaction history
5. Search Accounts → Find by customer name
```

---

## 💻 API/Service Methods

### BankService Interface

```java
// Account Management
String openAccount(String name, String email, String accountType)
List<Account> listAccount()
List<Account> searchAccountsByCustomerName(String name)

// Transactions
void deposit(String accountNumber, Double amount, String note)
void withdraw(String accountNumber, Double amount, String note)
void transfer(String fromAccount, String toAccount, Double amount, String note)

// Reporting
List<Transaction> getStatement(String account)
```

---

## ⚠️ Exception Handling

The application uses custom exceptions for specific error scenarios:

| Exception | Scenario | Message |
|-----------|----------|---------|
| `ValidationException` | Invalid input data | "Name is required", "Email is required", etc. |
| `AccountNotFoundException` | Account doesn't exist | "Account not found: {accountNumber}" |
| `InsufficientFundsException` | Balance too low | "Insufficient Balance" |
| `RuntimeException` | Self-transfer attempt | "Cannot transfer to your own account" |

### Error Handling Flow
```
User Input → Validation → Repository Check → Business Logic
                    ↓
             Exception Thrown
                    ↓
          Error Message Displayed
```

---

## ✔️ Code Quality & Validation

### Input Validation
- All string inputs trimmed and validated
- Email format checked (must contain "@")
- Account types restricted to SAVINGS/CURRENT
- Amounts validated as positive numbers
- Account numbers verified before operations

### Data Integrity
- Unique account numbers generated sequentially
- UUID used for customer and transaction IDs
- Timestamps recorded for all transactions
- Balance verification before withdrawals/transfers

### Best Practices Implemented
- ✅ Single Responsibility Principle (SRP)
- ✅ Dependency Injection (service receives repositories)
- ✅ Interface-based design (BankService interface)
- ✅ Functional interfaces for validation (Validation<T>)
- ✅ Streams and lambda expressions
- ✅ Optional for nullable values
- ✅ Enum for transaction types
- ✅ Meaningful exception names

---

## 🔮 Future Enhancements

### Short Term
- [ ] Persistent database integration (MySQL/PostgreSQL)
- [ ] User authentication & security
- [ ] Account overdraft protection
- [ ] Interest calculation for SAVINGS accounts
- [ ] Transaction filtering by date range
- [ ] Minimum balance requirements by account type

### Medium Term
- [ ] REST API using Spring Boot
- [ ] Web UI using Spring MVC/Thymeleaf
- [ ] Admin dashboard
- [ ] Account statement export (PDF/CSV)
- [ ] Multiple currency support
- [ ] Scheduled transactions

### Long Term
- [ ] Mobile app (Android/iOS)
- [ ] Microservices architecture
- [ ] Payment gateway integration
- [ ] Loan management module
- [ ] Investment portfolio tracking
- [ ] Real-time notifications

---

## 📝 Notes

- All data is **in-memory** and will be lost on application restart
- Account numbers are auto-generated in format `ACxxxxxx`
- Transfer creates two transaction records (one TRANSFER_OUT, one TRANSFER_IN)
- Statement transactions are sorted by timestamp
- Search is case-insensitive

---

## 👤 Author

**Shivam-GitLab**

---

## 📄 License

This project is open source and available under the MIT License.

---

## 🤝 Contributing

Contributions are welcome! Please feel free to fork the repository and submit pull requests.

---

## 📞 Support

For issues, questions, or suggestions, please open an issue on GitHub.

---

**Built with ❤️ using Java**
