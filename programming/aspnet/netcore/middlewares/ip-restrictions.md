# IP Restrictions


Sometimes we need to limit from users to access to Web App by IP address, 
and this can be done by adding a Middleware to filtering the request.

## Adding White List IP Address Config

First configure the whitelisted IP addresses in the `appsettings.json` by adding a new section called `IpSecuritySettings`

```json
"IpSecuritySettings": {
  "AllowedIPs": "::1,0.0.0.0,127.0.0.1" // comma-delimited list of whitelisted IP addresses, seperator ',' or ';'
}
```

Add the `IpSecuritySettings.cs` for the configuration:

```cs
public class IpSecuritySettings
{
    public string AllowedIPs { get; set; }

    public string[] AllowedIPsList
    {
        get
        {
            return AllowedIPs
                .Split(new Char[] { ',', ';' }, StringSplitOptions.RemoveEmptyEntries)
                .Where(x => !string.IsNullOrWhiteSpace(x))
                .Select(s => s.Trim())
                .ToArray();
        }
    }
}
```

Once you have added the `IpSecuritySettings.cs`, you will need to configure the options in the `Startup.cs`.
Add the IpSecuritySettings in `ConfigureServices` with the Configure extension method:

```cs
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        var builder = new ConfigurationBuilder()
            .SetBasePath(env.ContentRootPath)
            .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
            .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
            .AddEnvironmentVariables();
        Configuration = builder.Build();
    }

    public IConfigurationRoot Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        // Adds services required for using options.
        services.AddOptions();

        // Register the IConfiguration instance
        services.AddSingleton<IConfiguration>(Configuration);

        // Configure settings
        services.Configure<IpSecuritySettings>(Configuration.GetSection("IpSecuritySettings"));

        // Add framework services.
        services.AddMvc();
    }
}
```

## Setup the middleware

Add a new file `IpRestrictionMiddleware.cs` to filtering the request which is not whitelisted IP addresses:

```cs
public class IpRestrictionMiddleware
{
    public readonly RequestDelegate Next;
    public readonly ILogger<IpRestrictionMiddleware> Logger;
    public readonly IpSecuritySettings IpSecuritySettings;

    public IpRestrictionMiddleware(RequestDelegate next, ILogger<IpRestrictionMiddleware> logger, IOptions<IpSecuritySettings> ipSecuritySettings)
    {
        Next = next;
        Logger = logger;
        IpSecuritySettings = ipSecuritySettings.Value;
    }

    public async Task Invoke(HttpContext context)
    {
        var ipAddress = (string)context.Connection.RemoteIpAddress?.ToString();
        if (!IpSecuritySettings.AllowedIPsList.Contains(ipAddress))
        {
            context.Response.StatusCode = 403;
            Logger.LogInformation($"Forbidden Request from Remote IP address: {ipAddress}");
            return;
        }

        await Next(context);
    }
}
```

In the `Startup.cs`, add `IpRestrictionMiddleware` as the middleware in the pipeline in `Configure` with the UseMiddleware extension method:

```cs
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
    loggerFactory.AddConsole(Configuration.GetSection("Logging"));
    loggerFactory.AddDebug();

    // Enable IP Restriction Middleware
    app.UseMiddleware<IpRestrictionMiddleware>();

    app.UseMvc();
}
```

## Example

[IP_Restriction](https://github.com/nicholashew/aspnet-samples/tree/master/Middleware/IP_Restriction)
