# Gameplay Tag Select & Switch

**Gameplay Tag Select & Switch** is an Unreal Engine Blueprint editor plugin that adds flexible **Select** and **Switch** nodes driven by Gameplay Tags and Gameplay Tag Containers.

The plugin is designed to integrate naturally with Unreal Engine's Blueprint workflow while providing more flexible Gameplay Tag matching than the built-in Select and Switch nodes.

**Supported Unreal Engine Version:** UE 5.8

---

## Nodes

The plugin adds four Blueprint nodes:

* **Select (Gameplay Tag)**
* **Select (Gameplay Tag Container)**
* **Switch (Gameplay Tag)**
* **Switch (Gameplay Tag Container)**

The type in parentheses represents the node's **input type**.

Each node can independently use either:

* Gameplay Tag cases
* Gameplay Tag Container cases

This supports all four matching combinations:

```text
Gameplay Tag           -> Gameplay Tag
Gameplay Tag           -> Gameplay Tag Container
Gameplay Tag Container -> Gameplay Tag
Gameplay Tag Container -> Gameplay Tag Container
```

---

## Select Nodes

### Select (Gameplay Tag)

Selects a value based on a single `FGameplayTag`.

### Select (Gameplay Tag Container)

Selects a value based on an `FGameplayTagContainer`.

Select nodes behave similarly to Unreal Engine's native **Select** node.

They provide:

```text
Input
Exact Match
Match All      // When applicable
Default
Cases...
Return Value
```

Cases are evaluated in order.

The **first matching case wins**.

If no case matches, the **Default** value is returned.

---

## Switch Nodes

### Switch (Gameplay Tag)

Routes execution based on a single `FGameplayTag`.

### Switch (Gameplay Tag Container)

Routes execution based on an `FGameplayTagContainer`.

Each configured case creates an execution output.

If no case matches, execution continues through the **Default** output.

Cases are evaluated in order and the first matching case is used.

---

## Case Type

Every node provides a **Case Type** setting in the Details panel.

Available Case Types:

```text
Gameplay Tag
Gameplay Tag Container
```

The input type and Case Type are independent.

For example:

```text
Input:
Gameplay Tag Container

Case Type:
Gameplay Tag
```

checks the input container against individual Gameplay Tag cases.

Changing **Case Type** clears all existing cases because Gameplay Tag cases and Gameplay Tag Container cases use different data structures.

---

## Exact Match

All four nodes expose **Exact Match** directly as a Boolean input pin.

### Exact Match Enabled

```text
Exact Match = true
```

Only exact Gameplay Tag matches are accepted.

### Exact Match Disabled

```text
Exact Match = false
```

Gameplay Tag hierarchy matching is allowed.

Example:

```text
Input:
State.Combat.Melee

Case:
State.Combat
```

With `Exact Match` disabled, the case can match the input using Unreal Engine's normal hierarchical Gameplay Tag matching behavior.

---

## Gameplay Tag Container Cases

When **Case Type** is set to `Gameplay Tag Container`, each case can contain multiple Gameplay Tags.

Container cases are displayed directly on the node using the format:

```text
(3) Character.State.Combat
```

Where:

```text
(3)
```

is the number of Gameplay Tags in the container, and:

```text
Character.State.Combat
```

is the complete first Gameplay Tag.

The first Gameplay Tag is not truncated.

An empty container is displayed as:

```text
(0) None
```

### Container Tooltip

Hovering over a Container Case displays the complete container:

```text
Gameplay Tag Container (3)

• Character.State.Combat
• Character.State.Burning
• Character.State.Attacking
```

This keeps the node readable while still making the complete case contents easily accessible.

---

## Match All

**Match All** is available only when both the input and Case Type are Gameplay Tag Containers:

```text
Gameplay Tag Container
        ->
Gameplay Tag Container
```

It is displayed directly as a Boolean input pin.

### Match All Disabled

```text
Match All = false
```

The case matches when **any** Gameplay Tag in the Case Container matches the input container.

### Match All Enabled

```text
Match All = true
```

The case matches only when **all** Gameplay Tags in the Case Container match the input container.

Example:

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

---

## Matching Rules

### Gameplay Tag -> Gameplay Tag

```text
Input Tag matches Case Tag
```

---

### Gameplay Tag -> Gameplay Tag Container

```text
Input Tag matches ANY Tag in the Case Container
```

---

### Gameplay Tag Container -> Gameplay Tag

```text
ANY Tag in the Input Container matches the Case Tag
```

---

### Gameplay Tag Container -> Gameplay Tag Container

When `Match All` is disabled:

```text
ANY Case Tag must match
```

When `Match All` is enabled:

```text
ALL Case Tags must match
```

`Exact Match` controls whether exact or hierarchical Gameplay Tag matching is used.

---

## Wildcard Select Values

Select nodes use wildcard value pins similar to Unreal Engine's native Select node.

The following pins share the same resolved value type:

```text
Cases
Default
Return Value
```

Supported types include standard Blueprint-compatible types such as:

* Boolean
* Integer
* Float
* Name
* String
* Text
* Enums
* Structs
* Object references
* Class references
* Other Blueprint-compatible types

Connecting a typed value resolves the wildcard value type for the node.

---

## Persistent Value Type

Once a Select node's wildcard value type has been resolved, the type is retained.

For example:

```text
Wildcard
   ↓
Connect Text
   ↓
Text
```

If the connection is later removed:

```text
Text
   ↓
Disconnect
   ↓
Text
```

the node remains typed as `Text`.

The value type is also preserved when:

* Rebuilding cases
* Refreshing the node
* Saving and reopening the Blueprint
* Reopening the Unreal Editor

This avoids unnecessary wildcard resets while editing Blueprint graphs.

---

## Reset Value Type

Select nodes provide a context-menu action:

```text
Reset Value Type
```

Use this when you want the node to return to Wildcard and resolve a different value type.

The action is available when the value pins are not connected.

Example:

```text
Text
   ↓
Reset Value Type
   ↓
Wildcard
```

You can then connect a new type and allow the node to resolve again.

This separates two different operations clearly:

```text
Disconnect
```

means:

> Keep the current value type.

While:

```text
Reset Value Type
```

means:

> Return the node to Wildcard.

---

## Literal Select Inputs

When a Select node resolves to a supported reference-style type, the Details panel can expose the **Literal** option.

### Literal Disabled

```text
Literal = false
```

Case inputs preserve reference-style pin behavior.

### Literal Enabled

```text
Literal = true
```

Case inputs use normal value pins instead.

For example, with `Text`:

```text
Case 1    [Attack]
Case 2    [Defend]
Case 3    [Rest]
```

can be entered directly into the node.

The **Literal** option is kept in the Details panel because it changes the structure and editing behavior of the value pins rather than the Gameplay Tag matching rule.

---

## Case Priority

Cases are evaluated from top to bottom.

The first matching case wins.

This is especially important when `Exact Match` is disabled.

Example:

```text
0. Character.State.Combat
1. Character.State
2. Character
```

Input:

```text
Character.State.Combat.Attacking
```

may match more than one case.

The first matching case in the configured list is selected.

---

## Blueprint Editor Integration

The plugin uses concise node names so that they remain easy to search while staying visually distinct from Unreal Engine's built-in nodes.

Searching for:

```text
Select
```

can show:

```text
Select
Select (Gameplay Tag)
Select (Gameplay Tag Container)
```

Searching for:

```text
Switch
```

can show:

```text
Switch on Gameplay Tag
Switch (Gameplay Tag)
Switch (Gameplay Tag Container)
```

The plugin nodes use native-style Blueprint presentation and familiar Select / Switch behavior.

---

## Node Reference

### Select (Gameplay Tag)

Inputs:

```text
Gameplay Tag
Exact Match
Default
Cases...
```

Output:

```text
Return Value
```

Details:

```text
Case Type
Literal      // When applicable
```

---

### Select (Gameplay Tag Container)

Inputs:

```text
Gameplay Tag Container
Exact Match
Match All    // Only with Container Cases
Default
Cases...
```

Output:

```text
Return Value
```

Details:

```text
Case Type
Literal      // When applicable
```

---

### Switch (Gameplay Tag)

Inputs:

```text
Exec
Gameplay Tag
Exact Match
```

Outputs:

```text
Cases...
Default
```

Details:

```text
Case Type
```

---

### Switch (Gameplay Tag Container)

Inputs:

```text
Exec
Gameplay Tag Container
Exact Match
Match All    // Only with Container Cases
```

Outputs:

```text
Cases...
Default
```

Details:

```text
Case Type
```

---

## Compilation

Gameplay Tag Select & Switch uses an **UncookedOnly** Blueprint-node module.

The custom nodes are expanded during Blueprint compilation into Unreal Engine's native Blueprint logic, including:

* Gameplay Tag matching operations
* Standard Select behavior
* Standard execution flow nodes

The plugin does not introduce a custom Gameplay Tag runtime system.

The custom editor node implementation is not required in packaged game runtime code.

---

## Installation

Copy the plugin folder into your project's `Plugins` directory:

```text
YourProject/
  Plugins/
    GameplayTagSelectAndSwitch/
```

The plugin structure should include:

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
4. Enable **Gameplay Tag Select & Switch** if required.
5. Restart the editor if prompted.

---

## Usage

### Create a Node

Right-click inside a Blueprint graph and search for:

```text
Select
```

or:

```text
Switch
```

Then choose one of:

```text
Select (Gameplay Tag)
Select (Gameplay Tag Container)
Switch (Gameplay Tag)
Switch (Gameplay Tag Container)
```

### Configure Cases

1. Select the node.
2. Open the Details panel.
3. Choose the desired **Case Type**.
4. Add Gameplay Tag or Gameplay Tag Container cases.
5. Arrange them in the desired priority order.
6. Configure **Exact Match**.
7. Configure **Match All** when using Container-to-Container matching.
8. Connect Select values or Switch execution outputs.

For Select nodes, the wildcard value type is automatically resolved when a typed value is connected.

---

## Design Scope

Gameplay Tag Select & Switch is intentionally focused on one task:

> Use Gameplay Tag matching to drive familiar Blueprint Select and Switch workflows.

The plugin does not:

* Modify Gameplay Tags
* Replace Unreal Engine's Gameplay Tag system
* Introduce a custom runtime Gameplay Tag framework
* Require gameplay code to depend on custom runtime classes

Its goal is to make Gameplay Tag-driven Blueprint logic more compact, readable, and flexible while remaining familiar to Unreal Engine developers.
