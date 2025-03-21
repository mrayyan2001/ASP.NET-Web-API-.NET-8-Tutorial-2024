## Getting Started with .NET Core Web API: A Beginner's Guide

Welcome to the .NET Core Web API course! This course is designed for beginners and will guide you through building a stock market social media platform. You'll learn how to:

*   Store stocks and equities
*   Implement user-based commenting on stocks
*   Add stocks to user portfolios
*   Implement user login and security with JWT

All development will be done in Visual Studio Code. Let's dive in!

### Prerequisites

Before we begin, ensure you have the following installed:

*   **Visual Studio Code:** A lightweight and versatile code editor.
*   **Visual Studio (Community Edition):** Provides the .NET runtime and other essential components.
*   **SQL Server (Express Edition):** A database management system for storing application data.
*   **SQL Server Management Studio (SSMS):** A tool for managing SQL Server databases.

### Installation Guide

#### 1. Visual Studio Code

1.  Open your web browser and search for "Visual Studio Code."
2.  Navigate to the official Visual Studio Code website.
3.  Download the installer for your operating system (Windows, macOS, Linux).
4.  Run the installer and follow the on-screen instructions. The installation process is straightforward and requires minimal configuration.

#### 2. Visual Studio (Community Edition)

1.  Open your web browser and search for "Visual Studio."
2.  Navigate to the official Visual Studio website.
3.  Download the Community Edition (it's free!).
4.  Run the installer.
5.  During installation, you'll be presented with a list of workloads. Select the following:
    *   **.NET desktop development**
    *   **ASP.NET and web development**
    *   (Optional) Desktop development with C++
6.  Click "Install" and wait for the installation to complete.

#### 3. SQL Server (Express Edition)

1.  Open your web browser and search for "SQL Server."
2.  Navigate to the official Microsoft SQL Server downloads page.
3.  Download the Express Edition.
4.  Run the installer.
5.  Choose the "Basic" installation option for a simplified setup.
6.  Follow the on-screen instructions to complete the installation.

#### 4. SQL Server Management Studio (SSMS)

1.  After installing SQL Server, the installer will provide a link to download SSMS.
2.  Click the link to download the SSMS installer.
3.  Run the installer and follow the on-screen instructions to complete the installation.

### Project Setup

#### 1. Create a Project Folder

1.  Choose a location on your computer to store your project files.
2.  Create a new folder and name it "FinShark" (or any name you prefer).

#### 2. Open the Project Folder in Visual Studio Code

1.  Open Visual Studio Code.
2.  Click "File" > "Open Folder."
3.  Navigate to the "FinShark" folder you created and select it.

#### 3. Create the Web API Project

1.  Open the Visual Studio Code terminal (View > Terminal).
2.  Navigate into the project folder: `cd FinShark`
3.  Run the following command to create a new .NET Core Web API project:

    ```bash
    dotnet new webapi -o API
    ```

    This command creates a new folder named "API" within your project folder and generates the necessary files for a basic Web API.

#### 4. Navigate to the API Folder

1.  In the Visual Studio Code terminal, navigate into the API folder:

    ```bash
    cd API
    ```

    It's crucial to be in the correct directory for subsequent commands.

### Running the Web API

1.  In the Visual Studio Code terminal (within the "API" folder), run the following command:

    ```bash
    dotnet watch run
    ```

    This command builds and runs the Web API project. It also enables "watch" mode, which automatically restarts the application whenever you make changes to the code.

2.  Once the application is running, you'll see a message in the terminal indicating the URL where the API is accessible (usually `https://localhost:<port>/swagger`).

3.  Open your web browser and navigate to the Swagger URL. You should see the Swagger UI, which provides a user-friendly interface for testing your API endpoints.

### Cleaning Up `Program.cs`

The `Program.cs` file is the entry point of your application. Let's clean it up to start with a clean slate:

1.  Open the `Program.cs` file in Visual Studio Code.
2.  Remove the minimal API endpoint (the `app.MapGet` code block).
3.  Remove the `record` definition.
4.  Ensure your `Program.cs` file contains the following essential code:

    ```csharp
    var builder = WebApplication.CreateBuilder(args);

    // Add services to the container.

    builder.Services.AddControllers();
    // Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
    builder.Services.AddEndpointsApiExplorer();
    builder.Services.AddSwaggerGen();

    var app = builder.Build();

    // Configure the HTTP request pipeline.
    if (app.Environment.IsDevelopment())
    {
        app.UseSwagger();
        app.UseSwaggerUI();
    }

    app.UseHttpsRedirection();

    app.UseAuthorization();

    app.MapControllers();

    app.Run();
    ```

### API Fundamentals: CRUD Operations

APIs primarily perform CRUD operations:

*   **Create:** Adding new data (HTTP POST)
*   **Read:** Retrieving existing data (HTTP GET)
*   **Update:** Modifying existing data (HTTP PUT/PATCH)
*   **Delete:** Removing data (HTTP DELETE)

Understanding these operations is crucial for designing and building effective APIs.

### Recommended Visual Studio Code Extensions

Install these extensions to enhance your development experience:

*   **C# Dev Kit:** Provides C# language support, debugging, and more.
*   **.NET Extension Pack:** A collection of essential .NET development tools.
*   **NuGet Package Manager:** Simplifies the process of adding and managing NuGet packages.
*   **Prettier:** Automatically formats your code for improved readability.
*   **C# Extensions by jchannon:** Adds C# file templates.

To install extensions:

1.  Click the Extensions icon in the Visual Studio Code Activity Bar (or press `Ctrl+Shift+X`).
2.  Search for the extension by name.
3.  Click the "Install" button.

### Optional: React Frontend

This course includes an optional React frontend. If you're not interested in React, you can skip this part. If you want to use the React frontend:

1.  Clone the project repository from GitHub (link provided in the video description).
2.  Open the cloned project folder in Visual Studio Code.

The React frontend will be connected to the API later in the course.

### Conclusion

Congratulations! You've successfully set up your development environment and created a basic .NET Core Web API project. In the next video, we'll start building out the API functionality.