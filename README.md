# ModelMatcher Assertions [![NuGet Version](https://img.shields.io/nuget/v/modelmatcher.assertions.svg?style=flat)](https://www.nuget.org/packages/ModelMatcher.Assertions/)


**ModelMatcher** makes verifying models in your unit tests easier and cleaner. 

No more Linq statements to assert that an item is in a collection, and no need for for ten lines of assert statements just to check that all of the properties on a model are correct.

## Single items ...

```csharp
var result = myApi.Call();

var expectedResult = new MyModel
{
    Property1 = "Some Value",
    Property2 = 123,
    Property3 = false
};

result.ShouldMatch(expectedResult);
```

## Collections

One matching item:

```csharp
var resultCollection = myApi.Call();

var expectedResult = new MyModel
{
    Property1 = "Some Value",
    Property2 = 123,
    Property3 = false
};

resultCollection.ShouldContainAMatch(expectedResult);
```

Or multiple matches:

```csharp
var resultCollection = myApi.Call();

var expectedResult = new MyModel
{
    Property1 = "Some Value",
    Property2 = 123,
    Property3 = false
};

resultCollection.ShouldContainMatches(expectedResult, Matches.Two);
```

## Conditional Matching

For more control over how to verify properties:

```csharp
var result = myApi.Call();

var expectedResult = new MyModel
{
    Property1 = "Some Value",
    Property2 = 123,
    Property3 = false,
    Property4 = "Another Value"
    ...
};

result.ShouldMatch(expectedResult, new[]
	{
		Ignore.This(() => expectedResult.Property2),
		Match.IgnoringCase(() => expectedResult.Property4),
		Match.IfNotNull(() => expectedResult.Customer),
	});
```

You can use this on collections too:

```csharp
var resultCollection = myApi.Call();

var expectedResult = new MyModel
{
    Property1 = "Some Value",
    Property2 = 123,
    Property3 = false,
    Property4 = "Another Value"
    ...
};

// One matching item
result.ShouldContainAMatch(expectedResult, new[]
	{
		Ignore.This(() => expectedResult.Property2),
	});

// A correct number of matching items
result.ShouldContainMatches(expectedResult, new[]
	{
		Ignore.This(() => expectedResult.Property2),
	}, 
	Matches.Three);
```

## Non-default matching

Check only the properties that are *not* set to the default value in the expected model. This saves you from specifying every property in the expected model even if it is not important.

Single items:

```csharp
var result = myApi.Call();

// We only care that Property2 matches, ignore everything else
var expectedResult = new MyModel
{
    Property2 = 123,
};

result.ShouldMatchNonDefaultProperties(expectedResult);
```

Collections:

```csharp
var resultCollection = myApi.Call();

var expectedResult = new MyModel
{
    Property1 = "Some Value"
};

// Look for one matching item
resultCollection.ShouldContainAMatchOfNonDefaultProperties(expectedResult);

// A number of matching items
resultCollection.ShouldContainMatchesOfNonDefaultProperties(expectedResult, Matches.Three);
```


Sometimes you need to use this mode, but assert that a property *is* set to the default value. 
For example, to assert that a ``bool`` is set to ``false``.

You can do this with conditional matching:

```csharp
var result = myApi.Call();

var expectedResult = new MyModel
{
    Property2 = 123,
    Property3 = false
};

result.ShouldMatchNonDefaultProperties(expectedResult, new[]
	{
		// Property3 would otherwise be ignored, as it is set to the default value
		// in the expected model
		Match.This(() => expectedResult.Property3)
	});
```

This also works with collections:

```csharp
var resultCollection = myApi.Call();

var expectedResult = new MyModel
{
    Property2 = 123,
    Property3 = false
};

resultCollection.ShouldContainAMatchOfNonDefaultProperties(expectedResult, new[]
	{
		Match.This(() => expectedResult.Property3)
	});

resultCollection.ShouldContainMatchesOfNonDefaultProperties(expectedResult, new[]
	{
		Match.This(() => expectedResult.Property3)
	},
	Matches.Two);
```
