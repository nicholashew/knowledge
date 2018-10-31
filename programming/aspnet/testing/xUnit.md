# xUnit

## Create a directory structures for projects

Open a shell window. Create a directory structure with the below commands:

```bash
mkdir xUnitSample
cd xUnitSample
mkdir Calculator
mkdir Calculator.Tests
```

The directory and file structure thus far should be as follows:

```bash
├── xUnitSample
│   ├── Calculator
│   ├── Calculator.Tests
```

## Create the source project

Make the `Calculator` directory the current directory, and create a new project using `dotnet new classlib`, and then rename `Class1.cs` to `Calculator.cs`

```bash
├── xUnitSample
│   ├── Calculator
|   |   ├── Calculator.csproj
|   |   ├── Calculator.cs 
│   ├── Calculator.Tests
```

To use test-driven development (TDD), you first create a maths implementation of the `Calculator.cs` class:

```cs
using System;
using System.Linq;

namespace Calculator
{
    public class Calculator
    {
        public double Add(params double[] numbers)
        {
            return numbers.Sum(x => x);
        }
    }
}
```

## Creating the test project

Make the `Calculator.Tests` directory the current directory, and create a new project using `dotnet new xunit`. 

```bash
dotnet new xunit
```

The test project requires other packages to create and run unit tests. Add the `Calculator` class library as another dependency to the project. Use the 

```bash
dotnet add reference ../Calculator/Calculator.csproj
```

Rename `UnitTest1.cs` to `CalculatorTests.cs`

```bash
├── xUnitSample
│   ├── Calculator
|   |   ├── Calculator.csproj
|   |   ├── Calculator.cs 
│   ├── Calculator.Tests
|   |   ├── Calculator.Tests.csproj
|   |   ├── CalculatorTests.cs
```

## Creating the first test

```cs
using System;
using Xunit;
using Calculator;

namespace Calculator.Tests
{
    public class CalculatorTests
    {
        [Fact]
        public void SumTest()
        {
            Assert.Equal(2, Calculator.Add(1, 1));
        }
    }
}
```

The `Fact` attribute indicates a test method that is run by the test runner. Run `dotnet test` to build the tests and class library for running the xUnit test runner contains the program entry point to run your tests. 

```bash
$ dotnet test
Build started, please wait...
Build completed.

Starting test execution, please wait...

Total tests: 1. Passed: 1. Failed: 0. Skipped: 0.
Test Run Successful.
Test execution time: 1.3167 Seconds
```

## Creating the first theory

xUnit.net includes support for two different major types of unit tests:

* **Facts** are tests which are always true. They test invariant conditions.

* **Theories** are tests which are only true for a particular set of data.

**Theories** can be useful when we need to run multiple tests with different parameters instead of duplicate the test method, see the example code below:

```cs
[Theory]
[InlineData(2, 1, 1)]
[InlineData(15, 5, 10)]
[InlineData(120, 20, 100)]
public void SumShouldCorrect(double expected, double a, double b)
{
    Assert.Equal(expected, Calculator.Add(a, b));
}
```

## Passing array in InlineData params

```cs
[Theory]
[InlineData(6, new double[] { 1, 2, 3 })]
[InlineData(20, new double[] { 5, 5, 5, 5 })]
[InlineData(120, new double[] { 5, 5, 5, 5, 100 })]
public void SumArraysShouldCorrect(double expected, object numbers)
{
    double[] _numbers = (double[])numbers;
    Assert.Equal(expected, Calculator.Add(_numbers));
}
```

## Capturing output in unit tests

Unit tests have access to a special interface which replaces previous usage of `Console` and similar mechanisms: `ITestOutputHelper`. In order to take advantage of this, just add a constructor argument for this interface, and stash it so you can use it in the unit test.

```cs
using Xunit;
using Xunit.Abstractions;

namespace Calculator.Tests
{
    public class CalculatorTests
    {
        private readonly ITestOutputHelper Output;

        public CalculatorTests(ITestOutputHelper output)
        {
            this.Output = output;
        }

        [Fact]
        public void LoggerTest()
        {
            Output.WriteLine("Hello from LoggerTest");
            Assert.True(true);
        }
    }
}
```

## Create a test base

```cs
using Xunit.Abstractions;

namespace Calculator.Tests
{
    public abstract class TestBase
    {
        /// <summary>
        /// Gets or sets the logger to use for writing text to be captured in the test results.
        /// </summary>
        protected ITestOutputHelper Output { get; set; }

        protected TestBase(ITestOutputHelper output)
        {
            this.Output = output;
        }
    }
}
```

Inherits the `TestBase`

```cs
public class EncryptionTests : TestBase
{
    public EncryptionTests(ITestOutputHelper output)
        : base(output)
    {
    }
}
```

## Worth Reading

* [Getting started with xUnit.net](https://xunit.github.io/docs/getting-started-dotnet-core)
* [xUnit Capturing Output](https://xunit.github.io/docs/capturing-output)
* [Microsoft xUnit Guidelines](https://docs.microsoft.com/en-us/dotnet/core/testing/unit-testing-with-dotnet-test)
* [7 Popular Unit Test Naming Conventions @ 2014](https://vitalflux.com/7-popular-unit-test-naming-conventions/)