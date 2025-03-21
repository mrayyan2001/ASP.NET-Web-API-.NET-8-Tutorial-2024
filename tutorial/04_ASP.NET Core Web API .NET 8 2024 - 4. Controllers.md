## Building API Controllers: A Step-by-Step Guide

API controllers act as the entry points to your application, much like doors on a house. They handle incoming requests and direct them to the appropriate resources. This guide will walk you through creating your first API controllers, focusing on the essential concepts of "list" and "detail" endpoints.

### Understanding List and Detail Endpoints

Think of your Facebook feed:

*   **List Endpoint:** Displays a collection of items, like a news feed.
*   **Detail Endpoint:** Shows specific information about a single item, like a user's profile page.

We'll create controllers to handle both types of requests.

### Creating the Stock Controller

1.  **Create a "Controllers" Folder:** In your API project, create a new folder named "Controllers" to house your controller files.

2.  **Create a StockController:** Inside the "Controllers" folder, create a new class named `StockController`. We'll follow a model-by-model approach, creating separate controllers for each data model (e.g., `StockController`, `CommentController`). This promotes separation of concerns.

3.  **Base Structure:** Start with the basic structure of the controller:

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using YourProjectName.Data; // Replace with your actual namespace
    using System.Collections.Generic;
    using System.Linq;

    namespace YourProjectName.Controllers // Replace with your actual namespace
    {
        [ApiController]
        [Route("api/[controller]")]
        public class StockController : ControllerBase
        {
            private readonly ApplicationDbContext _context;

            public StockController(ApplicationDbContext context)
            {
                _context = context;
            }

            // ... (More code will be added here) ...
        }
    }
    ```

    *   **`[ApiController]`:**  This attribute indicates that this class is an API controller.
    *   **`[Route("api/[controller]")]`:** This attribute defines the route for accessing the controller.  `[controller]` will be replaced with the controller's name (without the "Controller" suffix), resulting in a route like "api/Stock".
    *   **`ControllerBase`:**  This is the base class for API controllers, providing access to common functionality like returning HTTP responses.
    *   **`ApplicationDbContext`:**  This is your database context, allowing you to interact with the database.  It's injected into the controller using dependency injection.  The `readonly` keyword ensures that the context cannot be modified after it's initialized.

4.  **List Endpoint (Get All Stocks):** Create an endpoint to retrieve a list of all stocks.

    ```csharp
    [HttpGet]
    public ActionResult<IEnumerable<Stock>> GetStocks()
    {
        return _context.Stocks.ToList();
    }
    ```

    *   **`[HttpGet]`:**  This attribute specifies that this action handles HTTP GET requests.
    *   **`ActionResult<IEnumerable<Stock>>`:**  This is the return type of the action.  `ActionResult` allows you to return different types of HTTP responses (e.g., OK, NotFound, BadRequest).  `IEnumerable<Stock>` specifies that the action will return a collection of `Stock` objects.
    *   **`_context.Stocks.ToList()`:**  This retrieves all stocks from the database using the `ApplicationDbContext` and converts them to a list.  The `ToList()` method is crucial because it executes the query and retrieves the data from the database. Without it, you're dealing with deferred execution, where the query is only built but not executed.

5.  **Detail Endpoint (Get Stock by ID):** Create an endpoint to retrieve a specific stock by its ID.

    ```csharp
    [HttpGet("{id}")]
    public ActionResult<Stock> GetStockById(int id)
    {
        var stock = _context.Stocks.Find(id);

        if (stock == null)
        {
            return NotFound();
        }

        return stock;
    }
    ```

    *   **`[HttpGet("{id}")]`:**  This attribute specifies that this action handles HTTP GET requests and includes a route parameter named "id".  The `{id}` placeholder will be replaced with the actual ID value in the URL.
    *   **`ActionResult<Stock>`:**  This is the return type of the action.  It allows you to return either a `Stock` object or an appropriate HTTP error response.
    *   **`_context.Stocks.Find(id)`:**  This attempts to find a stock in the database with the specified ID.  The `Find` method is optimized for searching by primary key.
    *   **`if (stock == null)`:**  This checks if a stock with the given ID was found.  If not, it returns a `NotFound` result (HTTP 404).
    *   **`return stock`:**  If a stock is found, it's returned as the response.

### Configuring the Application

1.  **Register Controllers in `Program.cs`:**  In your `Program.cs` file, add the following line within the `ConfigureServices` method to register your controllers:

    ```csharp
    builder.Services.AddControllers();
    ```

2.  **Map Controllers in `Program.cs`:**  In your `Program.cs` file, add the following line within the `Configure` method (before `app.Run()`) to map your controllers:

    ```csharp
    app.MapControllers();
    ```

    This line is essential for enabling routing to your controllers.  Without it, Swagger may not function correctly, and you might encounter an HTTPS redirect error.

### Testing with Swagger

1.  **Run the Application:**  Use the command `dotnet watch run` to start your application and enable automatic reloading on code changes.

2.  **Access Swagger:**  Open your web browser and navigate to the Swagger UI endpoint (usually `https://localhost:<port>/swagger`, where `<port>` is the port number your application is running on).

3.  **Test the Endpoints:**  Use the Swagger UI to test your newly created endpoints:

    *   **`GetStocks`:**  Execute the `GetStocks` endpoint to retrieve a list of all stocks.
    *   **`GetStockById`:**  Execute the `GetStockById` endpoint, providing a valid stock ID to retrieve a specific stock.  Also, test with an invalid ID to verify that the "Not Found" response is returned.

### Creating Dummy Data

You can use SQL Server Management Studio (SSMS) or a similar tool to insert sample data into your `Stocks` table.  Here's an example of an INSERT statement:

```sql
INSERT INTO Stocks (Name, Description, PurchasePrice, LastDividend, Industry, MarketCap)
VALUES
('AAPL', 'Apple Inc.', 150.00, 0.82, 'Technology', 2500000000000),
('TSLA', 'Tesla, Inc.', 800.00, 0.00, 'Automotive', 800000000000);
```

### Key Takeaways

*   API controllers are the entry points to your application.
*   List endpoints retrieve collections of data.
*   Detail endpoints retrieve specific data based on an ID.
*   `ActionResult` provides a flexible way to return different HTTP responses.
*   Proper configuration in `Program.cs` is crucial for routing and Swagger functionality.
*   Testing with Swagger is essential to ensure your endpoints are working correctly.