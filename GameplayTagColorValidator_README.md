# Gameplay Tag Color & Validator

**Gameplay Tag Color & Validator** is an editor-only Unreal Engine plugin that improves Gameplay Tag readability and helps detect invalid or unregistered Gameplay Tag references.

It adds configurable colors to native Gameplay Tag chips and provides validation for Blueprints and common data assets.

**Supported Unreal Engine Versions:** UE 5.3–5.8

---

## Features

### Gameplay Tag Coloring

Gameplay Tag Color & Validator can recolor native Gameplay Tag chips throughout supported Unreal Editor views.

Features include:

* Independent **Border Color** and **Text Color** display
* Adjustable **Border Opacity Multiplier**
* Manual Tag-to-color rules
* Parent Tag color inheritance
* Deterministic automatic colors based on root Gameplay Tags
* Configurable **Auto Color Seed**
* Stable automatic colors across editor restarts
* Native Unreal appearance for Tags without an assigned color
* Coloring in supported Details/Property Editor views and Blueprint Gameplay Tag pins

Color settings are stored as editor preferences and do not modify runtime Gameplay Tag data.

---

## Manual Colors

Manual rules can assign colors to individual Gameplay Tags or entire Tag branches.

Example:

```text
Character                Blue
Character.State.Combat   Red
Item                     Yellow
```

This produces:

```text
Character.State.Idle           -> Blue
Character.State.Combat.Attack  -> Red
Item.Weapon.Sword              -> Yellow
```

When multiple rules could apply, the nearest matching Tag or parent Tag takes priority.

Manual colors always take priority over automatic colors.

---

## Automatic Root Tag Colors

Enable:

```text
Auto Color By Root Tag Hash
```

to automatically generate colors for Tags that do not match a manual color rule.

Automatic colors are generated deterministically from:

```text
Root Gameplay Tag + Auto Color Seed
```

This means the same root Tag and Seed always produce the same color, including after restarting Unreal Editor or reopening the project.

For example:

```text
Character.State.Combat
Character.State.Idle
Character.Attribute.Health
```

all use the automatic color generated from:

```text
Character
```

unless a more specific manual color rule exists.

### Auto Color Seed

The **Auto Color Seed** setting changes the deterministic automatic color mapping.

For example:

```text
Seed = 0
```

produces one stable palette.

Changing it to:

```text
Seed = 1
```

produces a different palette.

As long as the Seed remains unchanged, the generated colors remain stable.

This allows the automatic color scheme to be changed without manually assigning colors to every root Gameplay Tag.

---

## Invalid Gameplay Tag Highlighting

When a referenced Gameplay Tag is no longer registered, the plugin can display an Unreal-style:

* **Warning** indicator
* **Error** indicator

next to the Gameplay Tag chip.

The Tag keeps its normal configured or native color appearance, so validation status does not replace normal color classification.

The displayed severity follows the validator severity setting.

---

## Blueprint Compile Validation

Blueprints can be validated automatically whenever they are compiled.

The validator checks Gameplay Tag references in:

* Gameplay Tag graph pin default values
* Gameplay Tag Container graph pin default values
* Blueprint node properties
* Blueprint member variable defaults
* Blueprint class defaults
* Nested supported properties containing Gameplay Tags

Invalid references are reported directly in:

```text
Compiler Results
```

as warnings or errors.

When the invalid reference belongs to a Blueprint node, the compiler result references that node so the problem can be located directly from the Compiler Results panel.

Automatic compile validation can be disabled independently from visual invalid-Tag highlighting.

---

## Content Browser Validation

Gameplay Tags can also be checked without compiling individual Blueprints.

Select one or more supported assets or folders in the Content Browser, right-click, and choose:

```text
Check Invalid Gameplay Tags
```

When folders are selected, scanning is recursive and includes supported assets in subfolders.

Results are displayed in the dedicated:

```text
Gameplay Tag Validator
```

Message Log.

Depending on the configured severity, invalid references are reported as:

* Warning
* Error

Large scans display progress and can be cancelled.

---

## Supported Assets

Content Browser validation currently supports:

* Blueprint
* DataTable
* DataAsset
* PrimaryDataAsset
* User Defined Struct defaults

---

## Recursive Property Scanning

The validator recursively scans supported property structures containing:

* `FGameplayTag`
* `FGameplayTagContainer`
* Structs
* Arrays
* Sets
* Maps
* `FInstancedStruct`

For `FInstancedStruct`, the validator inspects the actual runtime-selected `UScriptStruct`.

This allows Gameplay Tags nested inside instanced struct data to be validated as well.

Empty or unset Gameplay Tags are ignored and are not treated as invalid references.

---

## Settings

Open:

```text
Editor Preferences
  -> Plugins
  -> Gameplay Tag Color & Validator
```

### Display

#### Enable Tag Colors

Master switch for normal Gameplay Tag coloring.

Invalid Tag indicators can continue to work independently when normal Tag coloring is disabled.

#### Show Border Color

Controls whether the resolved Gameplay Tag color is applied to the native Tag chip border/background.

Default:

```text
Enabled
```

#### Show Text Color

Controls whether the resolved Gameplay Tag color is applied to the Tag text.

#### Border Opacity Multiplier

Multiplies the final Gameplay Tag chip border opacity.

It also affects the native border appearance when no manual or automatic color is assigned.

Default:

```text
0.8
```

---

### Colors

#### Auto Color By Root Tag Hash

Automatically assigns deterministic colors based on root Gameplay Tags when no manual color rule matches.

#### Auto Color Seed

Changes the deterministic mapping between root Gameplay Tags and generated colors.

The same Tag and Seed always produce the same automatic color.

#### Manual Colors

Defines explicit Tag-to-color rules.

Priority is:

```text
Nearest Manual Color
        ↓
Automatic Root Tag Color
        ↓
Native Unreal Appearance
```

---

### Invalid Tags

#### Enable Compiler Invalid Tag Validation

Enables automatic invalid Gameplay Tag validation whenever a Blueprint is compiled.

Disabling this setting does not disable the Content Browser:

```text
Check Invalid Gameplay Tags
```

command.

#### Compiler Invalid Tags Are Errors

When enabled, invalid Gameplay Tags are reported as errors.

When disabled, they are reported as warnings.

The same severity is used by:

* Blueprint compiler validation
* Content Browser validation
* Invalid Gameplay Tag indicators

---

## Usage

### Configure Gameplay Tag Colors

1. Open:

```text
Editor Preferences
-> Plugins
-> Gameplay Tag Color & Validator
```

2. Enable **Tag Colors**.
3. Choose whether to display Border Color, Text Color, or both.
4. Add entries to **Manual Colors** if desired.
5. Enable **Auto Color By Root Tag Hash** for automatic coloring.
6. Adjust **Auto Color Seed** to choose a different stable automatic palette.
7. Adjust **Border Opacity Multiplier** as required.

Changes are applied to supported Gameplay Tag displays in the editor.

---

### Validate a Blueprint

Compile the Blueprint normally.

If automatic validation is enabled, invalid Gameplay Tag references are reported in:

```text
Compiler Results
```

---

### Validate Assets or Folders

1. Select one or more supported assets or folders in the Content Browser.
2. Right-click.
3. Choose:

```text
Check Invalid Gameplay Tags
```

4. Review the results in:

```text
Gameplay Tag Validator
```

Message Log.

---

## Installation

Copy the plugin folder into your project's Plugins directory:

```text
YourProject/
  Plugins/
    GameplayTagColorValidator/
```

Then:

1. Regenerate project files if required.
2. Build the Editor target.
3. Start Unreal Editor.
4. Enable **Gameplay Tag Color & Validator** if required.
5. Restart the editor if prompted.

The plugin contains an editor-only module and does not add custom Gameplay Tag runtime logic to packaged games.

---

## Design Scope

Gameplay Tag Color & Validator is focused on editor-side Gameplay Tag visibility and validation.

It does not:

* Modify Gameplay Tag runtime behavior
* Add gameplay data to Gameplay Tags
* Replace Unreal Engine's Gameplay Tag system
* Add a runtime validation framework
* Require packaged games to depend on plugin runtime code

Its goal is to make large Gameplay Tag hierarchies easier to read while making stale or invalid Gameplay Tag references easier to discover during normal development.
