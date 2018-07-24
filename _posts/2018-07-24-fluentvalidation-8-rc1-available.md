---
layout: post
title: "FluentValidation 8.0-rc1 is now available"
---

FluentValidation 8.0-rc1 is now available. This release contains several breaking changes and feature enhancements:

### Validating properties by path

You can now validate specific properties using a full path, eg: 

```csharp
validator.Validate(customer, "Address.Line1", "Address.Line2");
``

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


Additonally lots of methods deprecated in previous versions have now been removed. Please [check out the upgrade guide for further details](https://fluentvalidation.net/upgrading-to-8). If you run into any issues, please post them [on the GitHub project site](https://github.com/JeremySkinner/FluentValidation)
