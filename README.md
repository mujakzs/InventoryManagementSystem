# Clean Architecture .NET Solution Setup

This guide explains how to structure a .NET solution using **Clean Architecture**, including recommended folder structures, project dependencies, and how to set everything up in Visual Studio.

---

## 📁 Solution Structure

A common way to organize projects in a .NET solution reflecting **Clean Architecture**:

```
YourProjectSolution.sln
├── src
│   ├── YourProject.Domain.csproj (.NET Class Library)
│   │   ├── Entities/
│   │   ├── Aggregates/
│   │   ├── Enums/
│   │   ├── Events/
│   │   ├── Exceptions/
│   │   ├── Interfaces/ (e.g., IProductRepository.cs)
│   │   ├── Specifications/
│   │   ├── Validators/Guards/
│   │   └── ValueObjects/
│   │
│   ├── YourProject.Application.csproj (.NET Class Library)
│   │   ├── Features/ (Organized by feature, e.g., Products, Orders)
│   │   │   ├── Products/
│   │   │   │   ├── Commands/
│   │   │   │   ├── Queries/
│   │   │   │   └── DTOs/
│   │   ├── Common/
│   │   │   ├── Interfaces/ (e.g., IEmailSender.cs, IDateTimeProvider.cs)
│   │   │   ├── Behaviors/ (MediatR pipeline behaviors)
│   │   │   └── Mappings/ (AutoMapper profiles)
│   │   └── Exceptions/
│   │
│   ├── YourProject.Infrastructure.csproj (.NET Class Library)
│   │   ├── Persistence/
│   │   │   ├── DataContext/ (e.g., ApplicationDbContext.cs)
│   │   │   ├── Repositories/ (e.g., ProductRepository.cs)
│   │   │   ├── Migrations/
│   │   │   └── Configurations/ (EF Core entity type configurations)
│   │   ├── Services/ (EmailSender, DateTimeProvider, etc.)
│   │   └── Identity/
│   │
│   └── YourProject.WebApi.csproj (ASP.NET Core Web API)
│       ├── Controllers/
│       ├── Middleware/
│       ├── Filters/
│       ├── Extensions/ (Service registration)
│       ├── appsettings.json
│       └── Program.cs (Composition Root)
│
└── tests
    ├── YourProject.Domain.UnitTests.csproj
    ├── YourProject.Application.UnitTests.csproj
    ├── YourProject.Infrastructure.IntegrationTests.csproj
    └── YourProject.Presentation.IntegrationTests.csproj
```

---

## 🚀 Setting Up the Projects in Visual Studio

### 1️⃣ Create Solution + Web API (Presentation Layer)

1. Open Visual Studio → **Create a new project**
2. Choose **ASP.NET Core Web API**
3. Name it: `YourProject.WebApi`
4. Name the solution: `YourProjectSolution`
5. Select the .NET version → **Create**

This becomes the **Presentation Layer**.

---

### 2️⃣ Add the Domain Layer

1. Right‑click **Solution** → Add → New Project
2. Select **Class Library** → Name it: `YourProject.Domain`
3. Delete `Class1.cs`
4. Add folders:

   * `Entities`
   * `Interfaces`
   * `ValueObjects`
   * `Events`
   * etc.

**Important:**
Domain should have **NO dependencies** on other projects.

---

### 3️⃣ Add the Application Layer

1. Add → New Project → **Class Library**: `YourProject.Application`
2. Delete `Class1.cs`
3. Add folders:

   * `Features`
   * `Common/Interfaces`
   * `DTOs`
   * `Behaviors`
4. Add reference:
   **Application → Domain**

---

### 4️⃣ Add the Infrastructure Layer

1. Add → New Project → **Class Library**: `YourProject.Infrastructure`
2. Delete `Class1.cs`
3. Add folders:

   * `Persistence/DataContext`
   * `Repositories`
   * `Services`
4. Add reference:
   **Infrastructure → Application**

Install NuGet packages here (EF Core, providers, etc.).

---

### 5️⃣ Configure Web API Dependencies (Presentation Layer)

Add references in `YourProject.WebApi`:

* WebApi → Application
* WebApi → Infrastructure

This lets the Web API send commands/queries to Application and register services from Infrastructure.

Set **Startup Project** → Web API.

---

## 🔁 Dependency Flow (Very Important)

```
Presentation (WebApi)
    ↓
Application
    ↓
Domain

Infrastructure → Application
```

✔ WebApi **depends on** Application & Infrastructure
✔ Infrastructure **implements** Application + Domain abstractions
✔ Application **depends on** Domain
✔ Domain **depends on nothing**

This is the essence of **Clean Architecture**.

---

## 🧱 Best Practices

### ✔ Dependency Injection (DI)

Register all services in `Program.cs`.
Use constructor injection.

### ✔ MediatR + CQRS

Keeps features organized into Commands & Queries.

### ✔ FluentValidation

Strong validation in the Application layer.

### ✔ AutoMapper

Useful for mapping Domain → DTO → API Models.

### ✔ Global Error Middleware

Simplify exception responses in Web API.

### ✔ Options Pattern

Use strongly typed `IOptions<T>` for config.

### ✔ Thin Controllers

Controllers should **only**:

* Accept requests
* Validate
* Call MediatR / Application layer

### ✔ No IQueryable Leakage

Repositories should return `List`, `IEnumerable`, or DTO results.

### ✔ Testing Strategy

* Domain → pure unit tests
* Application → unit tests with mocks
* Infrastructure → integration tests
* Presentation → API integration tests

---

## 🎯 Summary

This structure ensures:

* Separation of concerns
* Loose coupling
* High testability
* Cleaner maintainability
* Easy feature scaling

Your project becomes **future‑proof**, modular, and easier to expand.

---

