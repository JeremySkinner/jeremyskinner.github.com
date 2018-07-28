---
title: "Thoughts on using Drupal"
layout: post
---

After 10 years working with ASP.NET and C#, I've spent the last few months working with Drupal 8 and PHP. The last time I worked with PHP was in 2005, and although the language is still full of inconsistencies, it's nice to see that it's a fully object-oriented language and supports more modern functionality like anonymous functions and traits. 

Working with Drupal 8 has been quite a learning experience. Drupal is an extremely powerful platform, but has a huge learning curve. The documentation is pretty poor compared to the very well-written [in-depth tutorials](https://www.asp.net/learn) and [API docs](https://docs.microsoft.com) that ASP.NET provides. However It's good to see that Drupal 8 has embraced a more MVC-based approach (by using Symfony controllers), dependency injection and plugins. Sadly the horrible hook-based extensibility system hasn't completely gone away yet. 

The hardest thing I've found with Drupal is following an application's flow. Diving into an existing codebase and trying to find where a piece of functionality is implemented is something I found extremely challenging. For example a button on a form could be defined directly in a `Form` class, through a YAML file, or any number of hooks in any module within the application. I suppose this is the downside of an extremely flexible extensibility model.

I've also been enjoying getting much more into Linux and server management using the Bash CLI, something I've had very little experience with (my CLI of choice is usually Powershell)

Overall C#/.NET would still be my platform of choice, but learning something new and working on some very interesting projects with a great team is much more important to me than choice of language.

