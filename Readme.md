# Super simple Git versioning
This is a build utility package that adjusts your `csproj` / `vbproj` project's NuGet and `AssemblyInformalVersion` based on the current git repository's branch name and commit count by making use of the VS 2017 SDK-based project system.

Use it like this in your project:
```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard1.6</TargetFramework>
        <VersionPrefix>1.2.3</VersionPrefix>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="DasMulli.SimpleGitVersion" Version="1.0.0-*" PrivateAssets="All" />
    </ItemGroup>
</Project>
```

If you are on the `master` branch and have 23 commits, the resulting version will be:
```
1.2.3-master-0000023
```

If you are on the `feature/awesome` branch and have 25 commits, the resulting version will be:
```
1.2.3-feature-awesome-0000025
```

## Define Release labels for known branches

For known branches, you can define custom release lables (think of it as branch name to label dictionary):

```xml
<ItemGroup>
  <GitBranchToReleaseLabelMapping Include="master" ReleaseLabel="rc" />
  <GitBranchToReleaseLabelMapping Include="develop" ReleaseLabel="beta" />
</ItemGroup>
```

If you are on the `master` branch and have 23 commits, the resulting version will be:
```
1.2.3-rc-0000023
```

If you are on the `feature/awesome` branch and have 25 commits, the resulting version will be:
```
1.2.3-feature-awesome-0000025
```

## SemVer 2.0.0

For SemVer 2.0.0, you'll want your branch name and commit count be separate prerelease lables, separated by a single dot (`.`) and no zero-padding for the commit count. This can be enabled by setting the property `UseCompatGitVersion` to `false`.
Because older NuGet clients and feeds(!!) might not fully support SemVer 2.0.0, the "compatible" SemVer 1.0.0 version is used by default. Note that the support for pre-set `VersionSuffix` values is limited (there will be a dash).

But if you like to live dangerously, you can easily enable SemVer 2.0.0 versions using:
```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard1.6</TargetFramework>
        <VersionPrefix>1.2.3</VersionPrefix>
        <UseCompatGitVersion>false</UseCompatGitVersion>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="DasMulli.SimpleGitVersion" Version="1.0.0-*" PrivateAssets="All" />
    </ItemGroup>
</Project>
```

If you are on the `master` branch and have 23 commits, the resulting version will be:
```
1.2.3-master.23
```

If you are on the `feature/awesome` branch and have 25 commits, the resulting version will be:
```
1.2.3-feature-awesome.25
```

## Omit branch name in version suffix but use a custom label

The well-known `VersionPrefix` is considered while the final version is calculated, so you can define it yourself and tell `SimpleGitVersion` to not care about branch names:


```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard1.6</TargetFramework>
        <VersionPrefix>1.2.3</VersionPrefix>
        <VersionSuffix>foo</VersionSuffix>
        <IncludeBranchInGitVersion>false</IncludeBranchInGitVersion>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="DasMulli.SimpleGitVersion" Version="1.0.0-*" PrivateAssets="All" />
    </ItemGroup>
</Project>
```

If you are on ANY branch and have 23 commits, the resulting version will be:
```
1.2.3-foo-0000023
```

## Manually disable prerelease version generation

For release builds, you usually want to omit prerelease versions being generated. There is a property that can be used to disable all git version generation: `DisableGitVersionSuffix`. You can use it like this in your build scripts (not that a prerelease version will still be generated if you set `VersionSuffix` in your project file):

```sh
> dotnet pack /p:DisableGitVersionSuffix=true
```

## More control and re-using the values calculated by SimpleGitVersion

See the [msbuild target's inline documentation](DasMulli.SimpleGitVersion/build/DasMulli.SimpleGitVersion.targets) for more details.
