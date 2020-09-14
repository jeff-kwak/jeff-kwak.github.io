+++ 
draft = false
date = 2020-09-13T10:50:49-04:00
title = "Railway Web API with C#"
description = "A functional approach to returning validation errors from services in Web API"
slug = "railway-web-api-csharp" 
tags = ["csharp", "web api", "functional programming"]
categories = []
externalLink = ""
series = []
+++

## Airing of Grievances

There are a lot of ways of returning validation or business errors from a web API. A common pattern is to do some sort of exception handling and then map the exception to an HTTP status code somewhere: [Exception Handing in ASP.NET Web API](https://docs.microsoft.com/en-us/aspnet/web-api/overview/error-handling/exception-handling). This is such a common pattern, and honestly there's really nothing wrong with it at all.

However, there's just something about exceptions that bother me when they're used for problems that might happen through the normal processing of a request, such as a user input being out of bounds, a missing required field, et. al. I've always viewed exceptions as an ejection seat when things go terribly wrong, and I hate code that catches exceptions of one type just to throw exceptions of another. I completely understand why that's desirable from the point of mapping to a more explicit normalized state. However, if an exception is needed, it should be truly something wrong with the system itself and generally un-expected.

![Exceptions are ejection seats in my mind](/images/railway-web-api-csharp/ejection-seat.jpg "By Staff Sgt. Bennie J. Davis III, U.S. Air Force, Public Domain, https://commons.wikimedia.org/w/index.php?curid=10541701")

From a functional programming stand point, a nice clean way of handling errors and known failure conditions is to use something that Scott Wlaschin coined called [Railway Programming](https://fsharpforfunandprofit.com/rop/).

This post shows a basic way of solving part of this problem using C# and uses an interesting pattern borrowed directly from functional programming.

## Starting with the default web api project.

Given that you have dotnet core 3.1 and your favorite shell (I'm using the bash shell on WSL2 Ubuntu installation); create the default web api project:

```bash
mkdir WeatherApi && cd WeatherApi
dotnet new webapi
dotnet run
```

This sets up and runs the default ASP.NET Core Web API project. In this project is a sample API for a weather forecast. This is the controller `WeatherForecastController.cs`.

```csharp {linenos=true}
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        private static readonly string[] Summaries = new[]
        {
            "Freezing", "Bracing", "Chilly", "Cool", "Mild", "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
        };

        private readonly ILogger<WeatherForecastController> _logger;

        public WeatherForecastController(ILogger<WeatherForecastController> logger)
        {
            _logger = logger;
        }

        [HttpGet]
        public IEnumerable<WeatherForecast> Get()
        {
            var rng = new Random();
            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = DateTime.Now.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = Summaries[rng.Next(Summaries.Length)]
            })
            .ToArray();
        }
    }
```

Using Postman to access `GET https://localhost:5001/weatherforecast` yields a "five-day forecast" with random temperatures and random summaries. 

```json {linenos=true}
[
    {
        "date": "2020-09-13T23:20:45.3761084-04:00",
        "temperatureC": 22,
        "temperatureF": 71,
        "summary": "Bracing"
    },
    {
        "date": "2020-09-14T23:20:45.3763871-04:00",
        "temperatureC": 34,
        "temperatureF": 93,
        "summary": "Warm"
    },
    {
        "date": "2020-09-15T23:20:45.3763928-04:00",
        "temperatureC": -14,
        "temperatureF": 7,
        "summary": "Bracing"
    },
    {
        "date": "2020-09-16T23:20:45.3763933-04:00",
        "temperatureC": 31,
        "temperatureF": 87,
        "summary": "Warm"
    },
    {
        "date": "2020-09-17T23:20:45.3763937-04:00",
        "temperatureC": -17,
        "temperatureF": 2,
        "summary": "Sweltering"
    }
]
```

So -17C is "Sweltering" huh? Let's fix the randomness of the weather forecast summary. Let's use [Linear Interpolation](https://en.wikipedia.org/wiki/Linear_interpolation) to pick from the available summaries between a minimum allowed temperature (-20C) and a maximum allowed temperature (55C). Also add another endpoint to retrieve a forecast based on temperature.

```csharp {linenos=true}
    private static int Interpolate((int x, int y) p0, (int x, int y) p1, int x) => (p0.y * (p1.x - x) + p1.y * (x - p0.x)) / (p1.x - p0.x);

    private const int MinAllowedTemperature = -20;
    private const int MaxAllowedTemperature = 55;
    private WeatherForecast CalculateWeatherForecastForTemperature(int tempC)
    {
      var summary = Summaries[Interpolate((MinAllowedTemperature, 0), (MaxAllowedTemperature, Summaries.Length - 1), tempC)];
      return new WeatherForecast
      {
        Date = DateTime.Now,
        Summary = summary,
        TemperatureC = tempC
      };
    }

    [HttpGet("{tempC}")]
    public WeatherForecast GetWeatherForecastByTemperature(int tempC)
    {
      return CalculateWeatherForecastForTemperature(tempC);
    }
  }
```

Now `GET https://localhost:5001/weatherforecast/22` will reliably return a "Warm" forecast.

```json {linenos=true}
{
    "date": "2020-09-12T23:30:34.7482165-04:00",
    "temperatureC": 22,
    "temperatureF": 71,
    "summary": "Warm"
}
```

Trying `GET https://localhost:5001/weatherforecast/222` results in an array out-of-bounds exceptions. Actually my interpolation became extrapolation; hate it when that happens.

What if we tried to return an error message.

```csharp {linenos=true}
    private WeatherForecast CalculateWeatherForecastForTemperature(int tempC)
    {
      if(tempC > MaxAllowedTemperature) return $"The temperature {tempC}C is too high to calculate a weather forecast. The max allowed temperature is {MaxAllowedTemperature}C";
      
      var summary = Summaries[Interpolate((MinAllowedTemperature, 0), (MaxAllowedTemperature, Summaries.Length - 1), tempC)];
      return new WeatherForecast
      {
        Date = DateTime.Now,
        Summary = summary,
        TemperatureC = tempC
      };
    }
```

This doesn't compile of course. Because the function expects a return value of `WeatherForecast` not a string (or any sort of type representing a failure; please don't use naked strings as errors).

## Introducing Either
What we want is to _either_ return a `WeatherForecast` (the blue-sky result -- pun intended) or some error type. Introducing the `Either` type:

```csharp {linenos=true}
public sealed class Either<L, R>
{
  private L leftValue;
  private R rightValue;
  private bool isLeft;

  private Either() { }

  public static implicit operator Either<L, R>(L left) => new Either<L, R> { leftValue = left, rightValue = default(R), isLeft = true };

  public static implicit operator Either<L, R>(R right) => new Either<L, R> { leftValue = default(L), rightValue = right, isLeft = false };

  public T Match<T>(Func<L, T> leftEval, Func<R, T> rightEval) => isLeft ? leftEval(leftValue) : rightEval(rightValue);
}
```

This simple type holds _either_ an instance of some left type `L` or some right type `R`. By convention, we use the type by putting the failure condition on the left side (but it doesn't matter). Now the `CalculateWeatherForecastForTemperature` function can be made to return _either_ an error or a forecast.

```csharp {linenos=true}
private Either<WeatherForecastError, WeatherForecast> CalculateWeatherForecastForTemperature(int tempC)
{
  if (tempC > MaxAllowedTemperature)
    return new TemperatureTooHigh(tempC, MaxAllowedTemperature);

  var summary = 
    Summaries[
      Interpolate(
        (MinAllowedTemperature, 0), 
        (MaxAllowedTemperature, Summaries.Length - 1), tempC)];

  return new WeatherForecast
  {
    Date = DateTime.Now,
    Summary = summary,
    TemperatureC = tempC
  };
}
```

I have encapsulated the error conditions in a type called `WeatherForecastError` because there will be a `TemperatureTooLow` class in a little bit as well. The type `TemperatureTooLow` class inherits from `WeatherForecastError` (I'd like to ditch the inheritance too, but that's another post). The return statement on line 4 works because of the implicit cast operator defined in line 9 of the `Either` class. The return statement for the `WeatherForecast` works because of the other implicit cast operator.

Notice the function now returns an `Either< , >`. This is where the `Match` method is used. Two functions (lambdas) are provided, and if the `Either` is a left type (an error condition) the left function is called. If the `Either` is a right type (a `WeatherForecast`) the right function is called. We can switch on the type of error, if we needed, to explicitly map each kind to a specific `ActionResult` type.

```csharp {linenos=true}
[HttpGet("{tempC}")]
public ActionResult<WeatherForecast> GetWeatherForecastByTemperature(int tempC)
{
  return CalculateWeatherForecastForTemperature(tempC)
    .Match<ActionResult<WeatherForecast>>(
      error => error switch
      {
        TemperatureTooHigh tooHigh => BadRequest(tooHigh),
        TemperatureTooLow tooLow => BadRequest(tooLow),
        _ => Problem($"Unknown error trying to caculate a weather forecast for {tempC}C")
      },
      forecast => forecast
    );
}
```

This is a bit of a contrived example. What I like about the pattern is that the controller has one responsibility. Map the "business" result of weather forecast calculations to an HTTP response. Of course, `ActionResult` is helping with that as well, but it's pretty explicit and gives us plenty of Options (that might be the next post).

Here's the code for this little example: https://github.com/jeff-kwak/railway-web-api-csharp