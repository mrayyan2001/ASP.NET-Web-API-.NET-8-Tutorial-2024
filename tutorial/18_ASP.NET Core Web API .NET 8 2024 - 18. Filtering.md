## Enhancing Data Retrieval with `AsQueryable` and Filtering in ASP.NET Core

In this segment, we'll explore how to leverage `AsQueryable` in ASP.NET Core to implement filtering capabilities for your API endpoints. This approach allows for dynamic query construction, enabling more refined data retrieval from your database.

### The Power of `AsQueryable`

When fetching data from a database, you typically use methods like `ToList()` to execute the query and retrieve the results. However, `ToList()` immediately fires the SQL query, limiting your ability to further refine the data. This is where `AsQueryable` comes into play.

`AsQueryable` delays the execution of the SQL query, allowing you to chain additional operations like filtering, sorting, and pagination before the query is sent to the database. This approach is particularly useful when you want to dynamically build your query based on user input or other conditions.

### Implementing Filtering with `AsQueryable`

Let's dive into a practical example of filtering stocks by company name and symbol using `AsQueryable`.

1.  **Define a Query Object:**

    To handle query parameters, we'll create a `QueryObject` class to encapsulate the filter criteria. This class will contain properties for the company name and symbol.

    ```csharp
    // Helpers/QueryObject.cs
    public class QueryObject
    {
        public string? Symbol { get; set; }
        public string? CompanyName { get; set; }
    }
    ```

2.  **Modify the Controller:**

    In your controller, you'll modify the `Get` method to accept a `QueryObject` as a parameter. The `[FromQuery]` attribute indicates that the parameters will be passed via query string.

    ```csharp
    // Controllers/StockController.cs
    [HttpGet]
    public async Task<ActionResult<IEnumerable<Stock>>> Get([FromQuery] QueryObject query)
    {
        var stocks = await _stockRepository.GetAllAsync(query);
        return Ok(stocks);
    }
    ```

3.  **Update the Repository Interface and Implementation:**

    Update the `IStockRepository` interface to accept the `QueryObject` as a parameter.

    ```csharp
    // Interfaces/IStockRepository.cs
    Task<IEnumerable<Stock>> GetAllAsync(QueryObject query);
    ```

    Implement the `GetAllAsync` method in the `StockRepository` to utilize `AsQueryable` and apply the filtering logic.

    ```csharp
    // Repositories/StockRepository.cs
    public async Task<IEnumerable<Stock>> GetAllAsync(QueryObject query)
    {
        var stocks = _context.Stocks.AsQueryable();

        if (!string.IsNullOrWhiteSpace(query.CompanyName))
        {
            stocks = stocks.Where(s => s.CompanyName.Contains(query.CompanyName));
        }

        if (!string.IsNullOrWhiteSpace(query.Symbol))
        {
            stocks = stocks.Where(s => s.Symbol.Contains(query.Symbol));
        }

        return await stocks.ToListAsync();
    }
    ```

4.  **Testing the Implementation:**

    With these changes, you can now filter stocks by company name and symbol by passing query parameters to your API endpoint. For example:

    *   `GET /stocks?companyName=Microsoft` will return stocks with "Microsoft" in their company name.
    *   `GET /stocks?symbol=MSFT` will return stocks with the symbol "MSFT".
    *   `GET /stocks?companyName=Tesla&symbol=TSLA` will return stocks with the company name "Tesla" and the symbol "TSLA".

### Benefits of Using `AsQueryable`

*   **Dynamic Query Construction:** Build queries based on user input or other conditions.
*   **Improved Performance:** Execute the query only after all filtering and other operations are applied, potentially reducing the amount of data transferred from the database.
*   **Code Reusability:** Create reusable filtering logic that can be applied to different API endpoints.
*   **Maintainability:** Keep your code organized and easier to maintain by separating the query construction logic from the data retrieval logic.

By using `AsQueryable` and query objects, you can create flexible and efficient APIs that provide users with the ability to filter and retrieve the exact data they need.