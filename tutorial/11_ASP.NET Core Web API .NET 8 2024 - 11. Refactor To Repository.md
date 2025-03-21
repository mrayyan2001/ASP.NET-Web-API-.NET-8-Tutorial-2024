## Refactoring to the Repository Pattern

In this video, we'll refactor existing database code using the Repository Pattern. The Repository Pattern encapsulates database logic within dedicated functions, improving code organization and maintainability. A repository serves as a central point for all database interactions.

### Implementing the Repository Interface

First, we'll define an interface that outlines the contract for our repository. This interface will specify the methods our repository must implement.

```csharp
public interface IStockRepository
{
    Task<Stock?> GetByIdAsync(int id);
    Task<Stock> CreateAsync(Stock stockModel);
    Task<Stock?> UpdateAsync(int id, UpdateStockRequestDto updateStockRequestDto);
    Task<Stock?> DeleteAsync(int id);
}
```

Let's break down each method:

*   **`GetByIdAsync(int id)`**: Retrieves a `Stock` entity by its ID. It returns a nullable `Stock?` because the database might not find a matching record, resulting in a `null` value.
*   **`CreateAsync(Stock stockModel)`**: Creates a new `Stock` entity in the database. It takes a `Stock` object as input and returns the created `Stock` entity.
*   **`UpdateAsync(int id, UpdateStockRequestDto updateStockRequestDto)`**: Updates an existing `Stock` entity. It takes the ID of the stock to update and an `UpdateStockRequestDto` containing the updated data. The method returns the updated `Stock` entity, which can be null if the stock is not found.
*   **`DeleteAsync(int id)`**: Deletes a `Stock` entity from the database based on its ID. It takes the ID of the stock to delete and returns the deleted `Stock` entity, which can be null if the stock is not found.

### Implementing the StockRepository Class

Now, let's implement the `IStockRepository` interface in a `StockRepository` class. This class will contain the actual database interaction logic.

```csharp
public class StockRepository : IStockRepository
{
    private readonly AppDbContext _context;

    public StockRepository(AppDbContext context)
    {
        _context = context;
    }

    public async Task<Stock?> GetByIdAsync(int id)
    {
        return await _context.Stocks.FindAsync(id);
    }

    public async Task<Stock> CreateAsync(Stock stockModel)
    {
        _context.Stocks.Add(stockModel);
        await _context.SaveChangesAsync();
        return stockModel;
    }

    public async Task<Stock?> UpdateAsync(int id, UpdateStockRequestDto updateStockRequestDto)
    {
        var existingStock = await _context.Stocks.FirstOrDefaultAsync(x => x.Id == id);

        if (existingStock == null)
        {
            return null;
        }

        existingStock.CompanyName = updateStockRequestDto.CompanyName;
        existingStock.PurchasePrice = updateStockRequestDto.PurchasePrice;
        existingStock.LastDividend = updateStockRequestDto.LastDividend;
        existingStock.Industry = updateStockRequestDto.Industry;
        existingStock.MarketCap = updateStockRequestDto.MarketCap;

        await _context.SaveChangesAsync();
        return existingStock;
    }

    public async Task<Stock?> DeleteAsync(int id)
    {
        var stockModel = await _context.Stocks.FirstOrDefaultAsync(x => x.Id == id);

        if (stockModel == null)
        {
            return null;
        }

        _context.Stocks.Remove(stockModel);
        await _context.SaveChangesAsync();
        return stockModel;
    }
}
```

Key points:

*   **Dependency Injection:** The `StockRepository` class takes an `AppDbContext` instance through its constructor. This enables dependency injection, making the repository more testable and flexible.
*   **`GetByIdAsync` Implementation:** Uses `_context.Stocks.FindAsync(id)` to retrieve a stock by its ID.
*   **`CreateAsync` Implementation:** Adds the new `stockModel` to the `Stocks` collection, saves the changes to the database using `SaveChangesAsync`, and returns the created `stockModel`.
*   **`UpdateAsync` Implementation:**
    *   Retrieves the existing stock using `FirstOrDefaultAsync`.
    *   If the stock doesn't exist, it returns `null`.
    *   Updates the properties of the `existingStock` with values from the `updateStockRequestDto`.
    *   Saves the changes to the database.
    *   Returns the updated `existingStock`.
*   **`DeleteAsync` Implementation:**
    *   Retrieves the stock to delete using `FirstOrDefaultAsync`.
    *   If the stock doesn't exist, it returns `null`.
    *   Removes the `stockModel` from the `Stocks` collection.
    *   Saves the changes to the database.
    *   Returns the deleted `stockModel`.

### Refactoring the Controller

Now that we have our repository, we can refactor the controller to use it. First, inject the `IStockRepository` into the controller's constructor.

```csharp
public class StocksController : ControllerBase
{
    private readonly IStockRepository _stockRepository;

    public StocksController(IStockRepository stockRepository)
    {
        _stockRepository = stockRepository;
    }

    // ... other actions
}
```

Then, update the controller actions to use the repository methods:

```csharp
[HttpGet("{id}")]
public async Task<IActionResult> GetStock(int id)
{
    var stock = await _stockRepository.GetByIdAsync(id);

    if (stock == null)
    {
        return NotFound();
    }

    return Ok(stock);
}

[HttpPost]
public async Task<IActionResult> CreateStock(Stock stockModel)
{
    var createdStock = await _stockRepository.CreateAsync(stockModel);
    return CreatedAtAction(nameof(GetStock), new { id = createdStock.Id }, createdStock);
}

[HttpPut("{id}")]
public async Task<IActionResult> UpdateStock(int id, UpdateStockRequestDto updateStockRequestDto)
{
    var updatedStock = await _stockRepository.UpdateAsync(id, updateStockRequestDto);

    if (updatedStock == null)
    {
        return NotFound();
    }

    return Ok(updatedStock);
}

[HttpDelete("{id}")]
public async Task<IActionResult> DeleteStock(int id)
{
    var stockToDelete = await _stockRepository.DeleteAsync(id);

    if (stockToDelete == null)
    {
        return NotFound();
    }

    return NoContent();
}
```

### Benefits of Using the Repository Pattern

*   **Improved Code Organization:** Separates database logic from the controller, making the code cleaner and easier to understand.
*   **Testability:** Enables easier unit testing by allowing you to mock the repository and test the controller logic in isolation.
*   **Maintainability:** Simplifies database changes, as you only need to modify the repository implementation.
*   **Reusability:** The repository can be reused across multiple controllers or services.

By implementing the Repository Pattern, we've successfully refactored our database code, resulting in a more maintainable, testable, and organized application.