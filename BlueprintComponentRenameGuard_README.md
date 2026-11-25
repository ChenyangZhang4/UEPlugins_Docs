# Blueprint Component Rename Guard

Renaming a scene component in a parent Blueprint can cause components attached to it in derived Blueprints to fall back to `DefaultSceneRoot`. This editor plugin preserves the attachment, socket and relative transform, even when child and grandchild Blueprints are closed.

## Install from source

Copy the plugin folder into your project's `Plugins/` directory. Build the project's **Development Editor** target, then enable **Blueprint Component Rename Guard** under **Edit → Plugins**.

## Use

In the parent Blueprint's **Components** panel, select the component, press **F2**, enter its new name and press **Enter**. Use **Save All** to save the parent and affected descendants together.

**Tested on Windows** with UE 5.3.2, 5.5.4, 5.8.0 and 5.9 Preview (CL 60738873).
