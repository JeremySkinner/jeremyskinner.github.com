---
layout: post
title: "FluentValidation 8.0-preview1 is now available"
---

Fluentvalidation 8.0-preview1 is now available. This is a major release with several breaking changes. 

~~The biggest change is that validators should now inherit from `ValidatorBase` rather than `AbstractValidator` so you can take advantage of the new rule caching that's available in FluentValidation 8.~~ This change has been reverted for now and removed from preview2 due to some critical issues which were found.

Please [check out the upgrade guide](https://fluentvalidation.net/upgrading-to-8). If you run into any issues, please post them [on the GitHub project site](https://github.com/JeremySkinner/FluentValidation)
