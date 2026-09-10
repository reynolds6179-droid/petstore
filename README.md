Absolutely — here is a clean `README.md` you can use for the repository.

````markdown
# PetStore

A simple **PetStore CRUD application written in C#/.NET** demonstrating a layered architecture with SQL Server data access, a command-line user interface, and automated repository tests.

The solution is divided into four projects, with each project having a clearly defined responsibility.

## Solution Architecture

```text
PetStore
│
├── PetStore.Data
│   └── POCO / Data Definition Layer
│
├── PetStore.Repository
│   └── Repository / SQL Server Data Access Layer
│
├── PetStore.Cli
│   └── Command-Line Data Entry & Query Application
│
└── PetStore.Tests
    └── Repository Unit / Integration Tests
````

### Project Responsibilities

| Project               | Responsibility                                                   |
| --------------------- | ---------------------------------------------------------------- |
| `PetStore.Data`       | Contains POCO/domain objects and data definitions                |
| `PetStore.Repository` | Handles SQL Server connections and CRUD operations               |
| `PetStore.Cli`        | Provides a command-line interface for entering and querying data |
| `PetStore.Tests`      | Contains automated tests for the repository layer                |

---

# 1. PetStore.Data

The **Data Definition Layer** contains the POCO (Plain Old CLR Object) classes used throughout the application.

This project has no database or user-interface responsibilities.

Example objects may include:

```text
Pet
Owner
PetType
Address
```

Example:

```csharp
public class Pet
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Species { get; set; }
    public string Breed { get; set; }
    public DateTime BirthDate { get; set; }
    public int OwnerId { get; set; }
}
```

The goal is to keep the data definitions independent of the database implementation and user interface.

---

# 2. PetStore.Repository

The **Repository and Data Access Layer** is responsible for communicating with SQL Server.

This project contains:

* Database connection handling
* SQL queries/commands
* Repository classes
* CRUD operations
* Mapping SQL results to POCO objects
* Transaction handling where required

A typical repository interface might look like:

```csharp
public interface IPetRepository
{
    Pet GetById(int id);

    IEnumerable<Pet> GetAll();

    int Create(Pet pet);

    void Update(Pet pet);

    void Delete(int id);
}
```

The SQL Server implementation would be responsible for executing the appropriate SQL commands.

For example:

```csharp
public class PetRepository : IPetRepository
{
    private readonly string _connectionString;

    public PetRepository(string connectionString)
    {
        _connectionString = connectionString;
    }

    public Pet GetById(int id)
    {
        // SQL Server query and POCO mapping
    }

    public IEnumerable<Pet> GetAll()
    {
        // SQL Server query
    }

    public int Create(Pet pet)
    {
        // INSERT
    }

    public void Update(Pet pet)
    {
        // UPDATE
    }

    public void Delete(int id)
    {
        // DELETE
    }
}
```

## CRUD Operations

The repository layer supports the standard CRUD operations:

```text
CREATE
  ↓
INSERT new Pet

READ
  ↓
Get a Pet by ID
Get all Pets
Query Pets

UPDATE
  ↓
Modify an existing Pet

DELETE
  ↓
Remove a Pet
```

---

# 3. PetStore.Cli

The **Command-Line Interface** provides a simple menu-driven application for interacting with the PetStore.

The application allows users to perform CRUD operations without requiring a graphical user interface.

Example menu:

```text
================================
         PET STORE
================================

1. List all pets
2. Find pet by ID
3. Add a new pet
4. Update a pet
5. Delete a pet
6. Exit

Select an option:
```

## Example Data Entry

When adding a pet:

```text
================================
        ADD NEW PET
================================

Name: Buddy
Species: Dog
Breed: Golden Retriever
Birth Date: 2021-05-15
Owner ID: 10

Save this pet? (Y/N):
```

## Example Query

```text
Enter Pet ID: 25

Pet
----------------------------
ID:         25
Name:       Buddy
Species:    Dog
Breed:      Golden Retriever
Birth Date: 05/15/2021
Owner ID:   10
```

The CLI project should contain presentation and input-validation logic only.

It should **not** contain SQL queries.

The general flow is:

```text
User
 │
 ▼
PetStore.Cli
 │
 ▼
PetStore.Repository
 │
 ▼
SQL Server
```

---

# 4. PetStore.Tests

The **Test Project** contains automated tests for the repository layer.

The objective is to verify that the repository correctly performs all CRUD operations against SQL Server.

Tests should cover:

### Create

* Create a new pet
* Verify that the pet is assigned an ID
* Verify that the inserted data can be retrieved

### Read

* Retrieve a pet by ID
* Retrieve all pets
* Verify returned POCO properties

### Update

* Update an existing pet
* Retrieve the pet again
* Verify that the changes were persisted

### Delete

* Delete an existing pet
* Verify that the pet can no longer be retrieved

### Error Cases

Tests should also cover appropriate error conditions, such as:

* Invalid ID
* Non-existent pet
* Invalid input
* Database errors where appropriate

Example test:

```csharp
[Test]
public void CreatePet_ShouldPersistPet()
{
    var pet = new Pet
    {
        Name = "Buddy",
        Species = "Dog",
        Breed = "Golden Retriever",
        BirthDate = new DateTime(2021, 5, 15),
        OwnerId = 10
    };

    var id = _repository.Create(pet);

    var result = _repository.GetById(id);

    Assert.That(result, Is.Not.Null);
    Assert.That(result.Name, Is.EqualTo("Buddy"));
    Assert.That(result.Species, Is.EqualTo("Dog"));
}
```

---

# Database

The application uses **Microsoft SQL Server** as its persistence layer.

A typical database structure might look like:

```text
PetStore
│
├── Pet
│   ├── Id
│   ├── Name
│   ├── Species
│   ├── Breed
│   ├── BirthDate
│   └── OwnerId
│
├── Owner
│   ├── Id
│   ├── FirstName
│   ├── LastName
│   └── ...
│
└── PetType
    ├── Id
    └── Name
```

Foreign-key relationships should be enforced at the database level where appropriate.

---

# Configuration

The SQL Server connection string should be configured outside of the repository implementation.

For example:

```json
{
  "ConnectionStrings": {
    "PetStore": "Server=localhost;Database=PetStore;Trusted_Connection=True;TrustServerCertificate=True;"
  }
}
```

For production environments, connection strings and credentials should not be committed to source control.

Consider using:

* Environment variables
* User Secrets
* Azure Key Vault
* Other secure configuration providers

---

# Technologies

The project is built using:

* **C#**
* **.NET**
* **Microsoft SQL Server**
* **ADO.NET / SQL Client** for database access
* **NUnit / xUnit** for automated tests
* Command-line interface

The exact .NET version can be specified in the project files.

---

# Project Dependencies

The intended dependency direction is:

```text
             ┌──────────────────┐
             │  PetStore.Cli    │
             └────────┬─────────┘
                      │
                      ▼
             ┌──────────────────┐
             │PetStore.Repository│
             └────────┬─────────┘
                      │
                      ▼
             ┌──────────────────┐
             │  PetStore.Data   │
             └──────────────────┘


             ┌──────────────────┐
             │  PetStore.Tests  │
             └────────┬─────────┘
                      │
                      ▼
             ┌──────────────────┐
             │PetStore.Repository│
             └──────────────────┘
```

The important architectural principle is that the **CLI should not directly access SQL Server**.

Instead:

```text
CLI → Repository → SQL Server
```

This keeps database access isolated and makes the repository easier to test.

---

# Getting Started

## Prerequisites

Install:

1. .NET SDK
2. Microsoft SQL Server
3. SQL Server Management Studio or another SQL Server client
4. An IDE such as Visual Studio or JetBrains Rider

Verify the .NET installation:

```bash
dotnet --version
```

---

# Clone the Repository

```bash
git clone <repository-url>
cd PetStore
```

---

# Configure the Database

Create the PetStore database in SQL Server.

Example:

```sql
CREATE DATABASE PetStore;
GO
```

Create the required tables using the database initialization script.

For example:

```sql
CREATE TABLE Pet
(
    Id INT IDENTITY(1,1) PRIMARY KEY,
    Name NVARCHAR(100) NOT NULL,
    Species NVARCHAR(50) NOT NULL,
    Breed NVARCHAR(100),
    BirthDate DATE,
    OwnerId INT
);
```

Update the connection string to point to your SQL Server instance.

---

# Build the Solution

From the repository root:

```bash
dotnet restore
dotnet build
```

---

# Run the Application

```bash
dotnet run --project PetStore.Cli
```

The command-line menu will be displayed:

```text
================================
         PET STORE
================================

1. List all pets
2. Find pet by ID
3. Add a new pet
4. Update a pet
5. Delete a pet
6. Exit

Select an option:
```

---

# Run the Tests

Run all tests with:

```bash
dotnet test
```

To run only the repository tests:

```bash
dotnet test PetStore.Tests
```

A successful test run should verify the repository's CRUD functionality.

---

# Design Goals

The application is intentionally divided into separate projects to demonstrate separation of concerns.

### Data Layer

Responsible for:

```text
POCOs
Data structures
Domain objects
```

### Repository Layer

Responsible for:

```text
SQL Server
Queries
CRUD operations
Data mapping
```

### CLI Layer

Responsible for:

```text
Menus
User input
Input validation
Displaying results
```

### Test Layer

Responsible for:

```text
Automated testing
Repository verification
CRUD validation
Error scenarios
```

This separation makes the application easier to maintain, test, and extend.

---

# Future Improvements

Possible enhancements include:

* Dependency Injection
* Repository interfaces
* Async database operations
* Generic repository patterns
* Entity Framework Core
* More comprehensive input validation
* Logging
* Configuration through `appsettings.json`
* Integration-test database containers
* Test database initialization/cleanup
* Transaction support
* Pagination for large queries
* Search by pet name, species, or breed
* Owner management
* Pet adoption/status tracking
* REST API layer

A future version could add an ASP.NET Core Web API without changing the fundamental repository/data architecture:

```text
                   ┌───────────────┐
                   │   CLI Client  │
                   └───────┬───────┘
                           │
                   ┌───────▼───────┐
                   │   Web API     │
                   └───────┬───────┘
                           │
                   ┌───────▼──────────┐
                   │   Repository     │
                   └───────┬──────────┘
                           │
                   ┌───────▼──────────┐
                   │    SQL Server    │
                   └──────────────────┘
```

---

# Repository Structure

A possible final repository structure is:

```text
PetStore/
│
├── PetStore.sln
│
├── PetStore.Data/
│   ├── Pet.cs
│   ├── Owner.cs
│   ├── PetType.cs
│   └── PetStore.Data.csproj
│
├── PetStore.Repository/
│   ├── IPetRepository.cs
│   ├── PetRepository.cs
│   ├── DatabaseConnection.cs
│   └── PetStore.Repository.csproj
│
├── PetStore.Cli/
│   ├── Program.cs
│   ├── Menu.cs
│   ├── PetMenu.cs
│   └── PetStore.Cli.csproj
│
├── PetStore.Tests/
│   ├── PetRepositoryTests.cs
│   ├── DatabaseTestFixture.cs
│   └── PetStore.Tests.csproj
│
├── database/
│   ├── create-database.sql
│   ├── create-tables.sql
│   └── seed-data.sql
│
├── .gitignore
└── README.md
```

---

# License

This project is provided for educational and demonstration purposes.

```

This structure keeps the four projects very cleanly separated and makes the repository layer independently testable.
```
# petstore
