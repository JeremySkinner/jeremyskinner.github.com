---
layout: post
title: "Updating .NET Core inside AppVeyor"
---

I currently use [appveyor](https://appveyor.com) to run Continuous Integration builds for [FluentValidation](https://fluentvalidation.net). It's really easy to get started with appveyor as the build images come pre-installed with most tools you'd want to use as part of a build. The downside is it's not easy to specify which version of a particular tool you want to use, and appveyor often lags behind with new releases. 

For example, for FluentValidation I want the master branch to be built against the current LTS version of the .NET Core SDK (2.1.400), and I want my vNext branch to build against 2.2-preview1. But appveyor only has version 2.1.300 installed, so both branches need a new version of the .NET SDK.

Based on [this post](https://andrewlock.net/building-asp-net-core-2-0-preview-2-packages-on-appveyor/) by Andrew Lock, I updated the FluentValidation build script to download an install a newer .NET Core SDK if needed. The build script uses powershell with the [posh-build](https://github.com/jeremyskinner/posh-build) helpers for defining targets:

```powershell
target install-dotnet-core {
  # Ensures that .net core is up to date.
  # first get the required version from global.json
  $json = ConvertFrom-Json (Get-Content "$path/global.json" -Raw)
  $required_version = $json.sdk.version

  # Running dotnet --version stupidly fails if the required SDK version is higher 
  # than the currently installed version. So move global.json out the way 
  # and then put it back again 
  Rename-Item "$path/global.json" "$path/global.json.bak"
  $current_version = (dotnet --version)
  Rename-Item "$path/global.json.bak" "$path/global.json"
  Write-Host "Required .NET version: $required_version Installed: $current_version"

  if ($current_version -lt $required_version) {
    # Current installed version is too low.
    # Install new version as a local only dependency. 
    $urlCurrent = "https://dotnetcli.blob.core.windows.net/dotnet/Sdk/$required_version/dotnet-sdk-$required_version-win-x64.zip"
    Write-Host "Installing .NET Core $required_version from $urlCurrent"
    $env:DOTNET_INSTALL_DIR = "$path/.dotnetsdk"
    New-Item -Type Directory $env:DOTNET_INSTALL_DIR -Force | Out-Null
    (New-Object System.Net.WebClient).DownloadFile($urlCurrent, "dotnet.zip")
    Write-Host "Unzipping to $env:DOTNET_INSTALL_DIR"
    Add-Type -AssemblyName System.IO.Compression.FileSystem; [System.IO.Compression.ZipFile]::ExtractToDirectory("dotnet.zip", $env:DOTNET_INSTALL_DIR)
  }
}
```

Here I do the following:
- Check which version of .net core is needed based on what's defined in my global.json
- Check the current .net core installed version (working around that `dotnet --version` fails if there's a mismatch between required and expected version)
- Download the new SDK and unzip it to a local directory
- Update the environment PATH variable so that the new version is used whenever `dotnet` is called. 

This script can then be called as part of the `install` section of your appveyor.yml.

The full example is available in the [FluentValidation repository](https://github.com/JeremySkinner/FluentValidation/blob/668e462a8056f4c334f144644aa31d20c9d7c9a9/build.ps1#L117-L155).
