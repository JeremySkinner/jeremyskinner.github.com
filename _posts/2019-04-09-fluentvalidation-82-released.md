---
layout: post
title: "FluentValidation 8.2 released"
---

FluentValidation 8.2 is [now available](https://nuget.org/packages/fluentvalidation). This release contains several new features and bug fixes:

### ASP.NET Core DI Extensions for Child Validators
It's always been possible to inject child validators when working with a Dependency Injection container such as ASP.NET Core's Service Provider. Typically, you'd do this by injecting the child validator into the parent's constructor and then calling `SetValidator`:

```csharp
public class PersonValidator : AbstractValidator<Person> {
  public PersonValidator(AddressValidator addressValidator) {
    RuleFor(x => x.Address).SetValidator(addressValidator);
  }
}
```

With FluentValidation 8.2, you can optionally use the `InjectValidator` method instead:

```csharp
public class PersonValidator : AbstractValidator<Person> {
  public PersonValidator() {
    RuleFor(x => x.Address).InjectValidator();
  }
}
```

In this case, FluentValidation will attempt to resolve an instance of `IValidator<T>` from ASP.NET's service collection, where `T` is the same type as the property being validated (so in this case it'll look for an implementation of `IValidator<Address>` registered with the container).
If you need to explicitly specify the type, then this can be done with the other overload of `InjectValidator` which accepts a func referencing the service provider:

```csharp
public class PersonValidator : AbstractValidator<Person> {
  public PersonValidator() {
    RuleFor(x => x.Address).InjectValidator((services, context) => services.GetService<SomeOtherAddressValidator>());
  }
}
```

Note that these methods are only available when using the automatic validation that's part of [FluentValidation's ASP.NET Core integration](https://fluentvalidation.net/aspnet). These methods will not be available to use if you're invoking validators manually, or using FluentValidation outside of ASP.NET Core.

###  ASP.NET Core DI Extensions for registering validators

There are several new methods for registering validators with the ASP.NET service provider. These are extension methods on `IServiceCollection`:

```csharp
AddValidatorsFromAssemblies(this IServiceCollection services, IEnumerable<Assembly> assemblies, ServiceLifetime lifetime = ServiceLifetime.Transient)AddValidatorsFromAssembly
AddValidatorsFromAssembly(this IServiceCollection services, Assembly assembly, ServiceLifetime lifetime = ServiceLifetime.Transient)
AddValidatorsFromAssemblyContaining(this IServiceCollection services, Type type, ServiceLifetime lifetime = ServiceLifetime.Transient)
AddValidatorsFromAssemblyContaining<T>(this IServiceCollection services, ServiceLifetime lifetime = ServiceLifetime.Transient)
```

Typically you would not need to use these directly as the standard way of registering validators with the container would be to call the `Register...` methods as part of `AddFluentValidation` in application startup:

```csharp
services.AddMvc().AddFluentValidation(fv => {
	fv.RegisterValidatorsFromAssemblyContaining<MyValidator>();
});
```

Internally, the methods part of `AddFluentValidation` now make use of the new service collection extensions. 

### RuleFor.ForEach as an alternative to RuleForEach.

FluentValidation has supported `RuleForEach` for several years, which allows a rule to be applied to all (or some) items in a collection. As an alternative to using `RuleForEach`, it's now possible to call `ForEach` as part of a regular `RuleFor`. 
With this approach you can combine rules that act upon the entire collection with rules which act upon individual elements within the collection. For example:

```csharp
RuleFor(x => x.Orders)
  .Must(x => x.Orders.Count <= 10).WithMessage("No more than 10 orders are allowed");

RuleForEach(x => x.Orders)
  .Must(order => order.Total > 0).WithMessage("Orders must have a total of more than 0");
```

The above 2 rules could be re-written as:

```csharp
RuleFor(x => x.Orders)
  .Must(x => x.Orders.Count <= 10).WithMessage("No more than 10 orders are allowed")
  .ForEach(orderRule => {
    orderRule.Must(order => order.Total > 0).WithMessage("Orders must have a total of more than 0");
  });
 ```
I personally think that using 2 rules is clearer and easier to read, but the option of combining them is now available with the `ForEach` method.

### Default interceptor implementation
It's now possible to register a default implementation of `IValidatorInterceptor` with the service provider which will be used for all calls to FluentValidation by the automatic ASP.NET integration. [See the documentation for more details on interceptors](https://fluentvalidation.net/aspnet#validator-interceptors).

Note that this is only available with ASP.NET Core, not with MVC5 or WebApi2. 

### WithLocalizedMessage is deprecated
The overload of `WithMessage` that takes a callback/func should be used instead.

### MVC5 and WebApi2 integration is deprecated

Support for MVC5 and WebApi2 is now considered legacy. Going forward, only the ASP.NET Core integration will receive updates. Note that the current MVC5 and WebApi2 packages will continue to work just fine as part of FluentValidation 8.x but won't receive any further updates and won't ship as part of 9.0.

