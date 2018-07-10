---
layout: post
title: "FluentValidation 8.0-preview1 is now available"
---

Fluentvalidation 8.0-preview1 is now available. This is a major release with several breaking changes. 
The biggest change is that validators should now inherit from `ValidatorBase` rather than `AbstractValidator` so you can take advantage of the new rule caching that's available in FluentValidation 8.

FluentValidation 7:
```csharp
public class PersonValidator : AbstractValidator<Person> {
  public PersonValidator() {
    RuleFor(x => x.Surname).NotNull().WithMesage("Please enter your surname");
  }
}
```

Equivalent in FluentValidation 8:

```csharp
public class PersonValidator : ValidatorBase<Person> {
  protected override void Rules() {
    RuleFor(x => x.Surname).NotNull().WithMesage("Please enter your surname");
  }
}
```

There are several other big changes in this release, so please [check out the upgrade guide](https://fluentvalidation.net/upgrading-to-8). If you run into any issues, please post them [on the GitHub project site](https://github.com/JeremySkinner/FluentValidation)