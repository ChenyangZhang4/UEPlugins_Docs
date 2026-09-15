# Gameplay Tag Select

**Gameplay Tag Select** is an Unreal Engine Blueprint editor plugin that adds wildcard Select nodes driven by Gameplay Tags.

It provides two pure Blueprint nodes:

- **Select by Gameplay Tag**
- **Select by Gameplay Tag Container**

The nodes behave similarly to Unreal Engine's native **Select** node, while using Gameplay Tag matching to choose the returned value.

**Supported Unreal Engine version:** UE 5.8

## Features

### Select by Gameplay Tag

Selects a value based on a single `FGameplayTag`.

Configure the cases through **Match Tags** in the node's Details panel. Each valid, unique Gameplay Tag becomes an option pin on the node.

The order of **Match Tags** defines priority:

> The first matching Gameplay Tag in the array wins.

Example:

```text
Match Tags
0: Character.State.Combat
1: Character.State
2: Character
```

If hierarchical matching is enabled and the input Tag matches more than one case, the first matching entry is selected.

### Select by Gameplay Tag Container

Selects a value based on an `FGameplayTagContainer`.

Each entry in **Match Tags** becomes an option pin. The plugin checks the container against the cases in array order and returns the value associated with the first matching Tag.

This is useful when a container may satisfy multiple cases but you want an explicit priority.

### Exact Match

Both nodes expose **Exact Match** as a normal Boolean input pin.

- `true` — only identical Gameplay Tags match.
- `false` — Unreal Engine's hierarchical Gameplay Tag matching is used.

Because **Exact Match** is a pin rather than a fixed node setting, it can be controlled dynamically from Blueprint logic.

### Wildcard Value Pins

The case inputs, **Default**, and **Return Value** share one wildcard type, similar to Unreal Engine's native Select node.

Connecting any value pin resolves the wildcard type and propagates that type to the other value pins.

The nodes can therefore select values such as:

- Boolean
- Integer / Float
- Name / String / Text
- Object references
- Class references
- Structs
- Enums
- Other Blueprint-compatible value types

### Default Value

Every node includes a **Default** input.

If none of the configured Gameplay Tag cases match, the Default value is returned.

If no Match Tags are configured, the node effectively passes through the Default value once its wildcard type is resolved.

### Match Tags

Select a Gameplay Tag Select node and edit:

```text
Details
  -> Gameplay Tag Select
     -> Match Tags
```

Each valid unique Tag produces one option pin.

Invalid entries and duplicate Tags are ignored.

Changes to the Match Tags array automatically rebuild the option pins.

If the node ever contains stale pins after external changes, right-click the node and choose:

```text
Refresh Option Pins
```

### Native Select Integration

The plugin is designed to fit naturally into the Blueprint editor.

Both Gameplay Tag Select nodes:

- Use the same icon and tint as Unreal Engine's native **Select** node.
- Use the same Blueprint Action Menu category as the native Select node.
- Appear alongside the native Select node when searching for `select`.
- Are pure Blueprint nodes and do not add execution pins.

## Node Reference

### Select by Gameplay Tag

Inputs:

```text
Gameplay Tag
Exact Match
Default
<Tag Case 1>
<Tag Case 2>
...
```

Output:

```text
Return Value
```

Matching uses Unreal Engine's Gameplay Tag `Matches Tag` behavior.

### Select by Gameplay Tag Container

Inputs:

```text
Gameplay Tag Container
Exact Match
Default
<Tag Case 1>
<Tag Case 2>
...
```

Output:

```text
Return Value
```

Matching uses Unreal Engine's Gameplay Tag Container `Has Tag` behavior.

## Usage

### Create a Gameplay Tag Select Node

In a Blueprint graph:

1. Right-click to open the Blueprint Action Menu.
2. Search for `select`.
3. Add either:
   - **Select by Gameplay Tag**
   - **Select by Gameplay Tag Container**

### Configure Cases

1. Select the node.
2. Open its Details panel.
3. Add Gameplay Tags to **Match Tags**.
4. Arrange the array in the desired matching priority.
5. Connect values to the generated option pins.
6. Connect a fallback value to **Default**.
7. Set or connect **Exact Match** as required.

Example:

```text
Match Tags
0: Character.State.Dead
1: Character.State.Combat
2: Character.State

Character.State.Dead   -> Dead UI
Character.State.Combat -> Combat UI
Character.State        -> Generic State UI
Default                -> Normal UI
```

With hierarchical matching, array order matters whenever more than one case can match.

## Compilation

The plugin contains an **UncookedOnly** Blueprint-node module.

The custom nodes do not require custom runtime functions. During Blueprint compilation, they expand into Unreal Engine's built-in:

- Gameplay Tag comparison function calls
- Native Select nodes

As a result, the custom editor node implementation is not required in packaged game runtime code.

The compiler also reports errors for invalid node state such as:

- Unresolved wildcard value type
- Invalid or stale option pins
- Internal expansion failures

## Installation

1. Copy the `GameplayTagSelect` folder into your project's Plugins directory:

```text
YourProject/
  Plugins/
    GameplayTagSelect/
```

2. Regenerate project files if required.
3. Build the Editor target.
4. Start Unreal Editor and enable **Gameplay Tag Select** if it is not already enabled.
5. Restart the editor if requested.

## Design Scope

Gameplay Tag Select is intentionally focused on one task:

> Use Gameplay Tag matching as the condition for a native-style Blueprint Select node.

It does not modify Gameplay Tags themselves and does not add runtime Gameplay Tag systems.
