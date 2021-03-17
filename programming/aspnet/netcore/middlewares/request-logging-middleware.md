# Request Logging Middleware

This describes how to create a custom middleware that logging for simple request information in ASP.NET Core 2.x.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System;
using System.Diagnostics;
using System.Threading.Tasks;

namespace MyKnowledge.Middleware
{
    /// <summary>
    /// Request logging middleware for ASP.NET Core 2.2
    /// </summary>
    public class RequestLoggingMiddleware
    {
        public readonly RequestDelegate _next;
        public readonly ILogger<RequestLoggingMiddleware> _logger;

        public RequestLoggingMiddleware(RequestDelegate next, ILogger<RequestLoggingMiddleware> logger)
        {
            _next = next;
            _logger = logger;
        }

        public async Task Invoke(HttpContext httpContext)
        {
            try
            {
                var request = httpContext.Request;

                // this is an example to capture for specific request path by using Path.StartsWithSegments
                if (request.Path.StartsWithSegments("/api/v1"))
                {
                    var start = Stopwatch.GetTimestamp();
                    var requestTime = DateTime.UtcNow;
                    await _next(httpContext);
                    var elapsedMs = GetElapsedMilliseconds(start, Stopwatch.GetTimestamp());

                    //string requestDateTime = requestTime.ToString("yyyy-MM-dd HH:mm:ss.fff");
                    string requestMethod = request.Method;
                    int statusCode = httpContext.Response.StatusCode;
                    string requestPath = request.Path;
                    string queryString = request.QueryString.ToString();

                    //[yyyy-MM-dd HH:mm:ss.fff] HTTP {RequestMethod} {RequestPath} responded {StatusCode} in {Elapsed:0.0000} ms                    
                    _logger.LogInformation(
                      $"HTTP {requestMethod} {requestPath} responded {statusCode} in {elapsedMs} ms. Query_STR: {queryString}"
                    );
                }
                else
                {
                    await _next(httpContext);
                }
            }
            catch (System.Exception)
            {
                await _next(httpContext);
            }
        }

        static double GetElapsedMilliseconds(long start, long stop)
        {
            return (stop - start) * 1000 / (double)Stopwatch.Frequency;
        }
    }
}
```

If you are using [Serilog](https://github.com/serilog/serilog-aspnetcore), you can enable it with just one line in your application's `Startup.cs`, add the middleware with UseSerilogRequestLogging():

```cs
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
	if (env.IsDevelopment())
	{
		app.UseDeveloperExceptionPage();
	}
	else
	{
		app.UseExceptionHandler("/Home/Error");
	}

	app.UseSerilogRequestLogging(); // <-- Add this line       
```

## Reference: 
- [serilog-aspnetcore](https://github.com/serilog/serilog-aspnetcore)
- [Safely Logging API Requests and Responses in ASP.NET Core - SALIL PONDE](https://salslab.com/a/safely-logging-api-requests-and-responses-in-asp-net-core/)
