---
layout: post
title: "FluentValidation 9.1 released"
---

FluentValidation 9.1 is [now available](https://nuget.org/packages/fluentvalidation). This release contains a few new features and some bug fixes. [View the full release notes](https://github.com/FluentValidation/FluentValidation/releases/tag/9.1.0). A few highlights:

### Simplifying overloads of Validate

FluentValidation has several overloads of its `Validate` method which have caused some confusion. For example, the overload that accepted a `params` array of property names has been in place since very early releases, 
which allowed for validating only specific properties with `validator.Validate(instance, "Forename", "Surname")` 

However, this called ambiguity when we later introduced [rule sets](https://docs.fluentvalidation.net/en/latest/rulesets.html) back in version 3.0as you had to use a named parameter so as not to conflict with the proprety-name overload above:

```csharp
validator.Validate(instance, ruleSet: "MyRuleSet");
```

If you forgot the explicit named parameter, the property names overload would be invoked instead. This became more confusing when we enabled validation of multiple rulsets, as you had to specify these
with a comma-separated string:

```csharp
validator.Validate(instance, ruleSet: "MyRuleSet,SomeOtherRuleSet");
```

On top of this confusion, there are various other overloads that allow specifying a validator selector, property names as expressions, and whether the validator should throw an exception or not, on top of the "core" validate method that did all the work (which takes a raw `ValidatioCntext`). 

To try and tidy this up and avoid confusion, most of these overloads have now been deprecated and will be removed in FluentValidation 10. Instead, you can explicitly opt in to which features you want to use by passing in a set of configuration options:

```csharp
// Validate specific rulesets
validator.Validate(instance, opt => {
  opt.IncludeRuleSets("MyRuleSet", "SomeOtherRuleSet");
  
  // Can also force rules not in a ruleset to be run
  opt.IncludeRulesNotInRuleSet();
});

// Validate specific properties
validator.Validate(instance, opt => {
  opt.IncludeProperties("Forename", "Surname");
  // or
  opt.IncludeProperties(x => x.Surname, x.Forename);
});

// Throw exceptions on failure
validator.Validate(instance, opt => {
   opt.ThrowOnFailures();
});
```

These options can be combined together in the same validation run eg:

```csharp
// Validate specific rulesets
validator.Validate(instance, opt => {
  opt.IncludeRuleSets("MyRuleSet", "SomeOtherRuleSet").IncludeRulesNotInRuleSet();
  opt.ThrowOnFailures();
});
```



### CascadeMode impprovements

FluentValidation has supported the concept of a "CascadeMode" since version 1.2. The cascade mode allows a rule to stop after the first failure. For example:

```csharp
RuleFor(x => x.FirstName).Cascade(CascadeMode.StopOnFirstFailure).NotNull().NotEqual("foo");
```

In this case the `NotEqual` will not run if the `NotNull` fails. This logic has always been in place, and can also be set at the validator level:

```csharp
public MyValidator() {
  this.CascadeMode = CascadeMode.StopOnFirstFailure;
  
  // The NotEqual on this rule will not run if the NotNull fails.
  RuleFor(x => x.FirstName).NotNull().NotEqual("foo");
  
  // The NotEqual on this will will also not run if its NotNull fails, 
  // but the NotNull will always run regardless of the rules on FirstName above
  RuleFor(x => x.LastName).NotNull().NotEqual("foo");
}
```

Setting this at the validator level is the equivalent of setting it against each of the rules. However, the behaviour was often misleading for users of the library. 
Many users have assumed this means "stop immediately if *any* rule produces a failure", ie the whole validator could would only ever generate 0 or 1 validation errors. 
However this wasn't the case, this actually meant each call to `RuleFor` would only ever generate 0 or 1 failures (as `CascadeMode.StopOnFirstFailure` only ever applies at the rule-level, even when set on the validator). 
This has caused a lot of confusion over the years.

To resolve this we decided to introduce a new behaviour which is consistent with what users were expecting, and allows for a true "fail-fast" behaviour. The new option (`CascadeMode.Stop`) will act as a true fail-fast. When set at the validator level:

```csharp
public MyValidator() {
  // This whole validator class will only ever produce 1 failure at most.
  this.CascadeMode = CascadeMode.Stop;
  
  RuleFor(x => x.FirstName).NotNull().NotEqual("foo");
  RuleFor(x => x.LastName).NotNull().NotEqual("foo");
}
```

### Async test helper

The [test helper](https://docs.fluentvalidation.net/en/latest/testing.html) now has asynchronous overloads available.

The existing `StopOnFirstFailure` option will remain unchanged for backwards compatibility, but has been marked as deprecated.

[View the full release notes to see other fixes](https://github.com/FluentValidation/FluentValidation/releases/tag/9.1.0).
