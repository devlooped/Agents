# AGENTS.md

## Project Overview

This repo contains `Devlooped.Agents.AI`, a library of extensions for `Microsoft.Agents.AI`.

## Key Projects

| Project | Description |
|---------|-------------|
| `src/Agents` | Main library (`Devlooped.Agents.AI`) |
| `src/CodeAnalysis` | Roslyn source generators for the library |
| `src/Tests` | Unit and integration tests |

## Source Generators (`src/CodeAnalysis`)

### NameOf Generator

Generates a `partial class NameOf` with a nested static class per type, containing a `const string` for each public property name (including inherited and static properties).

**Usage** — add to any project that references `CodeAnalysis` as an analyzer:

```xml
<!-- Import MSBuild integration (required for project references; automatic for NuGet) -->
<Import Project="..\CodeAnalysis\build\Devlooped.Agents.CodeAnalysis.props" />

<ItemGroup>
  <NameOf Include="My.Namespace.SomeType" />
</ItemGroup>

<Import Project="..\CodeAnalysis\build\Devlooped.Agents.CodeAnalysis.targets" />
```

**Generated output** (namespace matches the consuming project's `RootNamespace`):

```csharp
namespace Devlooped.Agents.AI;

partial class NameOf
{
    public static readonly HashSet<string> SomeType =
    [
        nameof(My.Namespace.SomeType.MyProp),
        nameof(My.Namespace.SomeType.OtherProp),
    ];
}
```

- For generic types, string literals are used instead of `nameof()` expressions.
- Emits diagnostic `NAMEOF001` (warning) when a specified type cannot be resolved.

**Current usages in `Agents.csproj`:**
- `Microsoft.Agents.AI.ChatClientAgentOptions`
