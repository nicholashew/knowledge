# Simple Serilog

## Install Packages

Install Serilog packages for your own project, run the command belows to install Serilog packages.

**Package Manager**

```
PM> Install-Package Serilog.Sinks.RollingFile -Version 3.3.0
PM> Install-Package Serilog.Extensions.Logging -Version 2.0.2
PM> Install-Package Serilog.Enrichers.Environment -Version 2.1.2
PM> Install-Package Serilog.Enrichers.Thread -Version 3.0.0
PM> Install-Package Serilog.Settings.Configuration -Version 2.4.0
```

**.NET CLI**

```
dotnet add package Serilog.Sinks.RollingFile --version 3.3.0
dotnet add package Serilog.Extensions.Logging --version 2.0.2
dotnet add package Serilog.Enrichers.Environment --version 2.1.2
dotnet add package Serilog.Enrichers.Thread --version 3.0.0
dotnet add package Serilog.Settings.Configuration --version 2.4.0
```

## Configuration

Configure the Serilog in the `appsettings.json` in the section of “Serilog”:

```json
{
  "Serilog": {
    "MinimumLevel": "Information",
    "WriteTo": [
      {
        "Name": "RollingFile",
        "Args": {
          "pathFormat": "Logs\\log-{Date}.log",
          "outputTemplate": "{Timestamp:yyyy-MM-dd HH:mm:ss.fff zzz} [{MachineName}][Thread:{ThreadId}] [{Level}] {SourceContext} - {Message}{NewLine}{Exception}"
        }
      }
    ]
  }
}
```

Serilog levels can be overridden per logging source as below, for example we only want to filter Microsoft and System category from Level Information and below.

```json
{
  "Serilog": {
    "MinimumLevel": {
      "Default": "Information",
      "Override": {
        "Microsoft": "Warning",
        "System": "Warning"
      }
    }
  }
}
```

Configure the serilog options in the `Startup.cs` with the Startup method:

```cs
public class Startup
    {
        public IConfiguration Configuration { get; }

        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;

            //Setup Serilog
            Log.Logger = new LoggerConfiguration()
                .ReadFrom.Configuration(Configuration)
                .Enrich.WithMachineName()
                .Enrich.WithThreadId()
                .CreateLogger();
        }
```

Add the Serilog in `ConfigureServices` with the AddLogging extension method:

```cs
public void ConfigureServices(IServiceCollection services)
{
    // Add Serilog
    services.AddLogging(loggingBuilder => loggingBuilder.AddSerilog(dispose: true));

    services.AddMvc();
}
```

## Using a Static Logger

Loggers are created using a static Logger object:

```cs
Log.Logger.Information("No one listens to me!");
```

The example above will use a static logger that does not record events anywhere.

## Using a Logger

Create an API controller `TestsController.cs`. We now are injecting an instance of ILogger. Of which we are using to Logging inside our Get\(\) method.

Update the **TestsController** class with the following.

```cs
[Route("api/[controller]")]
public class TestsController : Controller
{
    private readonly ILogger<TestsController> _logger;

    public TestsController(ILogger<TestsController> logger)
    {
        _logger = logger;
    }

    // GET api/tests
    [HttpGet]
    public string Get()
    {            
        _logger.LogTrace("LogTrace");
        _logger.LogInformation("LogInformation");
        _logger.LogWarning("LogWarning");
        _logger.LogError("LogWarning");
        _logger.LogCritical("LogCritical");

        return Ok("Success");
    }
}
```

Much like `TestsController.cs` we inject an instance of ILogger and perform logging inside of GET request.

If we run our app now and consume the API endpoint `http://localhost/api/tests`, we should end up with some logging results in the console.

## Create a Logger Extensions

This is an optional extension for logger to define custom logging messages.

```cs
using Microsoft.Extensions.Logging;
using System;

public static class LoggerExtensions
{
    /// <summary>
    /// Formats and writes a trace log message with a default event id.
    /// </summary>
    /// <param name="logger">The <see cref="ILogger"/> to write to.</param>
    /// <param name="exception">The exception to log.</param>
    /// <param name="message">Format string of the log message.</param>
    /// <param name="args">An object array that contains zero or more objects to format.</param>
    public static void LogTrace(this ILogger logger, Exception exception, string message = null, params object[] args)
    {
        logger.LogTrace(default(EventId), exception, message, args);
    }

    /// <summary>
    /// Formats and writes a information log message with a default event id.
    /// </summary>
    /// <param name="logger">The <see cref="ILogger"/> to write to.</param>
    /// <param name="exception">The exception to log.</param>
    /// <param name="message">Format string of the log message.</param>
    /// <param name="args">An object array that contains zero or more objects to format.</param>
    public static void LogInformation(this ILogger logger, Exception exception, string message = null, params object[] args)
    {
        logger.LogInformation(default(EventId), exception, message, args);
    }

    /// <summary>
    /// Formats and writes a warning log message with a default event id.
    /// </summary>
    /// <param name="logger">The <see cref="ILogger"/> to write to.</param>
    /// <param name="exception">The exception to log.</param>
    /// <param name="message">Format string of the log message.</param>
    /// <param name="args">An object array that contains zero or more objects to format.</param>
    public static void LogWarning(this ILogger logger, Exception exception, string message = null, params object[] args)
    {
        logger.LogWarning(default(EventId), exception, message, args);
    }

    /// <summary>
    /// Formats and writes a error log message with a default event id.
    /// </summary>
    /// <param name="logger">The <see cref="ILogger"/> to write to.</param>
    /// <param name="exception">The exception to log.</param>
    /// <param name="message">Format string of the log message.</param>
    /// <param name="args">An object array that contains zero or more objects to format.</param>
    public static void LogError(this ILogger logger, Exception exception, string message = null, params object[] args)
    {
        logger.LogError(default(EventId), exception, message, args);
    }

    /// <summary>
    /// Formats and writes a critical log message with a default event id.
    /// </summary>
    /// <param name="logger">The <see cref="ILogger"/> to write to.</param>
    /// <param name="exception">The exception to log.</param>
    /// <param name="message">Format string of the log message.</param>
    /// <param name="args">An object array that contains zero or more objects to format.</param>
    public static void LogCritical(this ILogger logger, Exception exception, string message = null, params object[] args)
    {
        logger.LogCritical(default(EventId), exception, message, args);
    }
}
```

## Resources

* [Serilog](https://github.com/serilog/serilog)
* [Sample SimpleSerilog](https://github.com/nicholashew/aspnet-samples/tree/master/Logger/SimpleSerilog)
