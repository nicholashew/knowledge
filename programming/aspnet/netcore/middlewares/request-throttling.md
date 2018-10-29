# Request throttling in .NET Core MVC

Security in apis are important and we might not want the apis we build to be overly used. This could be to prevent DDoS attacks or to make sure no one tries to brute-force-use your api. To solve this problem we could use a small attribute function that allows for throttling of a specific endpoint.

## Attribute code

Example code with [Serilog](https://serilog.net/) logging.

```cs
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.Filters;
using Microsoft.Extensions.Caching.Memory;
using Serilog;
using System;
using System.Net;

namespace Sample.Filters
{

    /// <summary>
    /// Request throttling attribute to prevent DDoS attacks 
	/// or to make sure no one tries to brute-force-use your api.
    /// </summary>
    [AttributeUsage(AttributeTargets.Method)]
    public class ThrottleAttribute : ActionFilterAttribute
    {
        /// <summary>
        /// Optional, default gets from ActionExecutingContext ActionDescriptor DisplayName
        /// </summary>
        public string Name { get; set; }

        /// <summary>
        /// Gets and sets the Throttling seconds
        /// </summary>
        public int Seconds { get; set; }

        /// <summary>
        /// Gets and sets the Throttle message. 
        /// Default "You may only perform this action every {n} seconds."
        /// </summary>
        public string Message { get; set; }

        private static MemoryCache Cache { get; } = new MemoryCache(new MemoryCacheOptions());

        public override void OnActionExecuting(ActionExecutingContext c)
        {
            if (string.IsNullOrWhiteSpace(Name))
                Name = c.ActionDescriptor.DisplayName;
            
            var ipAddress = c.HttpContext.Connection.RemoteIpAddress?.ToString();
            var key = string.Concat(Name, "-", ipAddress);

            if (!Cache.TryGetValue(key, out bool entry))
            {
                var cacheEntryOptions = new MemoryCacheEntryOptions()
                    .SetAbsoluteExpiration(TimeSpan.FromSeconds(Seconds));

                Cache.Set(key, true, cacheEntryOptions);
            }
            else
            {
                if (string.IsNullOrEmpty(Message))
                    Message = "You may only perform this action every {n} seconds.";

                c.Result = new ContentResult { Content = Message.Replace("{n}", Seconds.ToString()) };
                c.HttpContext.Response.StatusCode = (int)HttpStatusCode.Conflict;

                Log.Logger.Information("Throttle filtered from action: {0}, ip: {1}", Name, ipAddress);
            }
        }
    }
}
```

## Example usage

```cs
[Route("api/[controller]")]
public class ActionsController : Controller
{
    // GET /api/actions/throttle
    [HttpGet("throttle")]
    // Only allow access every 5 seconds
    [Throttle(Name = "ThrottleTest", Seconds = 5)]
    public IActionResult GetThrottle() => new
    {
        Message = "OK!"
    };

	// GET /api/actions/throttle-2
    [HttpGet("throttle-2")]
    // Only allow access every 5 seconds
    [Throttle(Seconds = 5)]
    public IActionResult GetThrottle2() => new
    {
        Message = "OK!"
    };
}
```

## References

* [Request throttling in .NET Core MVC](https://www.johanbostrom.se/blog/request-throttling-in-net-core-mvc-and-api)