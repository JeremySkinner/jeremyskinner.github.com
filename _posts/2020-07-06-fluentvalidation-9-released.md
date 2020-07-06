---
layout: post
title: "FluentValidation 9 released"
---

FluentValidation 9 is [now available](https://nuget.org/packages/fluentvalidation). This release contains several new features and bug fixes, as well as some breaking changes. Please read the [upgrade guide](https://docs.fluentvalidation.net/en/latest/upgrading-to-9.html) and the [changelog](https://github.com/FluentValidation/FluentValidation/releases/tag/9.0.0) for full details, but here are a few things to be aware of:

### New TestHelper features

FluentValidation's unit-testing functionality has been improved with several new [advanced test extensions](https://docs.fluentvalidation.net/en/latest/testing.html#advanced-test-extensions). Additional improvements here for use with asynchronous validators are planned for 9.1. 

### Improvements to ASP.NET Core setup

When auto-registering validators in ASP.NET Core applications, you can now optionally filter out types you don't want to be registered:

```csharp
services.AddMvc().AddFluentValidation(fv => {
  fv.RegisterValidatorsFromAssemblyContaining<MyValidator>(scanResult => {
    return scanResult.ValidatorType != typeof(SomeValidatorToExclude);
  });
});
	
```

### NotEqual/Equal now perform ordinal comparisons

When using the `Equal` or `NotEqual` validators against a string property, these will now perform ordinal comparisons (rather than culture-specific comparisons). This fixes a regression that was introduced way back in FluentValidation 4. If you need to perform a culture-specific comparison, you must now explicitly specify this. [See the API docs for details](https://docs.fluentvalidation.net/en/latest/built-in-validators.html#equal-validator) 

### Default Email Validation Mode

This is a subtle behaviour change. Prior to version 9.0 FluentValidation used a regular expression for email validation. This has been changed to a simple check for the presence of [something]@[something]. Although this is a more naive check, this brings FluentValidation's email validation in-line with the .NET Core `EmailAddressAttribute`, and also follows the recommendation that regular expressions shouldn't be used for email addresses. If you need to preserve the previous behaviour, you can supply an optional parameter to use the old behaviour: `RuleFor(customer => customer.Email).EmailAddress(EmailValidationMode.Net4xRegex);` 

[See here for more details on this change](https://docs.fluentvalidation.net/en/latest/upgrading-to-9.html#default-email-validation-mode-changed) and the [API documentation here](https://docs.fluentvalidation.net/en/latest/built-in-validators.html?highlight=email#email-validator)

### Updates to the ScalePrecision validator

The algorithm used by the ScalePrecision validator has been [updated to match the behaviour of SQL Server (and other RDBMS solutions)](https://docs.fluentvalidation.net/en/latest/upgrading-to-9.html#changes-to-the-scaleprecisionvalidator)

### Removal of non-generic Validate overload and non-generic ValidationContext class

The `IValidator.Validate(object model)` overload has been removed to improve type safety. If you were using this method before, you should change your code to use the overload that accepts an `IValidationContext` instead:

```csharp
var context = new ValidationContext<object>(model);
var result = validator.Validate(context);
```

### Supported frameworks

Several old versions of the .NET Framework and .NET Core are no longer supported. The supported platforms list for the core FluentValidation library is now:
- .NET Core 2.0 or newer
- .NET Framework 4.6.1 or newer

For the ASP.NET integration, the supported platforms are:
- .NET Core 3.1
- .NET Core 2.1 

If you need to run FluentValidation on older versions of .NET Framework (<4.6.1) or obsolete versions of .NET Core (2.0, 2.2, 3.0) then you'll need to stick with FluentValidation 8.6.  

### Several changes to the internal API

This will not affect normal users of the library, but if you have built any custom advanced extensions then you should [See the upgrade guide to check if they affect you](https://docs.fluentvalidation.net/en/latest/upgrading-to-9.html) 

### Removal of several deprecated features

Several features that were deprecated in FluentValidation 8.x have been removed in 9.0. [See the upgrade guide for a complete list](https://docs.fluentvalidation.net/en/latest/upgrading-to-9.html) 
