---
layout: post
title: "FluentValidation 8.1 released"
---

FluentValidation 8.1 is [now available](https://nuget.org/packages/fluentvalidation). This release contains several new features and bug fixes:

### String Formatting in Message Placeholders

FluentValidation has always supported placeholders within error messages, such as `{PropertyValue}`:

```csharp
RuleFor(x => x.Age).GreaterThan(18).WithMessage("Must be older than 18 years. You entered {PropertyValue} years.");
```

Now you can also specify standard .NET formatting strings as part of the placeholder, such as `{PropertyValue:d}` or `{PropertyValue:p1}` etc. 

### Overriding Indexers for Collection Rules

Collection rules built using `RuleForEach` now allow you to override the indexer. For example, imagine you defined a collection rule against an collection of Address lines:

```csharp
RuleForEach(x => x.AddressLines).NotNull();
```
...Then the validator would generate validation failures for properties `person.AddressesLines[0]` and `person.AddressLines[1]` etc. The indexer would always be the numeric index within the collection surrounded by square brackets. This can now be overridden to use something different, or remove the indexer completely. Eg:

```csharp
v.RuleForEach(x => x.AddressLines)
  .OverrideIndexer((x, collection, element, index) => "<" + index + ">")
  .NotNull()
```

This would use angle brackets rather than square brackets in the generated indexer.

### Error code can now be used to select a different default error message. 

You can now use error codes to point to a particular error message stored in the default language manager. For example, the default 'NotNull' validation message has a code of `NotNullValidator` and a message of `'{PropertyName}' must not be empty.` If you want to use this message from inside a different validator (eg for a custom validator), you would have to hard-code the message again:

```csharp
RuleFor(x => x.Name).NotNull(); //defaults to '{PropertyName}' must not be empty.
// `Must` has its own default error message, so if you want to use the NotNull message you'd have to specify it explicitly
RuleFor(x => x.Name).Must(name => name != null).WithMessage("'{PropertyName}' must not be empty.");
```

However now you can select a message based on error code:

```
RuleFor(x +> x.Name).Must(name => name != null).WithErrorCode("NotNullValidator"); 
```

### Test Helper improvements

Additional details are now shown in the output for `ShouldNotHaveValidationErrorFor`. 

### Conditional validation improvements

If you use the top-level `When` or `Unless` methods to wrap multiple rules, the condition check is now cached and only executed once.

```csharp
// The IsStudnet check will now only be executed once.
When(x => x.IsStudent, () => {
   RuleFor(x => x.StudentNumber).NotNull();
   RuleFor(x => x.Courses).NotNull();
});
```

There's also a new `Otherwise` method that can now be chanined onto a `When` or `Unless` to do its opposite, without having to set up a second `When` call with the opposite condition:

```csharp
When(x => x.IsStudent, () => {
   RuleFor(x => x.StudentNumber).NotNull();
   RuleFor(x => x.Courses).NotNull();
}).Otherwise(() => {
   RuleFor(x => x.StudentNumber).Null();
});
```

### Other minor changes
- MVC 5's `CustomizeValidatorAttribute` now has the `Skip` property, like WebApi and AspNetCore, allowing auto-validation to be skipped for specific action parameters.
- Japanese language translation
- The overload of `OverridePropertyName` that takes an expression can now use an expression with any return value, not just strings.
- Minor wording changes to the default English error messages.
