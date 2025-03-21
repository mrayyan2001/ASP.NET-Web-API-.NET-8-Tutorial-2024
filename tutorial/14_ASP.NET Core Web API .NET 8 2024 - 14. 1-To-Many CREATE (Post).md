## Creating Comments with Parent-Child Relationships in Your API

This guide explains how to implement the creation of comments within your API, establishing a parent-child relationship between stocks and their associated comments. We'll cover the necessary code and logic to ensure comments are correctly linked to their respective stocks in the database.

### Understanding the Parent-Child Relationship

When creating comments that relate to specific stocks, it's crucial to establish a clear relationship between the two. In this scenario, the stock is the "parent," and the comments are the "children." This relationship is enforced using a primary key (on the stock) and a foreign key (on the comment).

*   **Primary Key:** Uniquely identifies each stock.
*   **Foreign Key:**  In the comment model, this key references the primary key of the stock it belongs to. This is how we link a comment to its parent stock.

### Pseudo Code Overview

When creating a comment, you must associate it with a parent stock. This is achieved by including the `StockId` when creating a new comment.

```
// Pseudo-code for creating a comment
CreateComment(StockId, CommentData) {
  // 1. Validate StockId
  // 2. Create new comment object
  // 3. Set comment.StockId = StockId
  // 4. Save comment to database
}
```

### Implementing the Comment Creation Endpoint

Let's walk through the code required to create a comment and link it to a stock.

#### 1. Controller Setup

First, we'll create an HTTP POST endpoint in the controller to handle comment creation requests.

```csharp
[HttpPost("{stockId}/comments")]
public async Task<IActionResult> Create([FromRoute] int stockId, [FromBody] CreateCommentDto commentDto)
{
    // ... implementation details ...
}
```

*   `[HttpPost("{stockId}/comments")]`: Defines the route for creating a comment, including the `stockId` as a route parameter.
*   `[FromRoute] int stockId`:  Extracts the `stockId` from the route.
*   `[FromBody] CreateCommentDto commentDto`:  Binds the data from the request body to a `CreateCommentDto` object.

#### 2. CreateCommentDto

The `CreateCommentDto` is a Data Transfer Object that defines the structure of the data required to create a comment.

```csharp
public class CreateCommentDto
{
    public string Title { get; set; }
    public string Content { get; set; }
}
```

This DTO includes the `Title` and `Content` properties, which are the essential pieces of information needed from the user to create a comment.

#### 3. Stock Existence Check

Before creating a comment, it's crucial to verify that the specified stock actually exists. To do this, we'll create a method in the `IStockRepository` to check for stock existence.

**IStockRepository Interface:**

```csharp
public interface IStockRepository
{
    Task<bool> StockExists(int id);
}
```

**StockRepository Implementation:**

```csharp
public class StockRepository : IStockRepository
{
    private readonly ApplicationDbContext _context;

    public StockRepository(ApplicationDbContext context)
    {
        _context = context;
    }

    public async Task<bool> StockExists(int id)
    {
        return await _context.Stocks.AnyAsync(s => s.Id == id);
    }
}
```

**Controller Implementation:**

```csharp
// Inside the Create action in the controller
if (!await _stockRepo.StockExists(stockId))
{
    return BadRequest("Stock does not exist.");
}
```

#### 4. Mapping the DTO to a Comment Model

Next, we need to map the `CreateCommentDto` to a `Comment` model.  We'll use AutoMapper for this.

**Comment Mapper Profile:**

```csharp
public class CommentProfile : Profile
{
    public CommentProfile()
    {
        CreateMap<CreateCommentDto, Comment>()
            .ForMember(dest => dest.StockId, opt => opt.MapFrom(src => src.StockId));
    }
}
```

**Controller Implementation:**

```csharp
var commentModel = _mapper.Map<Comment>(commentDto);
commentModel.StockId = stockId;
```

#### 5. Creating the Comment in the Database

Now that we have our `Comment` model, we can use the `ICommentRepository` to add it to the database.

**ICommentRepository Interface:**

```csharp
public interface ICommentRepository
{
    Task<Comment> CreateAsync(Comment comment);
}
```

**CommentRepository Implementation:**

```csharp
public class CommentRepository : ICommentRepository
{
    private readonly ApplicationDbContext _context;

    public CommentRepository(ApplicationDbContext context)
    {
        _context = context;
    }

    public async Task<Comment> CreateAsync(Comment comment)
    {
        _context.Comments.Add(comment);
        await _context.SaveChangesAsync();
        return comment;
    }
}
```

**Controller Implementation:**

```csharp
var createdComment = await _commentRepo.CreateAsync(commentModel);
```

#### 6. Returning the Created Comment

Finally, we return a `CreatedAtAction` result, which includes the URI of the newly created comment and the comment data.

```csharp
return CreatedAtAction(nameof(GetCommentById), new { id = createdComment.Id }, _mapper.Map<CommentDto>(createdComment));
```

### Complete Controller Code

```csharp
[HttpPost("{stockId}/comments")]
public async Task<IActionResult> Create([FromRoute] int stockId, [FromBody] CreateCommentDto commentDto)
{
    if (!ModelState.IsValid)
    {
        return BadRequest(ModelState);
    }

    if (!await _stockRepo.StockExists(stockId))
    {
        return BadRequest("Stock does not exist.");
    }

    var commentModel = _mapper.Map<Comment>(commentDto);
    commentModel.StockId = stockId;

    var createdComment = await _commentRepo.CreateAsync(commentModel);

    return CreatedAtAction(nameof(GetCommentById), new { id = createdComment.Id }, _mapper.Map<CommentDto>(createdComment));
}
```

### Testing the Endpoint

You can test this endpoint using tools like Postman or Swagger.  Send a POST request to `/api/stocks/{stockId}/comments` with a JSON body containing the `Title` and `Content` of the comment.

### Conclusion

By following these steps, you can successfully implement comment creation with a parent-child relationship to stocks in your API. This ensures that comments are correctly associated with their respective stocks, maintaining data integrity and enabling efficient data retrieval.