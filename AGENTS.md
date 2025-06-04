# Contributor Guide

## Project Description
This project is a test project for handling tasks for employees, including task creation, assignment, and completion tracking.

### Project Structure
- /src:
  - API: Contains the API controllers and DTOs for handling HTTP requests.
  - Application: Contains the business logic, commands, queries, and services.
  - Domain: Contains the domain entities and value objects.
  - DAL: Contains the data access layer, including the DbContext and migrations.
  - Infrastructure: Contains the infrastructure services for connecting to external services, logging, etc.
- /tests:
  - Application.UnitTests: Contains unit tests for the application layer.

The project should be organized in a way that follows the Clean Architecture principles, separating concerns into different layers.
Also, it should follow the Vertical Slice Architecture, where each feature is contained within its own folder, including the command, query, handler, and any related services or validators.
Example structure for a feature:
```
src
├── CodexTest.Application
│   ├── Features
│   │   ├── Task
│   │   │   ├── Commands
│   │   │   │   ├── CreateTaskCommand.cs
```

The namespace usually should follow the structure of the project, but for features, it should be more specific to the feature itself. For example:
```csharp
namespace CodexTest.Application.Commands;
```
instead of:
```csharp
namespace CodexTest.Application.Features.Task.Commands;
```

## Code Style and Guidelines
### General Guidelines
- Follow the [C# Coding Conventions](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/inside-a-program/coding-conventions) for .NET 9 provided by Microsoft.
- Use file scoped namespaces like this:
  ```csharp
  namespace MyNamespace;
  ```
  instead of:
  ```csharp
    namespace MyNamespace
    {
    }
    ```
- Use `var` when the type is obvious from the right-hand side of the assignment.
- Do not comment your code unless necessary. Use meaningful names for variables, methods, and classes to make the code self-documenting instead of relying on comments.
- Use documentation comments (`///`) for public methods and classes to describe their purpose and usage.
- Move constants to top level of the class/method or to a separate static class if they are used across multiple classes.
- Follow the [SOLID principles](https://en.wikipedia.org/wiki/SOLID) for object-oriented design. Follow DRY, KISS, and YAGNI principles.
- Use dependency injection for managing dependencies between classes.

### Tests Writing Guidelines
- Use NUnit for writing unit tests.
- Use the `Shouldly` library for assertions to make your tests more readable.
- Use `Moq` for mocking dependencies in your tests.
- Use the same coding conventions as the main project.
- Name your tests using the format `MethodName_StateUnderTest_ExpectedBehavior`, e.g., `CreateTask_WhenCalled_ShouldReturnTask`.
- Follow the Arrange-Act-Assert pattern in your tests. Divide your test with comments:
  ```csharp
  // Arrange
  // Act
  // Assert
  ```

### General Implementation Guidelines
- Prefer using Mediator pattern for handling commands and queries. You can use the `MediatR` library for this purpose.
- Use CQRS (Command Query Responsibility Segregation) pattern to separate read and write operations.
- Each command or query should have its own handler contained withing the same class as the command/query itself. Follow the request-response pattern for commands and queries.
Example structure for a command:
```csharp
namespace CodexTest.Application.Commands;
public class CreateTaskCommand
{
    public record Request(string Title, string Description) : IDomainCommand<Result<TaskDto>>; // or other appropriate type
    public record Response(Result<TaskDto> Result);

    public class Handler : IRequestHandler<Request, Result<TaskDto>>
    {
        ... // Implementation of the handler
    }
}
```

- Use AutoMapper for mapping between entities and DTOs (Data Transfer Objects).
- Use FluentValidation for validating input models.
- Use Result pattern for returning results from methods/commands/queries. This helps in handling errors and success cases uniformly.
- Use logging for important events and errors. Use `ILogger<T>` for logging.
- Use exceptions in exceptional cases. Do not use exceptions for control flow. Use results for business logic flow.
- Map Results to HTTP responses in your controllers.

### DB implementation guidelines
- Use PostgreSQL as the database.
- Use Entity Framework Core for data access. Do not use repository pattern because EF Core already provides a good abstraction for data access. But abstract your DbContext to a separate class/interface to keep your code clean and maintainable.
- Use migrations for managing database schema changes. Use the `dotnet ef` CLI commands to create and apply migrations.
- Use data seeding for populating initial data in the database when app starts in development mode. Use HasData method in your DbContext to seed data.
- Use `DbContext` for data access. Do not use raw SQL queries unless absolutely necessary.
- Use Code First approach for defining your database schema. Define your entities and their relationships in the `DbContext` class.
- Use Fluent API for configuring your entities and their relationships. Avoid using data annotations unless necessary.
- Use Guid as the primary key for your entities.
- When writing LINQ queries, use method syntax instead of query syntax for consistency with the rest of the codebase.
- When writing EF LINQ queries, use `AsNoTracking()` for read-only queries to improve performance. Try to avoid using `Include()` unless absolutely necessary, as it can lead to performance issues. Try to optimize your queries to load only the necessary data.