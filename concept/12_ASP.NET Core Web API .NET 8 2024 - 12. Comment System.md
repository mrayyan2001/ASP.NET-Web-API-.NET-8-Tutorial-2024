Concepts and Definitions:

*   **Stock Entity:** A data structure or class representing a stock, likely containing properties like ID, name, price, etc.
*   **Comment Infrastructure:** The underlying framework and components required to support the creation, storage, retrieval, and management of comments within an application.
*   **Controller:** A component in the Model-View-Controller (MVC) architectural pattern that handles incoming requests, processes them, and returns responses. In this context, it manages requests related to comments.
*   **Wiring Up the Interface:** The process of connecting the interface (e.g., `ICommentRepository`) to its implementation (e.g., `CommentRepository`) so that the application can use the defined methods.
*   **Database Code:** Code that interacts with a database, such as queries to retrieve, insert, update, or delete data.
*   **Repository:** A design pattern that abstracts the data access layer, providing a clean interface for interacting with data sources (e.g., databases).
*   **Repository Pattern:** A software design pattern that provides an abstraction layer between the data access layer and the business logic of an application.
*   **ICommentRepository:** An interface defining the contract for a comment repository, specifying methods for interacting with comment data.
*   **CommentRepository:** A class that implements the `ICommentRepository` interface, providing the concrete implementation for accessing and managing comment data in the database.
*   **Abstraction:** The process of hiding complex implementation details and presenting a simplified interface to the user.
*   **Application DB Context:** A class that represents the database connection and provides access to the database tables (e.g., `Comments`).
*   **Dependency Injection:** A design pattern where dependencies (e.g., `ApplicationDBContext`, `ICommentRepository`) are provided to a class rather than being created within the class itself.
*   **Private Read-Only Property:** A property that can only be accessed from within the class and cannot be modified after initialization.
*   **Get All:** A method that retrieves all comments from the database.
*   **To List Async:** An asynchronous method that converts the results of a database query into a list.
*   **Dependency Injection (Program.cs):** Configuring the application to provide instances of the `CommentRepository` when needed, typically done in the `Program.cs` file.
*   **Comment Controller:** A controller class specifically designed to handle requests related to comments.
*   **Controller Base:** A base class that provides common functionality for controllers, such as handling HTTP requests and responses.
*   **API Controller:** An attribute that marks a class as an API controller, enabling features like automatic model binding and validation.
*   **DTO (Data Transfer Object):** A simple object used to transfer data between layers of an application, often used to represent data in a format suitable for transmission over a network or for display in a user interface.
*   **Comment DTO:** A DTO specifically designed to represent comment data, likely containing properties like ID, title, content, and potentially related IDs (e.g., stock ID).
*   **Navigation Property:** A property in an entity that represents a relationship to another entity (e.g., a `Comment` entity having a `Stock` navigation property).
*   **Mapper:** A component responsible for transforming data from one format to another, such as converting between database entities and DTOs.
*   **Comment Mapper:** A class or component responsible for mapping between `Comment` entities and `CommentDTO` objects.
*   **Static:** A keyword in C# that indicates a member belongs to the type itself rather than to a specific instance of the type.
*   **Select (LINQ):** A LINQ (Language Integrated Query) method that projects each element of a sequence into a new form. In this context, it's used to transform a list of `Comment` objects into a list of `CommentDTO` objects.
*   **Seed:** The process of populating a database with initial data.
*   **Test Comment:** A sample comment used for testing purposes.
*   **Net Watch Run:** A command-line tool used to build and run .NET applications, automatically restarting the application when changes are detected.
*   **IAction Result:** An interface that represents the result of an action method in an ASP.NET Core controller, allowing for different types of responses (e.g., JSON, HTML).
*   **Async Task:** Represents an asynchronous operation that can return a value.