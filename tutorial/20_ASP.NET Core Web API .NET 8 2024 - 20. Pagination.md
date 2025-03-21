## Implementing Pagination with Skip and Take

Pagination is a crucial technique for handling large datasets in applications. Instead of loading all records at once, which can lead to performance issues and crashes, pagination divides the data into manageable pages. This approach enhances user experience and optimizes resource usage.

This guide will demonstrate how to implement pagination using the "Skip" and "Take" methods, a common and effective strategy.

### Understanding Skip and Take

The core of pagination lies in the "Skip" and "Take" methods.

*   **Skip:** This method instructs the system to skip a specified number of elements from the beginning of the dataset.
*   **Take:** This method instructs the system to take a specified number of elements after the "Skip" operation.

By combining "Skip" and "Take," you can effectively retrieve a specific "page" of data.

### Implementing Pagination in Your Application

Let's implement pagination in your application using a query object and a repository.

1.  **Query Object:**

    Create a query object to hold pagination parameters. This object will encapsulate the page number and page size.

    ```csharp
    public class StockQuery
    {
        public int PageNumber { get; set; } = 1; // Default to the first page
        public int PageSize { get; set; } = 20; // Default page size
    }
    ```

2.  **Repository:**

    Modify your repository's `GetAll` method to incorporate pagination logic.

    ```csharp
    public async Task<IEnumerable<Stock>> GetAll(StockQuery query)
    {
        // Calculate the number of records to skip
        var skipNumber = (query.PageNumber - 1) * query.PageSize;

        // Apply Skip and Take to the data retrieval
        return await _context.Stocks
            .Skip(skipNumber)
            .Take(query.PageSize)
            .ToListAsync();
    }
    ```

    *   The `skipNumber` is calculated by subtracting 1 from the `PageNumber` (because page numbers typically start at 1) and multiplying by the `PageSize`.
    *   The `Skip` method is used to skip the calculated number of records.
    *   The `Take` method is used to take the specified `PageSize` records after skipping.

3.  **Testing:**

    Test the pagination implementation using a tool like Swagger.

    *   Set the `PageNumber` and `PageSize` parameters in your query.
    *   Verify that the results returned correspond to the specified page and page size.

### Example

Let's say you have 10 records and a `PageSize` of 3.

*   **Page 1:** `PageNumber = 1`, `Skip = (1 - 1) * 3 = 0`, `Take = 3`. Returns records 1, 2, and 3.
*   **Page 2:** `PageNumber = 2`, `Skip = (2 - 1) * 3 = 3`, `Take = 3`. Returns records 4, 5, and 6.
*   **Page 3:** `PageNumber = 3`, `Skip = (3 - 1) * 3 = 6`, `Take = 3`. Returns records 7, 8, and 9.
*   **Page 4:** `PageNumber = 4`, `Skip = (4 - 1) * 3 = 9`, `Take = 3`. Returns record 10.

### Conclusion

Implementing pagination with "Skip" and "Take" is a straightforward and effective way to handle large datasets in your application. This approach improves performance, enhances user experience, and ensures your application remains responsive even with a growing number of records.