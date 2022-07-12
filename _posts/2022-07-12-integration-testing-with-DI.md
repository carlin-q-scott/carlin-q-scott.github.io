---
description: How to integration test an ASP.NET Core application using DI.
tags: [ dotnet, asp.net, dotnet core, integration testing, testing, NUnit, xUnit, C# ]
---
# Integration Testing ASP.NET Core With Dependency Injection

## NUnit

NUnit doesn't inject dependencies into test fixtures, but that's not a big deal because you can construct the DI container of your choice in an AssemblyFixture:

#### AssemblyFixture.cs
```csharp
[AssemblyFixture]
public static class TestFixture
{
    IHost host;

    [OneTimeSetup]
    public void OneTimeSetup()
    {
        host = Host.CreateDefaultBuilder()
            .ConfigureServices(Sut.StartUp.ConfigureServices)
            .Build();
    }
}
```
#### BaseTestFixture.cs
```csharp
[TestFixture]
public abstract class BaseTestFixture
{
    protected IServiceProvider Provider { get; private set; }

    IScope testScope;

    [SetUp]
    public void SetUp()
    {
        testScope = host.CreateScope();
        Provider = testScope.ServiceProvider;
    }

    [TearDown]
    public void TearDown()
    {
        testScope.Dispose();
    }
}
```

#### ExampleTests.cs
```csharp
[TestFixture]
public class ExampleTests : BaseTestFixture
{
    [TestCase]
    public void Test1()
    {
        // Assemble
        var sut = Provider.Resolve<SystemUnderTest>();

        // Act
        var result = sut.DoSomething();

        // Assert
        result.Should().Be(something);
    }
}
```

## xUnit

xUnit has an extension package that enables DI for your test classes, but it honestly doesn't save that much code compared to the NUnit implementation above. It however has the advantage in that it provides a structure that mimics the standard ASP.NET Startup class.

#### StartUp.cs
```csharp
    public class Startup
    {
        public IHostBuilder CreateHostBuilder() => Sut.Program.CreateHostBuilder();

        /// <summary>
        /// Configures services required by the system under test.
        /// </summary>
        /// <param name="services"></param>
        public void ConfigureServices(IServiceCollection services)
        {
            // register mocked dependencies here to override the real ones
        }

        public void Configure(ILoggerFactory loggerFactory, ITestOutputHelperAccessor accessor) =>
            loggerFactory.AddProvider(new XunitTestOutputLoggerProvider(accessor));
    }
```

#### ExampleTests.cs
```csharp
public class ExampleTests
{
    Sut sut;
    IMock<IDependency> dependencyMock;

    public ExampleTests(Sut sut, IMock<IDependency> dependencyMock)
    {
        this.sut = sut;
        this.dependencyMock = dependencyMock;
    }

    public void Test1()
    {
        // Act
        var result = sut.DoSomething();

        // Assert
        result.Should().Be(something);
    }
}