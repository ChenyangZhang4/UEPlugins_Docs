# CustomViewportViews

CustomViewportViews is an Unreal Engine editor plugin that adds reusable custom viewing directions directly to the Level Editor viewport.

It allows custom orthographic and perspective views to behave more like Unreal Engine's built-in Perspective, Top, Front, and Side views, including toolbar integration, viewport persistence and selection centering.

## Overview

Unreal Engine provides several built-in orthographic viewport directions:

* Top
* Bottom
* Front
* Back
* Left
* Right

These are useful, but they are limited to the primary world axes.

For projects using isometric, 2D, 2.5D, top-down, side-scroller, ARPG, RTS, or other fixed-angle layouts, developers often need to manually rotate the Perspective viewport or create a temporary Camera Actor.

CustomViewportViews adds dedicated editor viewport presets for these workflows.

## Features

* Custom Level Editor viewport types
* Orthographic and perspective presets
* Native-style viewport switching
* Custom Camera toolbar name
* Persistent custom viewport state
* Per-viewport saved preset
* Correct persistence when returning to native Unreal Engine views
* Selection-centered view switching
* Configurable default custom view

## Included Presets

The default configuration includes:

### 2D Top Down

Orthographic view intended for top-down 2D layouts.

### 2D Side Scroller

Orthographic side-oriented view suitable for side-scrolling projects.

### Isometric

A fixed isometric-style editor view useful for isometric environments and grid-based scene editing.

### ARPG 45°

A predefined angled view intended for ARPG-style scene composition.

### RTS 60°

A higher-angle view intended for RTS-style environments.

### Third Person

A perspective preset useful as a convenient fixed editor viewing direction for third-person projects.

## Usage

After enabling the plugin, open a Level Editor viewport and use the viewport Camera menu.

The custom presets are available alongside the normal viewport options.

Selecting a custom view will apply its configured:

* Projection mode
* Viewing direction
* Rotation
* Orthographic zoom or perspective settings

The currently active custom view is also shown in the viewport toolbar.

## Selection Centering

When a custom view is selected manually, the plugin checks the current editor selection.

If actors are selected, the new view is centered around the selection instead of an unrelated viewport location.

This allows a workflow such as:

1. Select an actor.
2. Choose `2D Top Down`, `Isometric`, or another custom view.
3. Continue editing with the selected actor already centered in the viewport.

Startup restoration does not intentionally re-center the viewport around the current selection.

## Viewport Persistence

CustomViewportViews integrates with the Level Editor viewport layout system.

When a custom viewport is active, its custom viewport type and preset can be restored when the Unreal Editor is restarted.

For example:

```text
2D Top Down
→ Close Unreal Editor
→ Reopen Project
→ 2D Top Down
```

Returning to a native Unreal Engine viewport is also preserved:

```text
2D Top Down
→ Perspective
→ Close Unreal Editor
→ Reopen Project
→ Perspective
```

Each viewport slot can keep its own saved custom preset.

This is useful with multi-viewport layouts where different panes use different viewing directions.

## Multiple Viewports

Custom views are stored using the individual Level Editor viewport configuration key.

A layout may therefore contain a combination such as:

```text
Perspective | 2D Top Down
------------|-------------
Isometric   | Front
```

Each viewport maintains its own state independently.

## Settings

Plugin settings are available in the Unreal Editor project settings.

### Default View Name

Defines which custom preset is used when a newly created custom viewport does not have a previously saved preset.

The default value is:

```text
2D Top Down
```

This setting does not replace already saved viewport presets.

## Editor Integration

CustomViewportViews integrates with the existing Level Editor viewport rather than opening a separate editor viewport window.

The plugin uses a dedicated viewport layout entity so that Unreal Engine can correctly identify and persist custom viewport types.

The toolbar integration updates the existing Camera/View control with the active custom view name and icon.

## Installation

Copy the plugin folder into your project's Plugins directory:

```text
YourProject/
└── Plugins/
    └── CustomViewportViews/
```

Then regenerate project files if necessary and rebuild the project.

After launching Unreal Editor, enable the plugin if it is not already enabled.

Restart the editor when requested.

## Runtime Impact

CustomViewportViews is an editor-focused plugin.

It does not replace or modify:

* Player cameras
* Camera Actors used at runtime
* Gameplay camera logic
* Packaged-game rendering behavior

Its purpose is to improve the Level Editor scene-editing workflow.
