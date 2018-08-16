---
layout: post
title: "FluentValidation 8.0 released"
---

FluentValidation is out and [available to download from Nuget](http://nuget.org/packages/fluentvalidation). This is a major release with several breaking change, so please make sure you read the [upgrade notes](https://fluentvalidation.net/upgrading-to-8.html) before upgrading. 

### Validating properties by path

You can now validate specific properties using a full path, eg: 

```csharp
validator.Validate(customer, "Address.Line1", "Address.Line2");
```

### Validating a specific ruleset with SetValidator

Previously, if you defined a child validator with `SetValidator`, then whichever ruleset you invoked on the parent validator will cascade to the child validator.
Now you can explicitly define which ruleset will run on the child:

```csharp
RuleFor(x => x.Address).SetValidator(new AddressValidator(), "myRuleset");
```

### AttrbiutedValidatorFactory has been moved to a separate package

The `ValidatorAttribute` and the `AttributedValidatorFactory` were typically used in MVC/WebApi projects to wire models to their validators. This is no longer recommended when usign AspNetCore as the built-in Service Provider is a better alternative.
These classes can still be used by explicitly installing the `FluentValidation.ValidatorAttribute` package. Note this package is installed by default if you are using the legacy MVC5/WebApi integration rather than AspNetCore.

### SetCollectionValidator is deprecated

`RuleForEach` provides a more flexible syntax for the same result.

### Async changes

Internally, the asynchronous validation API has been cleaned up thanks to `await\async`. From a consumer's point of view, the asynchronous methods should all continue to work as before with the exception of some methods that previously didn't take a `CancellationToken` that now do.

The full changelog is [available here](https://github.com/JeremySkinner/FluentValidation/blob/master/Changelog.txt)
