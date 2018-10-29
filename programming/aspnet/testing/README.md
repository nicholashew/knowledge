# Testing

There are four main stages of testing that need to be completed before a program can be cleared for use: unit testing, integration testing, system testing, and acceptance testing.

* [Unit Testing](#unit-testing)
* [System Testing](#system-testing)
* [Acceptance Testing](#acceptance-testing)
* [Integration Testing](#integration-testing)

## Unit Testing

During this first round of testing, the program is submitted to assessments that focus on specific units or components of the software to determine whether each one is fully functional. The main aim of this endeavor is to determine whether the application functions as designed. In this phase, a unit can refer to a function, individual program or even a procedure, and a White-box Testing method is usually used to get the job done. One of the biggest benefits of this testing phase is that it can be run every time a piece of code is changed, allowing issues to be resolved as quickly as possible. It’s quite common for software developers to perform unit tests before delivering software to testers for formal testing.

### Example

In the example below, it’s a simple domain service which takes repository as the dependency and uses it to persist data to some database. This service has only one method, which creates a product entity and runs logic on it, so calculates a discount. Consider now, what should we put inside unit tests? Should we care about the persistence layer and checking whether the entity is actually saved into DB? **No**, because it’s not the part of the unit we’re trying to test. Our test should **ONLY** check whether domain logic works as expected in the isolated environment. 

```cs
public class ProductsService : IProductsService
{
    private readonly IProductsRepository _productsRepository;
 
    public ProductsService(IProductsRepository productsRepository)
    {
        _productsRepository = productsRepository;
    }
 
    public ProductEntity Create(string name, decimal price)
    {
        decimal discount;
 
        if(price > 100) 
            discount  = 50;
        else if (price > 50) 
            discount = 30;
        else
            discount = 0;
 
        var product = new ProductEntity 
        {
            Name = name,
            Price = price,
            discount = discount
        };
 
        var createdProduct = _productsRepository.Create(product);
 
        return createdProduct;
    }
}

public class ProductsService_Create_Tests
{
    private readonly IProductsService _productsService;
 
    public ProductsService_Create_Tests()
    {
        var mockProductsRepository = new MockProductsRepository();
        _productsService = new ProductsService(mockProductsRepository);
    }
    
    [Fact]
    public void ProductsService_Create_Returns_New_Product_With_No_Discount_For_Price_Lower_Than_50()
    {
        var createdProduct = _productsService.Create("TV", 30);
        Assert.Equal(0, createdProduct.Discount);
    }
}
```

## Integration Testing

Integration testing allows individuals the opportunity to combine all of the units within a program and test them as a group. This testing level is designed to find interface defects between the modules/functions. This is particularly beneficial because it determines how efficiently the units are running together. Keep in mind that no matter how efficiently each unit is running, if they aren’t properly integrated, it will affect the functionality of the software program. In order to run these types of tests, individuals can make use of various testing methods, but the specific method that will be used to get the job done will depend greatly on the way in which the units are defined.

### Example

Imagine that you’ve just completed your ASP.NET Core app. All unit tests passed, so you know that your domain logic behaves properly, but you’re still not sure whether all your code works properly. The easiest way to verify that would be to run the app, call the API and do the assertion on the response. That’s exactly what we are going to do! However, before we move further, we should take a look at the app that we are going to test (just the controller part):

```cs
[Route("api/Users")]
public class UsersController : Controller
{
    private readonly IEnumerable<UserModel> _users = new List<UserModel>
    {
        new UserModel { "John", Age = 13 },
        new UserModel { "Sam", Age = 24 },
        new UserModel { "Pit", Age = 44 },
    };
 
 
    [HttpGet("Filtered")]
    public IEnumerable<UserModel> GetFilteredUsers([FromQuery] ushort minAgeFilter)
        => _users.Where(user => user.Age >= minAgeFilter);
}
```

Next, preparing test project. The first thing we need to is creating a test project with [xUnit](https://xunit.github.io/)

```
dotnet new xunit -n MyApp.IntegrationTests
```

Having the project installed, we need to add three dependencies:
- Project we want to test (project reference) – obvious
- [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost) (package reference) – provides TestHost class which runs our app during the tests
- [RestEase](https://www.nuget.org/packages/RestEase/) (package reference) – strongly typed HTTP client for .NET Core
- Recommend using [Shouldly](https://www.nuget.org/packages/Shouldly/) for nice assertions but it’s not required

And the example integration test for the API:

```cs
public interface IUsersApi
{
    [Get("/api/Users/Filtered")]
    Task<IEnumerable<UserModel>> GetFilteredUsersAsync(ushort minAgeFilter);
}
 
public class UsersApi_IntegrationTests
{
    [Fact]
    public async void UsersApi_GetFilteredUsersAsync_Returns_Properly_Filtered_Users()
    {
        ushort minAgeFilter = 13;
        var users = await _usersApi.GetFilteredUsersAsync(minAgeFilter);
 
        users.ShouldAllBe(user => user.Age > minAgeFilter);
    }
 
 
    private readonly IUsersApi _usersApi;
    private readonly TestServer _testServer;
 
    public UsersApi_IntegrationTests()
    {
        var webHostBuilder = new WebHostBuilder().UseStartup<TestedAppStartup>();
 
        _testServer = new TestServer(webHostBuilder);
        _usersApi = RestClient.For<IUsersApi>(_testServer.CreateClient());
    }
}
```

The first line is very similar to what you can find inside Program class (in ASP.NET Core 2.0). It creates a new instance of WebHostBuilder class which is then passed to the TestServer’s constructor. This class will use the builder to create a temporary test environment for our app. Using other words, we’ll be able to call the API inside each integration test.

In some scenarios, you might also want to check the response object returned from the API to test HTTP status code or something different. Fortunately, this is very simple with RestEase:

```cs
[Fact]
public async void UsersApi_GetFilteredUsersAsync_Returns_Properly_Filtered_Users()
{
    ushort minAgeFilter = 13;
    var response = await _usersApi.GetFilteredUsersAsync(minAgeFilter);
 
    response.ResponseMessage.StatusCode.ShouldBe(HttpStatusCode.OK);
 
    var users = response.GetContent();
    users.ShouldAllBe(user => user.Age > minAgeFilter);
}
```

## System Testing

System testing is the first level in which the complete application is tested as a whole. The goal at this level is to evaluate whether the system has complied with all of the outlined requirements and to see that it meets Quality Standards. System testing is undertaken by independent testers who haven’t played a role in developing the program. This testing is performed in an environment that closely mirrors production. System Testing is very important because it verifies that the application meets the technical, functional, and business requirements that were set by the customer.

## Acceptance Testing

The final level, Acceptance testing (or User Acceptance Testing), is conducted to determine whether the system is ready for release. During the Software development life cycle, requirements changes can sometimes be misinterpreted in a fashion that does not meet the intended needs of the users. During this final phase, the user will test the system to find out whether the application meets their business’ needs. Once this process has been completed and the software has passed, the program will then be delivered to production.

As you can see, the extensiveness of these tests is just another reason why bringing your software testers in early is important. When a program is more thoroughly tested, a greater number of bugs will be detected; this ultimately results in higher quality software.

## Useful Resources

- [xUnit](https://xunit.github.io/) - free, open source, community-focused unit testing tool for the .NET Framework
- [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost) (package reference) – provides TestHost class which runs our app during the tests
- [RestEase](https://www.nuget.org/packages/RestEase/) (package reference) – strongly typed HTTP client for .NET Core
- [Shouldly](https://www.nuget.org/packages/Shouldly/) for nice assertions 

## References

- [The Four Levels of Software Testing](https://www.seguetech.com/the-four-levels-of-software-testing/)
- [Writing integration tests for ASP.NET Core app with xUnit, RestEase and TestHost](http://foreverframe.net/writing-integration-tests-for-asp-net-core-app-with-xunit-restease-and-testhost/ )