---
layout: post
title: "FluentValidation 9.2 and 9.3-preview1 released"
---

FluentValidation 9.1 is [now available](https://nuget.org/packages/fluentvalidation). This release contains new features and performance improvements.


## Inheritance Validator
FluentValidation 9.2 contains the new Inheritance Validator, which allows you to set up specific child validators for different subclasses/implementors depending on their runtime type. 

For example, imaging the following example:

```
// We have an interface that represents a 'contact',
// for example in a CRM system. All contacts must have a name and email.
public interface IContact {
  string Name { get; set; }
  string Email { get; set; }
}

// A Person is a type of contact, with a name and a DOB.
public class Person : IContact {
  public string Name { get; set; }
  public string Email { get; set; }

  public DateTime DateOfBirth { get; set; }
}

// An organisation is another type of contact,
// with a name and the address of their HQ.
public class Organisation : IContact {
  public string Name { get; set; }
  public string Email { get; set; }

  public Address Headquarters { get; set; }
}

// Our model class that we'll be validating.
// This might be a request to send a message to a contact.
public class ContactRequest {
  public IContact Contact { get; set; }

  public string MessageToSend { get; set; }
}
```

Next we create validators for Person and Organisation:

```csharp
public class PersonValidator : AbstractValidator<Person> {
  public PersonValidator() {
    RuleFor(x => x.Name).NotNull();
    RuleFor(x => x.Email).NotNull();
    RuleFor(x => x.DateOfBirth).GreaterThan(DateTime.MinValue);
  }
}

public class OrganisationValidator : AbstractValidator<Organisation> {
  public OrganisationValidator() {
    RuleFor(x => x.Name).NotNull();
    RuleFor(x => x.Email).NotNull();
    RuleFor(x => x.HeadQuarters).SetValidator(new AddressValidator());
  }
}
```

Now we create a validator for our `ContactRequest`. We can define specific validators for the `Contact` property, depending on its runtime type. This is done by calling `SetInheritanceValidator`, passing in a function that can be used to define specific child validators:

```csharp
public class ContactRequestValidator : AbstractValidator<ContactRequest> {
  public ContactRequestValidator() {

    RuleFor(x => x.Contact).SetInheritanceValidator(v => {
      v.Add<Organisation>(new OrganisationValidator());
      v.Add<Person>(new PersonValidator());
    });

  }
}
```



## Performance Improvements

FluentValidation 9.2 also contains several performance improvements which should make a difference if you're invoking `Validate` on a large number of objects. 

## FluentValidation 9.3-Preview1 and .NET 5 support

FluentValidation 9.3-preview1 has also been released at the same time, which adds support for .NET 5 Preview 8.


