## Data Transfer Objects (DTOs) in .NET: A Practical Guide

If you're working with .NET, you've likely encountered the acronym DTO, which stands for **Data Transfer Object**. While the term can be broadly applied, in the context of .NET Core APIs, DTOs primarily serve to shape data for requests and responses.

### Why Use DTOs?

DTOs help you avoid exposing your entire model to the user or storing unnecessary data in your database. They allow you to:

*   **Trim Down Data:** Return only the necessary information to the client.
*   **Format Data:** Structure data in a specific way for requests and responses.
*   **Enhance Security:** Prevent sensitive data (e.g., passwords) from being exposed.
*   **Data Annotations:** Apply data annotations to properties.

### DTO Use Cases

1.  **Response DTOs:** Control what data is returned from your API. For example, you might want to return a username without the password.
2.  **Request DTOs:** Define the expected format for data submitted to your API. This allows you to validate and shape the data before it reaches your model.

### Example: Implementing DTOs Without AutoMapper

Let's walk through a practical example of using DTOs to shape the response data for a `Stock` model.

#### 1. Create DTOs Folder

Create a folder named `DTOs` to house your DTOs. It's good practice to further organize these into subfolders based on the model they represent (e.g., `DTOs/Stock`).

#### 2. Create a `StockDto`

Create a class named `StockDto` that mirrors the properties you want to expose from your `Stock` model.

```csharp
// DTOs/Stock/StockDto.cs
public class StockDto
{
    public int Id { get; set; }
    public string Symbol { get; set; }
    public string CompanyName { get; set; }
    public decimal Purchase { get; set; }
    public decimal LastDiv { get; set; }
    public string Industry { get; set; }
    public long MarketCap { get; set; }
}
```

In this example, we're excluding the `Comments` property from the `Stock` model in our `StockDto`.

#### 3. Create a Mapper Class

Create a class named `StockMappers` to house your mapping logic. This class will contain extension methods to convert between your model and DTO.

```csharp
// DTOs/Stock/StockMappers.cs
public static class StockMappers
{
    public static StockDto ToStockDto(this Stock stockModel)
    {
        return new StockDto
        {
            Id = stockModel.Id,
            Symbol = stockModel.Symbol,
            CompanyName = stockModel.CompanyName,
            Purchase = stockModel.Purchase,
            LastDiv = stockModel.LastDiv,
            Industry = stockModel.Industry,
            MarketCap = stockModel.MarketCap
        };
    }
}
```

This extension method takes a `Stock` model as input and returns a new `StockDto` populated with the desired properties.

#### 4. Implement the Mapper in the Controller

In your `StockController`, use the `Select` method to apply the mapping to your data before returning it in the response.

```csharp
// StockController.cs
[HttpGet]
public ActionResult<IEnumerable<StockDto>> GetAllStocks()
{
    var stocks = _context.Stocks.Select(s => s.ToStockDto()).ToList();
    return Ok(stocks);
}

[HttpGet("{id}")]
public ActionResult<StockDto> GetStock(int id)
{
    var stock = _context.Stocks.FirstOrDefault(s => s.Id == id);

    if (stock == null)
    {
        return NotFound();
    }

    return Ok(stock.ToStockDto());
}
```

The `Select` method acts as a mapper, transforming each `Stock` object into a `StockDto` object.

### Benefits of This Approach

*   **Explicit Mapping:** You have full control over which properties are mapped and how.
*   **No External Dependency:** You don't need to rely on AutoMapper or other mapping libraries.
*   **Readability:** The mapping logic is clear and easy to understand.

### Conclusion

DTOs are a powerful tool for shaping data in .NET APIs. By using DTOs, you can improve security, reduce data transfer size, and tailor your API responses to the specific needs of your clients. While AutoMapper is a popular choice for mapping, creating your own mappers provides greater control and avoids external dependencies.