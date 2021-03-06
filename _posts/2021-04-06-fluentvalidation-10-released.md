---
layout: post
title: "FluentValidation 10.0 Released"
---

FluentValidation 10.0 is [now available](https://nuget.org/packages/fluentvalidation). This is a major release that has been under development for the last year and contains various performance improvements.

Please ensure you read the [upgrade guide](https://docs.fluentvalidation.net/en/latest/upgrading-to-10.html) before upgrading from FluentValidation 9.x to 10.0.

The goal of this release was to improve performance and memory usage, as well as improve some longstanding issues with the library's internal API. To accomodate these changes and improvements, we had to make some breaking changes in a few areas. Users of the public API (the fluent interface) shouldn't be affected, but if you make use of our Internal API, or you write custom validators then you'll need to update your code (see the [upgrade guide](https://docs.fluentvalidation.net/en/latest/upgrading-to-10.html)).

Full release notes an be [found on the releases page](https://github.com/FluentValidation/FluentValidation/releases).

From a performance standpoint, memory usage has almost halved and validation speed is over 1.5x faster for a large validation run. The following benchmarks were run on a Lenovo Thinkpad t480s laptop running .NET Core 3.1 on Windows 10 Pro with an Intel Core i7-8550U and 16gb RAM:

- FluentValidation 9.2.0:  748.24 MB, 1491.44 ms
- FluentValidation 10.0.0: 440.21 MB, 535.84 ms

Additional benchmark code can be found [in the repository](https://github.com/FluentValidation/FluentValidation/blob/main/src/FluentValidation.Tests.Benchmarks/ValidationBenchmark.cs).
