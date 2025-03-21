## Building a Comment Infrastructure: From Controller to Repository

In this video, we're diving into building a comment infrastructure for our application, mirroring a real-world development process. We'll focus on setting up the controller and wiring up the interface, while ensuring clean code by using a repository pattern to abstract database interactions.

### Creating the Comment Repository Interface

First, we'll create an interface for our comment repository, named `ICommentRepository`. This interface will define the contract for interacting with comments, allowing us to easily switch implementations later.

```csharp
public interface ICommentRepository
{
    Task<List<CommentDto>> GetAllAsync();
}
```

### Implementing the Comment Repository

Next, we'll create the concrete implementation of the `ICommentRepository` interface, named `CommentRepository`. This class will handle the actual database interactions.

```csharp
public class CommentRepository : ICommentRepository
{
    private readonly ApplicationDbContext _context;

    public CommentRepository(ApplicationDbContext context)
    {
        _context = context;
    }

    public async Task<List<CommentDto>> GetAllAsync()
    {
        return await _context.Comments
            .Select(CommentMapper.ToCommentDto)
            .ToListAsync();
    }
}
```

This repository uses the `ApplicationDbContext` to access the `Comments` table and retrieves all comments. It then uses a `CommentMapper` to convert the `Comment` entities to `CommentDto` objects.

### Registering the Repository for Dependency Injection

To make the repository available for dependency injection, we need to register it in `Program.cs`.

```csharp
builder.Services.AddScoped<ICommentRepository, CommentRepository>();
```

### Building the Comment Controller

Now, let's create the `CommentController` to handle incoming requests related to comments.

```csharp
[ApiController]
[Route("api/[controller]")]
public class CommentController : ControllerBase
{
    private readonly ICommentRepository _commentRepo;

    public CommentController(ICommentRepository commentRepo)
    {
        _commentRepo = commentRepo;
    }

    [HttpGet]
    public async Task<IActionResult> GetAll()
    {
        var comments = await _commentRepo.GetAllAsync();
        return Ok(comments);
    }
}
```

This controller injects the `ICommentRepository` and uses it to retrieve all comments when the `GetAll` endpoint is called.

### Creating the Comment DTO

To transfer comment data between the API and the client, we'll create a `CommentDto` (Data Transfer Object). This DTO will contain the properties we want to expose to the client.

```csharp
public class CommentDto
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime CreatedOn { get; set; }
    public int StockId { get; set; }
}
```

### Creating the Comment Mapper

To map between the `Comment` entity and the `CommentDto`, we'll create a `CommentMapper`.

```csharp
public static class CommentMapper
{
    public static CommentDto ToCommentDto(Comment commentModel)
    {
        return new CommentDto
        {
            Id = commentModel.Id,
            Title = commentModel.Title,
            Content = commentModel.Content,
            CreatedOn = commentModel.CreatedOn,
            StockId = commentModel.StockId
        };
    }
}
```

### Testing the Infrastructure

To test our infrastructure, we'll add a test comment to the database and then call the `GetAll` endpoint.

```csharp
// Add a test comment to the database
var testComment = new Comment
{
    Title = "Test Comment",
    Content = "Test Content",
    StockId = 21,
    CreatedOn = DateTime.Now
};
_context.Comments.Add(testComment);
_context.SaveChanges();

// Call the GetAll endpoint
// Expected result: The test comment should be returned
```

### Conclusion

We've successfully built a comment infrastructure, including the repository, controller, DTO, and mapper. This infrastructure provides a solid foundation for managing comments in our application.