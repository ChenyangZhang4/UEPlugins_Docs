# Gameplay Tag Select & Switch

**Gameplay Tag Select & Switch** is an Unreal Engine Blueprint editor plugin that adds flexible **Select** and **Switch** nodes driven by Gameplay Tags and Gameplay Tag Containers.

The plugin is designed to feel consistent with Unreal Engine's native Blueprint nodes while providing more flexible Gameplay Tag matching.

**Supported Unreal Engine version:** UE 5.8

## Features

The plugin provides four Blueprint nodes:

- **Select (Gameplay Tag)**
- **Select (Gameplay Tag Container)**
- **Switch (Gameplay Tag)**
- **Switch (Gameplay Tag Container)**

The type shown in parentheses represents the **input type** of the node.

Each node can independently use either:

- `Gameplay Tag` cases
- `Gameplay Tag Container` cases

This allows all four matching combinations:

```text
Gameplay Tag       -> Gameplay Tag
Gameplay Tag       -> Gameplay Tag Container
Gameplay TagContainer -> Gameplay Tag
Gameplay TagContainer -> Gameplay Tag Container
```

## Select Nodes

### Select (Gameplay Tag)

Selects and returns a value based on a single `FGameplayTag`.

### Select (Gameplay Tag Container)

Selects and returns a value based on an `FGameplayTagContainer`.

Both Select nodes use wildcard value pins similarly to Unreal Engine's native **Select** node.

Connecting a supported value type resolves the wildcard type and propagates it to:

```text
Case inputs
Default
Return Value
```

Supported value types include standard Blueprint-compatible types such as:

- Boolean
- Integer
- Float
- Name
- String
- Text
- Enums
- Structs
- Object references
- Class references
- Other Blueprint-compatible types

The first matching case is selected.

If no case matches, the **Default** value is returned.

## Switch Nodes

### Switch (Gameplay Tag)

Routes execution based on a single `FGameplayTag`.

### Switch (Gameplay Tag Container)

Routes execution based on an `FGameplayTagContainer`.

Each configured case creates an execution output.

If none of the configured cases match, execution continues through the **Default** output.

The first matching case wins.

## Case Type

Every node provides a **Case Type** option in the Details panel.

Available types:

```text
Gameplay Tag
Gameplay Tag Container
```

The node input type and Case Type are independent.

For example:

```text
Input Type:
Gameplay Tag Container

Case Type:
Gameplay Tag
```

checks whether the input container contains a tag matching each configured case.

Changing **Case Type** clears the existing cases because Gameplay Tags and Gameplay Tag Containers are structurally different case types.

## Exact Match

All four nodes expose **Exact Match** directly as a Boolean input pin.

```text
Exact Match = true
```

Only exact Gameplay Tag matches are accepted.

```text
Exact Match = false
```

Gameplay Tag hierarchy matching is enabled.

For example:

```text
Input:
State.Combat.Melee

Case:
State.Combat
```

With hierarchical matching enabled, the case can match the input.

The matching direction follows Unreal Engine's Gameplay Tag matching behavior.

## Gameplay Tag Container Cases

When **Case Type** is set to `Gameplay Tag Container`, each case may contain multiple Gameplay Tags.

Container cases are displayed directly on the Blueprint node in a compact format:

```text
(3) Character.State.Combat
```

Where:

```text
(3)
```

is the total number of Gameplay Tags in the case, and:

```text
Character.State.Combat
```

is the complete first Gameplay Tag in that container.

An empty container is displayed as:

```text
(0) None
```

Hovering over a Container Case displays the complete container in its tooltip.

Example:

```text
Gameplay Tag Container (3)

• Character.State.Combat
• Character.State.Burning
• Character.State.Attacking
```

This keeps the node compact while preserving full case information when needed.

## Match All

**Match All** is available when both the node input and Case Type are Gameplay Tag Containers:

```text
Gameplay Tag Container
        ->
Gameplay Tag Container
```

It is exposed directly as a Boolean input pin.

### Match All Disabled

```text
Match All = false
```

The case matches when **any** required Gameplay Tag matches the input container.

### Match All Enabled

```text
Match All = true
```

The case matches only when **all** required Gameplay Tags match the input container.

For example:

```text
Input Container:
State.Combat
State.Burning
State.Poisoned

Case Container:
State.Combat
State.Burning
```

With:

```text
Match All = true
```

the case matches because both required tags are present.

## Matching Behavior

The four input/case combinations behave as follows.

### Gameplay Tag -> Gameplay Tag

The input Gameplay Tag is compared against the case Gameplay Tag.

```text
Input Tag matches Case Tag
```

### Gameplay Tag -> Gameplay Tag Container

The input Gameplay Tag is compared against the Gameplay Tags contained in the case.

The case matches when the input matches at least one tag in the Case Container.

```text
Input Tag matches ANY Case Tag
```

### Gameplay Tag Container -> Gameplay Tag

The Gameplay Tags in the input container are checked against the case Gameplay Tag.

The case matches when at least one input tag matches the case.

```text
ANY Input Tag matches Case Tag
```

### Gameplay Tag Container -> Gameplay Tag Container

The input container is matched against the Case Container.

Behavior is controlled by **Match All**:

```text
Match All = false
    ANY Case Tag must match

Match All = true
    ALL Case Tags must match
```

**Exact Match** controls whether hierarchical Gameplay Tag matching is allowed in all of these combinations.

## Literal Select Inputs

Select nodes support an additional **Literal** option when the resolved output type is a reference type for which literal/value case inputs are supported.

The option appears in the node's Details panel only when relevant.

### Literal Disabled

```text
Literal = false
```

Case inputs preserve the reference-style pin behavior.

For example, a Text reference output may use Text reference input pins.

### Literal Enabled

```text
Literal = true
```

Case inputs use normal value pins instead.

For types such as `Text`, this allows values to be entered directly into the node:

```text
Case A    [Attack]
Case B    [Defend]
Case C    [Rest]
```

The option is kept in the Details panel because it changes the structure and editing behavior of the case pins rather than the matching rule itself.

## Case Priority

Cases are evaluated in their configured order.

The **first matching case wins**.

This is especially important when hierarchical matching is enabled.

For example:

```text
Cases:

0. Character.State.Combat
1. Character.State
2. Character
```

Input:

```text
Character.State.Combat.Attacking
```

may satisfy multiple cases when `Exact Match` is disabled.

The first matching case in the list is selected.

## Blueprint Integration

Gameplay Tag Select & Switch is designed to integrate naturally with the Blueprint editor.

The nodes:

- Use familiar native-style Select and Switch presentation
- Appear in appropriate Blueprint Action Menu categories
- Can be found easily by searching for `Select` or `Switch`
- Use concise names that remain visually distinct from Unreal Engine's native `Switch on ...` nodes

Example search results:

```text
Select
Select (Gameplay Tag)
Select (Gameplay Tag Container)
```

and:

```text
Switch on Gameplay Tag
Switch (Gameplay Tag)
Switch (Gameplay Tag Container)
```

## Node Reference

### Select (Gameplay Tag)

Inputs:

```text
Gameplay Tag
Exact Match
Default
<Case 1>
<Case 2>
...
```

Output:

```text
Return Value
```

Additional Details options:

```text
Case Type
Literal (when applicable)
```

### Select (Gameplay Tag Container)

Inputs:

```text
Gameplay Tag Container
Exact Match
Match All      // Only for Container Cases
Default
<Case 1>
<Case 2>
...
```

Output:

```text
Return Value
```

Additional Details options:

```text
Case Type
Literal (when applicable)
```

### Switch (Gameplay Tag)

Inputs:

```text
Exec
Gameplay Tag
Exact Match
```

Outputs:

```text
<Case 1>
<Case 2>
...
Default
```

Additional Details option:

```text
Case Type
```

### Switch (Gameplay Tag Container)

Inputs:

```text
Exec
Gameplay Tag Container
Exact Match
Match All      // Only for Container Cases
```

Outputs:

```text
<Case 1>
<Case 2>
...
Default
```

Additional Details option:

```text
Case Type
```

## Compilation

Gameplay Tag Select & Switch contains an **UncookedOnly** Blueprint node module.

The custom nodes are editor/compiler nodes and do not require a custom Gameplay Tag runtime system.

During Blueprint compilation, the nodes expand into Unreal Engine Blueprint operations using native Gameplay Tag matching and standard Blueprint flow/value nodes.

The plugin therefore does not require its custom Blueprint node implementation in packaged game runtime code.

## Installation

Copy the plugin folder into your project's `Plugins` directory:

```text
YourProject/
  Plugins/
    GameplayTagSelectAndSwitch/
```

The plugin directory should contain:

```text
GameplayTagSelectAndSwitch/
  GameplayTagSelectAndSwitch.uplugin
  Source/
    GameplayTagSelectAndSwitch/
```

Then:

1. Regenerate project files if required.
2. Build the Editor target.
3. Start Unreal Editor.
4. Enable **Gameplay Tag Select & Switch** if it is not already enabled.
5. Restart the editor if requested.

## Usage

### Create a Node

In a Blueprint graph:

1. Right-click to open the Blueprint Action Menu.
2. Search for `Select` or `Switch`.
3. Choose one of:

```text
Select (Gameplay Tag)
Select (Gameplay Tag Container)
Switch (Gameplay Tag)
Switch (Gameplay Tag Container)
```

### Configure Cases

Select the node and open its Details panel.

Choose:

```text
Case Type
```

Then configure the case list.

For Select nodes, connect or enter the corresponding values.

For Switch nodes, connect the desired execution outputs.

Set:

```text
Exact Match
```

as required.

For Gameplay Tag Container to Gameplay Tag Container matching, also configure:

```text
Match All
```

as required.

## Design Scope

Gameplay Tag Select & Switch is intentionally focused on one task:

> Use Gameplay Tag matching to drive native-style Blueprint Select and Switch behavior.

The plugin does not:

- Modify Gameplay Tags themselves
- Introduce a custom Gameplay Tag runtime system
- Replace Unreal Engine's existing Gameplay Tag API
- Require gameplay code to depend on custom runtime classes

Its goal is to make common Gameplay Tag selection and execution-routing patterns more expressive while remaining familiar to Blueprint users.