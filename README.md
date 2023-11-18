# Belly: Declare BevyUI with Styles, Classes and Dataflow

The `belly` is a plugin for the [`bevy` game engine](https://github.com/bevyengine/bevy) that helps to declaratively define a user interface with `eml` markup (macros & assets), style it with a very CSS-like `ess` syntax, and define data flow using `from!` & `to!` bind macros and/or connect events to handlers (funcs).

If you want to get started right away, check out the [User Guide](docs/user-guide.md).

## About

The main goals of the `belly` plugin are:

- fill in the missing pieces in the `bevy` UI system (inputs, scrolls, text layout, etc.)
- reduce the [boilerplate](https://bevyengine.org/examples/ui/ui/) defining the UI
- allow effective separatation of the layout, styling, data & logic from each other
- build the basis to provide various tools for *`game`* developers & designers

### Features

- Hierarcy definition using `eml` macro or asset
- Style definition using direct attributes or `ess` stylesheet assets
- Hot-reloading for `eml` and `ess` assets
- Data bindings for resources, components, params and content
- Event -> handler connections
- Predefined styles properties for every piece os UI
- Basic templating (for-loops, slots)
- Out of the box default styles & fonts
- Ability to define custom widgets, properties, and bind transformers
- Style & behaviour extending
- Predefined widgets for configuring layout, generating content and handling input:
  - body, div, span, br, strong
  - img, progressbar, label
  - textinput, slider, button, buttongroup
- Styleboxes (9-patch-slices/`image-border`)

### Upcoming features

- Complete rich-text processing
- True inline/block/inline-block elements
- Binding transitions (changing values over time)
- Style transitions (changing style properties over time)
- Scene-based widgets
- Styled drawing primitives (lines, rects, curves, shapes)
- Asset validation tools
- In-game developer panel with UI tree & style inspector
- More widgets (tabview, scrollarea, checkbox, attach, line, popup, tooltip)
- Developer tools (vscode plugin)
- Localization
- Scripting
- Theming
- Asset loading progress handling
- More templating (if/else)

## Example

```rust
// examples/color-picker.rs
// cargo run --example color-picker
use belly::prelude::*;
use bevy::prelude::*;

fn main() {
    App::new()
        .add_plugins(DefaultPlugins)
        .add_plugins(BellyPlugin)
        .add_systems(Startup, setup)
        .run();
}

const COLORS: &[&'static str] = &[
    // from https://colorswall.com/palette/105557
    // Red     Pink       Purple     Deep Purple
    "#f44336", "#e81e63", "#9c27b0", "#673ab7",
    // Indigo  Blue       Light Blue Cyan
    "#3f51b5", "#2196f3", "#03a9f4", "#00bcd4",
    // Teal    Green      Light      Green Lime
    "#009688", "#4caf50", "#8bc34a", "#cddc39",
    // Yellow  Amber      Orange     Deep Orange
    "#ffeb3b", "#ffc107", "#ff9800", "#ff5722",
];

fn setup(mut commands: Commands) {
    commands.spawn(Camera2dBundle::default());
    commands.add(StyleSheet::load("color-picker.ess"));
    let colorbox = commands.spawn_empty().id();
    commands.add(eml! {
        <body>
            <span c:controls>
                <slider c:red
                    bind:value=to!(colorbox, BackgroundColor:0|r)
                    bind:value=from!(colorbox, BackgroundColor:0.r())
                />
                <slider c:green
                    bind:value=to!(colorbox, BackgroundColor:0|g)
                    bind:value=from!(colorbox, BackgroundColor:0.g())
                />
                <slider c:blue
                    bind:value=to!(colorbox, BackgroundColor:0|b)
                    bind:value=from!(colorbox, BackgroundColor:0.b())
                />
                <slider c:alpha
                    bind:value=to!(colorbox, BackgroundColor:0|a)
                    bind:value=from!(colorbox, BackgroundColor:0.a())
                />
            </span>
            <img c:colorbox-holder src="trbg.png">
                <span {colorbox} c:colorbox s:background-color=managed()
                    on:ready=run!(|c: &mut BackgroundColor| c.0 = Color::WHITE)/>
            </img>
            <span c:colors>
            <for color in = COLORS>
                <button on:press=run!(for colorbox |c: &mut BackgroundColor| { c.0 = Color::from_hex(color) })>
                    <span s:background-color=*color s:width="100%" s:height="100%"/>
                </button>
            </for>
            </span>
        </body>
    });
}
```

![Color Picker](docs/img/examples/color-picker.gif)

## Reference Documentation

- [User Guide](docs/user-guide.md)
- [Style Properties](docs/style-properties.md)
- [Widgets](docs/widgets.md)
- Bind transformers (work in progress)
- Crate docs (work in progress)

## License

The `belly` is dual-licensed under either:

- MIT License ([LICENSE-MIT](LICENSE-MIT) or [http://opensource.org/licenses/MIT](http://opensource.org/licenses/MIT))
- Apache License, Version 2.0 ([LICENSE-APACHE](LICENSE-APACHE) or [http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0))

This means you can select the license you prefer!
This dual-licensing approach is the de-facto standard in the Rust ecosystem and there are [very good reasons](https://github.com/bevyengine/bevy/issues/2373) to include both.
