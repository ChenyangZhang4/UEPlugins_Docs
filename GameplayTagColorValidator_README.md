# Gameplay Tag Color & Validator

**Gameplay Tag Color & Validator** is an editor-only Unreal Engine plugin that improves Gameplay Tag readability and helps detect invalid or unregistered Gameplay Tag references before they become harder to track down.

It adds configurable colors to native Gameplay Tag chips and provides invalid-tag validation for Blueprints and common data assets.

**Supported Unreal Engine versions:** UE 5.3–5.8

## Features

### Gameplay Tag Coloring

- Colors native Gameplay Tag chips in the Unreal Editor.
- Supports both **Border Color** and **Text Color** independently.
- Adjustable **Border Opacity Multiplier**.
- Manual Tag-to-color rules.
- Child Tags inherit the color of the nearest matching manually colored parent Tag.
- Optional **Auto Color By Root Tag Hash** for Tags without a manual color rule.
- Tags without an assigned color can keep the native UE border while still using the configured border opacity multiplier.
- Coloring is applied to Gameplay Tag chips in supported Details/Property Editor views and Blueprint Gameplay Tag pins.
- Settings are stored as per-project, per-user editor preferences and do not affect runtime data.

Example manual rules:

```text
Character                Blue
Character.State.Combat   Red
Item                     Yellow
```

With these rules:

```text
Character.State.Idle          -> Blue
Character.State.Combat.Attack -> Red
Item.Weapon.Sword             -> Yellow
```

The nearest matching parent rule takes priority.

### Invalid Gameplay Tag Highlighting

When a Gameplay Tag reference is no longer registered, the plugin displays an Unreal-style **Warning** or **Error** icon to the left of the Tag chip.

The Tag chip keeps its normal configured/native appearance, so invalid-tag indication does not replace the normal color classification.

Warning/Error display follows the validator severity setting.

### Blueprint Compile Validation

Blueprints can be checked automatically whenever they are compiled.

The validator checks Gameplay Tag references in:

- Gameplay Tag graph pin default values
- Gameplay Tag Container graph pin default values
- Blueprint node properties
- Blueprint member variable defaults
- Blueprint class defaults
- Nested supported properties containing Gameplay Tags

Invalid references are reported directly in **Compiler Results** as either warnings or errors.

When a problem belongs to a Blueprint node, the compiler message references that node so it can be located directly from the Compiler Results panel.

Automatic Blueprint validation can be disabled independently from the visual Tag highlighting.

### Content Browser Validation

Select one or more supported assets or folders in the Content Browser, then right-click and choose:

```text
Check Invalid Gameplay Tags
```

For folders, the scan is recursive and checks supported assets in all subfolders.

Results are displayed in the dedicated **Gameplay Tag Validator** Message Log.

Depending on the validator severity setting, invalid references are displayed as:

- **Warning** — yellow warning indicator
- **Error** — red error indicator

Large scans display progress and can be cancelled.

### Supported Assets

The Content Browser validator currently supports:

- Blueprint
- DataTable
- DataAsset
- PrimaryDataAsset
- User Defined Struct defaults

### Recursive Property Scanning

Gameplay Tag validation recursively scans supported property structures, including:

- `FGameplayTag`
- `FGameplayTagContainer`
- Structs
- Arrays
- Sets
- Maps
- `FInstancedStruct`

`FInstancedStruct` contents are inspected using the actual runtime-selected `UScriptStruct`, so Gameplay Tags nested inside instanced struct data are also validated.

Empty/unset Gameplay Tags are ignored and are not treated as invalid references.

## Settings

Open:

```text
Editor Preferences
  -> Plugins
  -> Gameplay Tag Color & Validator
```

### Display

**Enable Tag Colors**  
Master switch for normal Gameplay Tag coloring. Invalid Tag indication remains available independently.

**Show Border Color**  
Applies the resolved Tag color to the native Gameplay Tag chip border/background.

**Show Text Color**  
Applies the resolved Tag color to the text inside the Gameplay Tag chip.

**Border Opacity Multiplier**  
Multiplies the final Tag chip border opacity. It also affects the native border when a Tag falls back to Unreal Engine's default border appearance.

### Colors

**Auto Color By Root Tag Hash**  
Automatically generates a stable color from the root Gameplay Tag when no manual color rule matches.

**Manual Colors**  
Defines manual Tag-to-color overrides. The nearest matching Tag or parent Tag has priority over the automatic root color.

### Invalid Tags

**Enable Compiler Invalid Tag Validation**  
Enables automatic invalid Gameplay Tag checks during Blueprint compilation.

Disabling this setting does not disable the Content Browser **Check Invalid Gameplay Tags** command.

**Compiler Invalid Tags Are Errors**  
When enabled, invalid Gameplay Tags are reported as errors. Otherwise they are reported as warnings.

This severity is also used by Content Browser validation results and invalid Tag icons.

## Installation

1. Copy the plugin folder into your project's Plugins directory:

```text
YourProject/
  Plugins/
    GameplayTagColorValidator/
```

2. Regenerate project files if required.
3. Build your Editor target.
4. Start Unreal Editor and enable **Gameplay Tag Color & Validator** if it is not already enabled.
5. Restart the editor if requested.

The plugin contains an **Editor-only** module and does not add runtime Gameplay Tag logic to packaged builds.

## Usage

### Assign Gameplay Tag Colors

1. Open **Editor Preferences -> Plugins -> Gameplay Tag Color & Validator**.
2. Enable **Tag Colors**.
3. Choose whether to color the Border, Text, or both.
4. Add entries to **Manual Colors**, or enable **Auto Color By Root Tag Hash**.
5. Adjust **Border Opacity Multiplier** as needed.

### Check a Blueprint

Compile the Blueprint normally.

If automatic invalid-tag validation is enabled, invalid Gameplay Tag references are reported in **Compiler Results**.

### Check Assets or Folders

1. Select one or more supported assets and/or folders in the Content Browser.
2. Right-click.
3. Choose **Check Invalid Gameplay Tags**.
4. Review the results in the **Gameplay Tag Validator** Message Log.

## Design Scope

The plugin is intentionally focused on editor-side Gameplay Tag visibility and validation:

- It does not modify Gameplay Tag runtime behavior.
- It does not add project gameplay data to Tags.
- It does not require a separate project-wide validation workflow.
- Folder validation can be used when a broader recursive scan is needed.

The goal is to keep Gameplay Tags easier to distinguish while making stale Tag references easier to notice and locate during normal editor work.
