# Crate Documentation

**Version:** 0.11.3

**Format Version:** 57

# Module `gtk4`

# Rust GTK 4 bindings

The project website is [here](https://gtk-rs.org/).

Rust bindings of [GTK 4](http://www.gtk.org), part of [gtk4-rs](https://github.com/gtk-rs/gtk4-rs/).

This library contains safe Rust bindings for [GTK 4](http://www.gtk.org), a
multi-platform GUI toolkit. It is a part of [gtk-rs](http://gtk-rs.org/).

Most of this documentation is generated from the C API.
Until all parts of the documentation have been reviewed there will be incongruities
with the actual Rust API.

For a gentle introduction to gtk-rs we recommend the online book
[*GUI development with Rust and GTK 4*](https://gtk-rs.org/gtk4-rs/stable/latest/book/).

See also:

 - [gtk-rs project overview](https://gtk-rs.org)
 - [General `GLib` family types and object system overview](mod@glib)
 - [GTK documentation](https://www.gtk.org/docs/)
 - [GTK Visual Index](https://docs.gtk.org/gtk4/visual_index.html)

## Minimum supported Rust version

Currently, the minimum supported Rust version is `1.83`.

## "Hello, World!" example program

GTK needs to be initialized before use by calling [`init`][`fn@init`]. Creating an
[`Application`][`struct@Application`] will call [`init`][`fn@init`] for you.

The [`gtk4`](mod@crate) crate is usually renamed to `gtk`. You can find an example in
the [features](#features) section for how to do this globally in your `Cargo.toml`.

```rust,no_run
use gtk4 as gtk;
use gtk::prelude::*;
use gtk::{glib, Application, ApplicationWindow};

fn main() -> glib::ExitCode {
    let app = Application::builder()
        .application_id("org.example.HelloWorld")
        .build();

    app.connect_activate(|app| {
        // We create the main window.
        let window = ApplicationWindow::builder()
            .application(app)
            .default_width(320)
            .default_height(200)
            .title("Hello, World!")
            .build();

        // Show the window.
        window.present();
    });

    app.run()
}
```

## The main loop

In a typical GTK application you set up the UI, assign signal handlers
and run the main event loop.

```rust,no_run
use gtk4 as gtk;
use gtk::prelude::*;
use gtk::{glib, Application, ApplicationWindow, Button};

fn main() -> glib::ExitCode {
    let application = Application::builder()
        .application_id("com.example.FirstGtkApp")
        .build();

    application.connect_activate(|app| {
        let window = ApplicationWindow::builder()
            .application(app)
            .title("First GTK Program")
            .default_width(350)
            .default_height(70)
            .build();

        let button = Button::with_label("Click me!");
        button.connect_clicked(|_| {
            eprintln!("Clicked!");
        });
        window.set_child(Some(&button));

        window.present();
    });

    application.run()
}
```

## Threads

GTK is not thread-safe. Accordingly, none of this crate's structs implement
[`Send`] or [`Sync`].

The thread where [`init`][`fn@init`] was called is considered the main thread. OS X has
its own notion of the main thread and [`init`][`fn@init`] must be called on that thread.
After successful initialization, calling any [`gtk`](mod@crate) or [`gdk`][`mod@gdk`]
functions (including [`init`][`fn@init`]) from other threads will `panic`.

Any thread can schedule a closure to be run by the main loop on the main
thread via [`glib::idle_add`][`fn@glib::idle_add`] or
[`glib::timeout_add`][`fn@glib::timeout_add`]. While
working with GTK you might need the [`glib::idle_add_local`][`fn@glib::idle_add_local`]
or [`glib::timeout_add_local`][`fn@glib::timeout_add_local`] version without the
[`Send`] bound. Those may only be called from the main thread.

# Panics

The [`gtk`](mod@crate) and [`gdk`][`mod@gdk`] crates have some run-time safety and contract
checks.

- Any constructor or free function will panic if called before [`init`][`fn@init`] or on
  a non-main thread.

- Any [`&str`] or [`&Path`](std::path::Path) parameter with an interior null (`\0`) character will cause a panic.

- Some functions will panic if supplied out-of-range integer parameters. All
  such cases will be documented individually but they are not yet.

- A panic in a closure that handles signals or in any other closure passed
  to a [`gtk`](mod@crate) function will abort the process.

## Features

### Library versions

By default this crate provides only GTK 4.0 APIs. You can access additional
functionality by selecting one of the `v4_2`, `v4_4`, etc. features.

`Cargo.toml` example:

```toml
[dependencies.gtk]
package = "gtk4"
version = "0.x.y"
features = ["v4_2"]
```

Take care when choosing the version to target: some of your users might
not have easy access to the latest ones. The higher the version, the fewer
users will have it installed.

## Documentation

- The Rust API [Stable](https://gtk-rs.org/gtk4-rs/stable/latest/docs/gtk4)/[Development](https://gtk-rs.org/gtk4-rs/git/docs/gtk4/)
- Book [Stable](https://gtk-rs.org/gtk4-rs/stable/latest/book)/[Development](https://gtk-rs.org/gtk4-rs/git/book)
- [Examples](https://github.com/gtk-rs/gtk4-rs/tree/main/examples)
- [The C API](https://docs.gtk.org/gtk4/)
- [GTK Installation Instructions](https://www.gtk.org/docs/installations/)

## Using

We recommend using [crates from crates.io](https://crates.io/keywords/gtk-rs),
as [demonstrated here](https://gtk-rs.org/gtk4-rs/stable/latest/docs/gtk4/index.html#library-versions).

If you want to track the bleeding edge, use the git dependency instead:

```toml
[dependencies]
gtk = { git = "https://github.com/gtk-rs/gtk4-rs.git", package = "gtk4" }
```

Avoid mixing versioned and git crates like this:

```toml
# This will not compile
[dependencies]
gdk = {version = "0.1", package = "gdk4"}
gtk = { git = "https://github.com/gtk-rs/gtk4-rs.git", package = "gtk4" }
```

### Features

| Feature | Description |
| ---     | ----------- |
| `v4_22` | Enable the new APIs part of GTK 4.22 |
| `v4_20` | Enable the new APIs part of GTK 4.20 |
| `v4_18` | Enable the new APIs part of GTK 4.18 |
| `v4_16` | Enable the new APIs part of GTK 4.16 |
| `v4_14` | Enable the new APIs part of GTK 4.14 |
| `v4_12` | Enable the new APIs part of GTK 4.12 |
| `v4_10` | Enable the new APIs part of GTK 4.10 |
| `v4_8` | Enable the new APIs part of GTK 4.8 |
| `v4_6` | Enable the new APIs part of GTK 4.6 |
| `v4_4` | Enable the new APIs part of GTK 4.4 |
| `v4_2` | Enable the new APIs part of GTK 4.2 |
| `gnome_50` | Enable all version feature flags of this crate and its dependencies to match the GNOME 50 SDK |
| `gnome_49` | Enable all version feature flags of this crate and its dependencies to match the GNOME 49 SDK |
| `gnome_48` | Enable all version feature flags of this crate and its dependencies to match the GNOME 48 SDK |
| `gnome_47` | Enable all version feature flags of this crate and its dependencies to match the GNOME 47 SDK |
| `gnome_46` | Enable all version feature flags of this crate and its dependencies to match the GNOME 46 SDK |
| `gnome_45` | Enable all version feature flags of this crate and its dependencies to match the GNOME 45 SDK |
| `gnome_44` | Enable all version feature flags of this crate and its dependencies to match the GNOME 44 SDK |
| `gnome_43` | Enable all version feature flags of this crate and its dependencies to match the GNOME 43 SDK |
| `gnome_42` | Enable all version feature flags of this crate and its dependencies to match the GNOME 42 SDK |
| `unsafe-assume-initialized` | Disable checks that gtk is initialized, for use in C ABI libraries |
| `xml_validation` | Enable `xml_validation` feature of gtk4-macros |
| `blueprint` | Enable `blueprint` feature of gtk4-macros |

### See Also

- [glib](https://crates.io/crates/glib)
- [gio](https://crates.io/crates/gio)
- [gdk4](https://crates.io/crates/gdk4)
- [gsk4](https://crates.io/crates/gsk4)

## License

The Rust bindings of __gtk4__ are available under the MIT License, please refer to it.

## Modules

## Module `subclass`

**Attributes:**

- `Other("#[attr = MacroUse {arguments: UseAll}]")`

Traits intended for creating custom types.

```rust
pub mod subclass { /* ... */ }
```

### Modules

## Module `accessible`

**Attributes:**

- `Other("#[attr = CfgTrace([NameValue { name: \"feature\", value: Some(\"v4_10\"), span: src/subclass/mod.rs:17:7: 17:24 (#0) }])]")`
- `Other("#[attr = CfgAttrTrace]")`
- `Other("#[doc(cfg(feature = \"v4_10\"))]")`

Traits intended for implementing the [`Accessible`] interface.

```rust
pub mod accessible { /* ... */ }
```

### Traits

#### Trait `AccessibleImpl`

```rust
pub trait AccessibleImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::Accessible>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn platform_state(self: &Self, state: AccessiblePlatformState) -> bool { /* ... */ }
  ```

- ```rust
  fn bounds(self: &Self) -> Option<(i32, i32, i32, i32)> { /* ... */ }
  ```

- ```rust
  fn at_context(self: &Self) -> Option<ATContext> { /* ... */ }
  ```

- ```rust
  fn accessible_parent(self: &Self) -> Option<Accessible> { /* ... */ }
  ```

- ```rust
  fn first_accessible_child(self: &Self) -> Option<Accessible> { /* ... */ }
  ```

- ```rust
  fn next_accessible_sibling(self: &Self) -> Option<Accessible> { /* ... */ }
  ```

#### Trait `AccessibleImplExt`

```rust
pub trait AccessibleImplExt: AccessibleImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_platform_state(self: &Self, state: AccessiblePlatformState) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_bounds(self: &Self) -> Option<(i32, i32, i32, i32)> { /* ... */ }
  ```

- ```rust
  fn parent_at_context(self: &Self) -> Option<ATContext> { /* ... */ }
  ```

- ```rust
  fn parent_accessible_parent(self: &Self) -> Option<Accessible> { /* ... */ }
  ```

- ```rust
  fn parent_first_accessible_child(self: &Self) -> Option<Accessible> { /* ... */ }
  ```

- ```rust
  fn parent_next_accessible_sibling(self: &Self) -> Option<Accessible> { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: AccessibleImpl>

## Module `accessible_range`

**Attributes:**

- `Other("#[attr = CfgTrace([NameValue { name: \"feature\", value: Some(\"v4_10\"), span: src/subclass/mod.rs:20:7: 20:24 (#0) }])]")`
- `Other("#[attr = CfgAttrTrace]")`
- `Other("#[doc(cfg(feature = \"v4_10\"))]")`

Traits intended for implementing the [`AccessibleRange`] interface.

```rust
pub mod accessible_range { /* ... */ }
```

### Traits

#### Trait `AccessibleRangeImpl`

```rust
pub trait AccessibleRangeImpl: AccessibleImpl + ObjectSubclass<Type: IsA<crate::AccessibleRange>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn set_current_value(self: &Self, value: f64) -> bool { /* ... */ }
  ```

#### Trait `AccessibleRangeImplExt`

```rust
pub trait AccessibleRangeImplExt: AccessibleRangeImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_set_current_value(self: &Self, value: f64) -> bool { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: AccessibleRangeImpl>

## Module `accessible_text`

**Attributes:**

- `Other("#[attr = CfgTrace([NameValue { name: \"feature\", value: Some(\"v4_14\"), span: src/subclass/mod.rs:23:7: 23:24 (#0) }])]")`
- `Other("#[attr = CfgAttrTrace]")`
- `Other("#[doc(cfg(feature = \"v4_14\"))]")`

Traits intended for implementing the [`AccessibleText`] interface.

```rust
pub mod accessible_text { /* ... */ }
```

### Traits

#### Trait `AccessibleTextImpl`

```rust
pub trait AccessibleTextImpl: WidgetImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn attributes(self: &Self, offset: u32) -> Vec<(AccessibleTextRange, GString, GString)> { /* ... */ }
  ```

- ```rust
  fn caret_position(self: &Self) -> u32 { /* ... */ }
  ```

- ```rust
  fn contents(self: &Self, start: u32, end: u32) -> Option<glib::Bytes> { /* ... */ }
  ```

- ```rust
  fn contents_at(self: &Self, offset: u32, granularity: crate::AccessibleTextGranularity) -> Option<(u32, u32, glib::Bytes)> { /* ... */ }
  ```

- ```rust
  fn default_attributes(self: &Self) -> Vec<(GString, GString)> { /* ... */ }
  ```

- ```rust
  fn extents(self: &Self, start: u32, end: u32) -> Option<graphene::Rect> { /* ... */ }
  ```

- ```rust
  fn offset(self: &Self, point: &graphene::Point) -> Option<u32> { /* ... */ }
  ```

- ```rust
  fn selection(self: &Self) -> Vec<AccessibleTextRange> { /* ... */ }
  ```

- ```rust
  fn set_caret_position(self: &Self, position: u32) -> bool { /* ... */ }
  ```

- ```rust
  fn set_selection(self: &Self, selection: usize, range: AccessibleTextRange) -> bool { /* ... */ }
  ```

#### Trait `AccessibleTextImplExt`

```rust
pub trait AccessibleTextImplExt: AccessibleTextImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_attributes(self: &Self, offset: u32) -> Vec<(AccessibleTextRange, GString, GString)> { /* ... */ }
  ```

- ```rust
  fn parent_caret_position(self: &Self) -> u32 { /* ... */ }
  ```

- ```rust
  fn parent_contents(self: &Self, start: u32, end: u32) -> Option<glib::Bytes> { /* ... */ }
  ```

- ```rust
  fn parent_contents_at(self: &Self, offset: u32, granularity: crate::AccessibleTextGranularity) -> Option<(u32, u32, glib::Bytes)> { /* ... */ }
  ```

- ```rust
  fn parent_default_attributes(self: &Self) -> Vec<(GString, GString)> { /* ... */ }
  ```

- ```rust
  fn parent_extents(self: &Self, start: u32, end: u32) -> Option<graphene::Rect> { /* ... */ }
  ```

- ```rust
  fn parent_offset(self: &Self, point: &graphene::Point) -> Option<u32> { /* ... */ }
  ```

- ```rust
  fn parent_selection(self: &Self) -> Vec<AccessibleTextRange> { /* ... */ }
  ```

- ```rust
  fn parent_set_caret_position(self: &Self, position: u32) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_set_selection(self: &Self, selection: usize, range: AccessibleTextRange) -> bool { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: AccessibleTextImpl>

## Module `actionable`

Traits intended for implementing the [`Actionable`] interface.

```rust
pub mod actionable { /* ... */ }
```

### Traits

#### Trait `ActionableImpl`

```rust
pub trait ActionableImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::Actionable>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Required Items

###### Required Methods

- `action_name`
- `action_target_value`
- `set_action_name`
- `set_action_target_value`

#### Trait `ActionableImplExt`

```rust
pub trait ActionableImplExt: ActionableImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_action_name(self: &Self) -> Option<GString> { /* ... */ }
  ```

- ```rust
  fn parent_action_target_value(self: &Self) -> Option<Variant> { /* ... */ }
  ```

- ```rust
  fn parent_set_action_name(self: &Self, name: Option<&str>) { /* ... */ }
  ```

- ```rust
  fn parent_set_action_target_value(self: &Self, value: Option<&Variant>) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: ActionableImpl>

## Module `adjustment`

Traits intended for subclassing [`Adjustment`].

```rust
pub mod adjustment { /* ... */ }
```

### Traits

#### Trait `AdjustmentImpl`

```rust
pub trait AdjustmentImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::Adjustment>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn changed(self: &Self) { /* ... */ }
  ```

- ```rust
  fn value_changed(self: &Self) { /* ... */ }
  ```

#### Trait `AdjustmentImplExt`

```rust
pub trait AdjustmentImplExt: AdjustmentImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_changed(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_value_changed(self: &Self) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: AdjustmentImpl>

## Module `application`

Traits intended for subclassing [`Application`].

```rust
pub mod application { /* ... */ }
```

### Traits

#### Trait `GtkApplicationImpl`

```rust
pub trait GtkApplicationImpl: ApplicationImpl + ObjectSubclass<Type: IsA<crate::Application>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn window_added(self: &Self, window: &Window) { /* ... */ }
  ```

- ```rust
  fn window_removed(self: &Self, window: &Window) { /* ... */ }
  ```

- ```rust
  fn save_state(self: &Self, state: &glib::VariantDict) -> bool { /* ... */ }
  ```

- ```rust
  fn restore_state(self: &Self, reason: crate::RestoreReason, state: &glib::Variant) -> bool { /* ... */ }
  ```

- ```rust
  fn restore_window(self: &Self, reason: crate::RestoreReason, state: &glib::Variant) { /* ... */ }
  ```

#### Trait `GtkApplicationImplExt`

```rust
pub trait GtkApplicationImplExt: GtkApplicationImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_window_added(self: &Self, window: &Window) { /* ... */ }
  ```

- ```rust
  fn parent_window_removed(self: &Self, window: &Window) { /* ... */ }
  ```

- ```rust
  fn parent_save_state(self: &Self, state: &glib::VariantDict) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_restore_state(self: &Self, reason: crate::RestoreReason, state: &glib::Variant) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_restore_window(self: &Self, reason: crate::RestoreReason, state: &glib::Variant) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: GtkApplicationImpl>

## Module `application_window`

Traits intended for subclassing [`ApplicationWindow`].

```rust
pub mod application_window { /* ... */ }
```

### Traits

#### Trait `ApplicationWindowImpl`

```rust
pub trait ApplicationWindowImpl: WindowImpl + ObjectSubclass<Type: IsA<crate::ApplicationWindow> + IsA<gio::ActionGroup> + IsA<gio::ActionMap>> + ''static {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn save_state(self: &Self, state: &glib::VariantDict) -> bool { /* ... */ }
  ```

#### Trait `ApplicationWindowImplExt`

```rust
pub trait ApplicationWindowImplExt: ApplicationWindowImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_save_state(self: &Self, state: &glib::VariantDict) -> bool { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: ApplicationWindowImpl>

## Module `box_`

Traits intended for subclassing [`Box`].

```rust
pub mod box_ { /* ... */ }
```

### Traits

#### Trait `BoxImpl`

```rust
pub trait BoxImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::Box>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

## Module `buildable`

Traits intended for implementing the [`Buildable`] interface.

```rust
pub mod buildable { /* ... */ }
```

### Traits

#### Trait `BuildableImpl`

```rust
pub trait BuildableImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::Buildable>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn set_id(self: &Self, id: &str) { /* ... */ }
  ```

- ```rust
  fn id(self: &Self) -> Option<GString> { /* ... */ }
  ```

- ```rust
  fn add_child(self: &Self, builder: &Builder, child: &Object, type_: Option<&str>) { /* ... */ }
  ```

- ```rust
  fn set_buildable_property(self: &Self, builder: &Builder, name: &str, value: &Value) { /* ... */ }
  ```

- ```rust
  fn parser_finished(self: &Self, builder: &Builder) { /* ... */ }
  ```

- ```rust
  fn internal_child(self: &Self, builder: &Builder, name: &str) -> Option<Object> { /* ... */ }
  ```

- ```rust
  fn construct_child(self: &Self, builder: &Builder, name: &str) -> Object { /* ... */ }
  ```

#### Trait `BuildableImplExt`

```rust
pub trait BuildableImplExt: BuildableImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_set_id(self: &Self, id: &str) { /* ... */ }
  ```

- ```rust
  fn parent_id(self: &Self) -> Option<GString> { /* ... */ }
  ```

- ```rust
  fn parent_add_child(self: &Self, builder: &Builder, child: &Object, type_: Option<&str>) { /* ... */ }
  ```

- ```rust
  fn parent_set_buildable_property(self: &Self, builder: &Builder, name: &str, value: &Value) { /* ... */ }
  ```

- ```rust
  fn parent_parser_finished(self: &Self, builder: &Builder) { /* ... */ }
  ```

- ```rust
  fn parent_internal_child(self: &Self, builder: &Builder, name: &str) -> Option<Object> { /* ... */ }
  ```

- ```rust
  fn parent_construct_child(self: &Self, builder: &Builder, name: &str) -> Object { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: BuildableImpl>

## Module `builder_scope`

Traits intended for implementing the [`BuilderScope`] interface.

```rust
pub mod builder_scope { /* ... */ }
```

### Traits

#### Trait `BuilderCScopeImpl`

```rust
pub trait BuilderCScopeImpl: BuilderScopeImpl + ObjectSubclass<Type: IsA<crate::BuilderCScope>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

#### Trait `BuilderScopeImpl`

```rust
pub trait BuilderScopeImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::BuilderScope>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Required Items

###### Required Methods

- `create_closure`

##### Provided Methods

- ```rust
  fn type_from_name(self: &Self, builder: &Builder, type_name: &str) -> glib::Type { /* ... */ }
  ```

- ```rust
  fn type_from_function(self: &Self, builder: &Builder, function_name: &str) -> glib::Type { /* ... */ }
  ```

#### Trait `BuilderScopeImplExt`

```rust
pub trait BuilderScopeImplExt: BuilderScopeImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_type_from_name(self: &Self, builder: &Builder, type_name: &str) -> glib::Type { /* ... */ }
  ```

- ```rust
  fn parent_type_from_function(self: &Self, builder: &Builder, function_name: &str) -> glib::Type { /* ... */ }
  ```

- ```rust
  fn parent_create_closure(self: &Self, builder: &Builder, function_name: &str, flags: BuilderClosureFlags, object: Option<&glib::Object>) -> Result<glib::Closure, glib::Error> { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: BuilderScopeImpl>

## Module `button`

Traits intended for subclassing [`Button`].

```rust
pub mod button { /* ... */ }
```

### Traits

#### Trait `ButtonImpl`

```rust
pub trait ButtonImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::Button> + IsA<crate::Actionable>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn activate(self: &Self) { /* ... */ }
  ```

- ```rust
  fn clicked(self: &Self) { /* ... */ }
  ```

#### Trait `ButtonImplExt`

```rust
pub trait ButtonImplExt: ButtonImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_activate(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_clicked(self: &Self) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: ButtonImpl>

## Module `cell_area`

Traits intended for subclassing [`CellArea`].

```rust
pub mod cell_area { /* ... */ }
```

### Types

#### Struct `CellCallback`

```rust
pub struct CellCallback {
    // Some fields omitted
}
```

##### Fields

| Name | Type | Documentation |
|------|------|---------------|
| *private fields* | ... | *Some fields have been omitted* |

##### Implementations

###### Methods

- ```rust
  pub fn call<R: IsA<CellRenderer>>(self: &Self, cell_renderer: &R) -> glib::ControlFlow { /* ... */ }
  ```

###### Trait Implementations

- **Any**
  - ```rust
    fn type_id(self: &Self) -> TypeId { /* ... */ }
    ```

- **Borrow**
  - ```rust
    fn borrow(self: &Self) -> &T { /* ... */ }
    ```

- **BorrowMut**
  - ```rust
    fn borrow_mut(self: &mut Self) -> &mut T { /* ... */ }
    ```

- **Debug**
  - ```rust
    fn fmt(self: &Self, f: &mut $crate::fmt::Formatter<''_>) -> $crate::fmt::Result { /* ... */ }
    ```

- **Freeze**
- **From**
  - ```rust
    fn from(t: T) -> T { /* ... */ }
    ```
    Returns the argument unchanged.

- **Into**
  - ```rust
    fn into(self: Self) -> U { /* ... */ }
    ```
    Calls `U::from(self)`.

- **RefUnwindSafe**
- **Send**
- **Sync**
- **TryFrom**
  - ```rust
    fn try_from(value: U) -> Result<T, <T as TryFrom<U>>::Error> { /* ... */ }
    ```

- **TryInto**
  - ```rust
    fn try_into(self: Self) -> Result<U, <U as TryFrom<T>>::Error> { /* ... */ }
    ```

- **Unpin**
- **UnsafeUnpin**
- **UnwindSafe**
#### Struct `CellCallbackAllocate`

```rust
pub struct CellCallbackAllocate {
    // Some fields omitted
}
```

##### Fields

| Name | Type | Documentation |
|------|------|---------------|
| *private fields* | ... | *Some fields have been omitted* |

##### Implementations

###### Methods

- ```rust
  pub fn call<R: IsA<CellRenderer>>(self: &Self, cell_renderer: &R, cell_area: &gdk::Rectangle, cell_background: &gdk::Rectangle) -> glib::ControlFlow { /* ... */ }
  ```

###### Trait Implementations

- **Any**
  - ```rust
    fn type_id(self: &Self) -> TypeId { /* ... */ }
    ```

- **Borrow**
  - ```rust
    fn borrow(self: &Self) -> &T { /* ... */ }
    ```

- **BorrowMut**
  - ```rust
    fn borrow_mut(self: &mut Self) -> &mut T { /* ... */ }
    ```

- **Debug**
  - ```rust
    fn fmt(self: &Self, f: &mut $crate::fmt::Formatter<''_>) -> $crate::fmt::Result { /* ... */ }
    ```

- **Freeze**
- **From**
  - ```rust
    fn from(t: T) -> T { /* ... */ }
    ```
    Returns the argument unchanged.

- **Into**
  - ```rust
    fn into(self: Self) -> U { /* ... */ }
    ```
    Calls `U::from(self)`.

- **RefUnwindSafe**
- **Send**
- **Sync**
- **TryFrom**
  - ```rust
    fn try_from(value: U) -> Result<T, <T as TryFrom<U>>::Error> { /* ... */ }
    ```

- **TryInto**
  - ```rust
    fn try_into(self: Self) -> Result<U, <U as TryFrom<T>>::Error> { /* ... */ }
    ```

- **Unpin**
- **UnsafeUnpin**
- **UnwindSafe**
### Traits

#### Trait `CellAreaImpl`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait CellAreaImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::CellArea> + IsA<crate::Buildable> + IsA<crate::CellLayout>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn cell_properties() -> &''static [ParamSpec] { /* ... */ }
  ```

- ```rust
  fn set_cell_property<R: IsA<CellRenderer>>(self: &Self, _renderer: &R, _id: usize, _value: &Value, _pspec: &ParamSpec) { /* ... */ }
  ```

- ```rust
  fn cell_property<R: IsA<CellRenderer>>(self: &Self, _renderer: &R, _id: usize, _pspec: &ParamSpec) -> Value { /* ... */ }
  ```

- ```rust
  fn activate<P: IsA<CellAreaContext>, W: IsA<Widget>>(self: &Self, context: &P, widget: &W, area: &gdk::Rectangle, flags: CellRendererState, edit_only: bool) -> bool { /* ... */ }
  ```

- ```rust
  fn add<R: IsA<CellRenderer>>(self: &Self, renderer: &R) { /* ... */ }
  ```

- ```rust
  fn apply_attributes<M: IsA<TreeModel>>(self: &Self, tree_model: &M, iter: &TreeIter, is_expander: bool, is_expanded: bool) { /* ... */ }
  ```

- ```rust
  fn create_context(self: &Self) -> Option<CellAreaContext> { /* ... */ }
  ```

- ```rust
  fn copy_context<P: IsA<CellAreaContext>>(self: &Self, context: &P) -> Option<CellAreaContext> { /* ... */ }
  ```

- ```rust
  fn event<W: IsA<Widget>, P: IsA<CellAreaContext>>(self: &Self, context: &P, widget: &W, event: &gdk::Event, area: &gdk::Rectangle, flags: CellRendererState) -> bool { /* ... */ }
  ```

- ```rust
  fn foreach(self: &Self, callback: &CellCallback) { /* ... */ }
  ```

- ```rust
  fn foreach_alloc<P: IsA<CellAreaContext>, W: IsA<Widget>>(self: &Self, context: &P, widget: &W, area: &gdk::Rectangle, bg_area: &gdk::Rectangle, callback: &CellCallbackAllocate) { /* ... */ }
  ```

- ```rust
  fn remove<R: IsA<CellRenderer>>(self: &Self, renderer: &R) { /* ... */ }
  ```

- ```rust
  fn is_activatable(self: &Self) -> bool { /* ... */ }
  ```

- ```rust
  fn focus(self: &Self, direction_type: DirectionType) -> bool { /* ... */ }
  ```

- ```rust
  fn request_mode(self: &Self) -> SizeRequestMode { /* ... */ }
  ```

- ```rust
  fn preferred_width<P: IsA<CellAreaContext>, W: IsA<Widget>>(self: &Self, context: &P, widget: &W) -> (i32, i32) { /* ... */ }
  ```

- ```rust
  fn preferred_width_for_height<P: IsA<CellAreaContext>, W: IsA<Widget>>(self: &Self, context: &P, widget: &W, height: i32) -> (i32, i32) { /* ... */ }
  ```

- ```rust
  fn preferred_height<P: IsA<CellAreaContext>, W: IsA<Widget>>(self: &Self, context: &P, widget: &W) -> (i32, i32) { /* ... */ }
  ```

- ```rust
  fn preferred_height_for_width<P: IsA<CellAreaContext>, W: IsA<Widget>>(self: &Self, context: &P, widget: &W, width: i32) -> (i32, i32) { /* ... */ }
  ```

- ```rust
  fn snapshot<P: IsA<CellAreaContext>, W: IsA<Widget>>(self: &Self, context: &P, snapshot: &Snapshot, widget: &W, background_area: &gdk::Rectangle, cellarea: &gdk::Rectangle, flags: CellRendererState, paint_focus: bool) { /* ... */ }
  ```

#### Trait `CellAreaImplExt`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait CellAreaImplExt: CellAreaImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_activate<P: IsA<CellAreaContext>, W: IsA<Widget>>(self: &Self, context: &P, widget: &W, area: &gdk::Rectangle, flags: CellRendererState, edit_only: bool) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_add<R: IsA<CellRenderer>>(self: &Self, renderer: &R) { /* ... */ }
  ```

- ```rust
  fn parent_apply_attributes<M: IsA<TreeModel>>(self: &Self, tree_model: &M, iter: &TreeIter, is_expander: bool, is_expanded: bool) { /* ... */ }
  ```

- ```rust
  fn parent_create_context(self: &Self) -> Option<CellAreaContext> { /* ... */ }
  ```

- ```rust
  fn parent_copy_context<P: IsA<CellAreaContext>>(self: &Self, context: &P) -> Option<CellAreaContext> { /* ... */ }
  ```

- ```rust
  fn parent_event<W: IsA<Widget>, P: IsA<CellAreaContext>>(self: &Self, context: &P, widget: &W, event: &gdk::Event, area: &gdk::Rectangle, flags: CellRendererState) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_foreach(self: &Self, callback: &CellCallback) { /* ... */ }
  ```

- ```rust
  fn parent_foreach_alloc<P: IsA<CellAreaContext>, W: IsA<Widget>>(self: &Self, context: &P, widget: &W, area: &gdk::Rectangle, bg_area: &gdk::Rectangle, callback: &CellCallbackAllocate) { /* ... */ }
  ```

- ```rust
  fn parent_remove<R: IsA<CellRenderer>>(self: &Self, renderer: &R) { /* ... */ }
  ```

- ```rust
  fn parent_is_activatable(self: &Self) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_focus(self: &Self, direction_type: DirectionType) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_request_mode(self: &Self) -> SizeRequestMode { /* ... */ }
  ```

- ```rust
  fn parent_preferred_width<P: IsA<CellAreaContext>, W: IsA<Widget>>(self: &Self, cell_area_context: &P, widget: &W) -> (i32, i32) { /* ... */ }
  ```

- ```rust
  fn parent_preferred_height<P: IsA<CellAreaContext>, W: IsA<Widget>>(self: &Self, cell_area_context: &P, widget: &W) -> (i32, i32) { /* ... */ }
  ```

- ```rust
  fn parent_preferred_width_for_height<P: IsA<CellAreaContext>, W: IsA<Widget>>(self: &Self, cell_area_context: &P, widget: &W, height: i32) -> (i32, i32) { /* ... */ }
  ```

- ```rust
  fn parent_preferred_height_for_width<P: IsA<CellAreaContext>, W: IsA<Widget>>(self: &Self, cell_area_context: &P, widget: &W, width: i32) -> (i32, i32) { /* ... */ }
  ```

- ```rust
  fn parent_snapshot<P: IsA<CellAreaContext>, W: IsA<Widget>>(self: &Self, context: &P, snapshot: &Snapshot, widget: &W, background_area: &gdk::Rectangle, cellarea: &gdk::Rectangle, flags: CellRendererState, paint_focus: bool) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: CellAreaImpl>

#### Trait `CellAreaClassExt`

**Attributes:**

- `Other("#[allow(clippy::missing_safety_doc)]")`

```rust
pub unsafe trait CellAreaClassExt: ClassStruct {
    /* Associated items */
}
```

> This trait is unsafe to implement.

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn find_cell_property(self: &Self, property_name: &str) -> Option<ParamSpec> { /* ... */ }
  ```

- ```rust
  fn list_cell_properties(self: &Self) -> Vec<ParamSpec> { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: ClassStruct>

## Module `cell_area_context`

Traits intended for subclassing [`CellAreaContext`].

```rust
pub mod cell_area_context { /* ... */ }
```

### Traits

#### Trait `CellAreaContextImpl`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait CellAreaContextImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::CellAreaContext>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn reset(self: &Self) { /* ... */ }
  ```

- ```rust
  fn preferred_height_for_width(self: &Self, width: i32) -> (i32, i32) { /* ... */ }
  ```

- ```rust
  fn preferred_width_for_height(self: &Self, height: i32) -> (i32, i32) { /* ... */ }
  ```

- ```rust
  fn allocate(self: &Self, width: i32, height: i32) { /* ... */ }
  ```

#### Trait `CellAreaContextImplExt`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait CellAreaContextImplExt: CellAreaContextImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_reset(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_preferred_height_for_width(self: &Self, width: i32) -> (i32, i32) { /* ... */ }
  ```

- ```rust
  fn parent_preferred_width_for_height(self: &Self, height: i32) -> (i32, i32) { /* ... */ }
  ```

- ```rust
  fn parent_allocate(self: &Self, width: i32, height: i32) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: CellAreaContextImpl>

## Module `cell_editable`

Traits intended for implementing the [`CellEditable`] interface.

```rust
pub mod cell_editable { /* ... */ }
```

### Traits

#### Trait `CellEditableImpl`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait CellEditableImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::CellEditable>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn editing_done(self: &Self) { /* ... */ }
  ```

- ```rust
  fn remove_widget(self: &Self) { /* ... */ }
  ```

- ```rust
  fn start_editing(self: &Self, event: Option<&gdk::Event>) { /* ... */ }
  ```

#### Trait `CellEditableImplExt`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait CellEditableImplExt: CellEditableImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_editing_done(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_remove_widget(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_start_editing(self: &Self, event: Option<&gdk::Event>) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: CellEditableImpl>

## Module `cell_layout`

Traits intended for implementing the [`CellLayout`] interface.

```rust
pub mod cell_layout { /* ... */ }
```

### Types

#### Struct `CellLayoutDataCallback`

```rust
pub struct CellLayoutDataCallback {
    // Some fields omitted
}
```

##### Fields

| Name | Type | Documentation |
|------|------|---------------|
| *private fields* | ... | *Some fields have been omitted* |

##### Implementations

###### Methods

- ```rust
  pub fn call<C: IsA<CellLayout>, R: IsA<CellRenderer>, M: IsA<TreeModel>>(self: &Self, cell_layout: &C, cell_renderer: &R, model: &M, iter: &TreeIter) { /* ... */ }
  ```

###### Trait Implementations

- **Any**
  - ```rust
    fn type_id(self: &Self) -> TypeId { /* ... */ }
    ```

- **Borrow**
  - ```rust
    fn borrow(self: &Self) -> &T { /* ... */ }
    ```

- **BorrowMut**
  - ```rust
    fn borrow_mut(self: &mut Self) -> &mut T { /* ... */ }
    ```

- **Debug**
  - ```rust
    fn fmt(self: &Self, f: &mut $crate::fmt::Formatter<''_>) -> $crate::fmt::Result { /* ... */ }
    ```

- **Drop**
  - ```rust
    fn drop(self: &mut Self) { /* ... */ }
    ```

- **Freeze**
- **From**
  - ```rust
    fn from(t: T) -> T { /* ... */ }
    ```
    Returns the argument unchanged.

- **Into**
  - ```rust
    fn into(self: Self) -> U { /* ... */ }
    ```
    Calls `U::from(self)`.

- **RefUnwindSafe**
- **Send**
- **Sync**
- **TryFrom**
  - ```rust
    fn try_from(value: U) -> Result<T, <T as TryFrom<U>>::Error> { /* ... */ }
    ```

- **TryInto**
  - ```rust
    fn try_into(self: Self) -> Result<U, <U as TryFrom<T>>::Error> { /* ... */ }
    ```

- **Unpin**
- **UnsafeUnpin**
- **UnwindSafe**
### Traits

#### Trait `CellLayoutImpl`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait CellLayoutImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::CellLayout>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn add_attribute<R: IsA<CellRenderer>>(self: &Self, cell: &R, attribute: &str, column: i32) { /* ... */ }
  ```

- ```rust
  fn clear_attributes<R: IsA<CellRenderer>>(self: &Self, cell: &R) { /* ... */ }
  ```

- ```rust
  fn cells(self: &Self) -> Vec<CellRenderer> { /* ... */ }
  ```

- ```rust
  fn set_cell_data_func<R: IsA<CellRenderer>>(self: &Self, cell: &R, callback: Option<CellLayoutDataCallback>) { /* ... */ }
  ```

- ```rust
  fn reorder<R: IsA<CellRenderer>>(self: &Self, cell: &R, position: i32) { /* ... */ }
  ```

- ```rust
  fn clear(self: &Self) { /* ... */ }
  ```

- ```rust
  fn pack_start<R: IsA<CellRenderer>>(self: &Self, cell: &R, expand: bool) { /* ... */ }
  ```

- ```rust
  fn pack_end<R: IsA<CellRenderer>>(self: &Self, cell: &R, expand: bool) { /* ... */ }
  ```

- ```rust
  fn area(self: &Self) -> Option<CellArea> { /* ... */ }
  ```

#### Trait `CellLayoutImplExt`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait CellLayoutImplExt: CellLayoutImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_add_attribute<R: IsA<CellRenderer>>(self: &Self, cell: &R, attribute: &str, column: i32) { /* ... */ }
  ```

- ```rust
  fn parent_clear_attributes<R: IsA<CellRenderer>>(self: &Self, cell: &R) { /* ... */ }
  ```

- ```rust
  fn parent_cells(self: &Self) -> Vec<CellRenderer> { /* ... */ }
  ```

- ```rust
  fn parent_set_cell_data_func<R: IsA<CellRenderer>>(self: &Self, cell: &R, callback: Option<CellLayoutDataCallback>) { /* ... */ }
  ```

- ```rust
  fn parent_reorder<R: IsA<CellRenderer>>(self: &Self, cell: &R, position: i32) { /* ... */ }
  ```

- ```rust
  fn parent_clear(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_pack_start<R: IsA<CellRenderer>>(self: &Self, cell: &R, expand: bool) { /* ... */ }
  ```

- ```rust
  fn parent_pack_end<R: IsA<CellRenderer>>(self: &Self, cell: &R, expand: bool) { /* ... */ }
  ```

- ```rust
  fn parent_area(self: &Self) -> Option<CellArea> { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: CellLayoutImpl>

## Module `cell_renderer`

Traits intended for subclassing [`CellRenderer`].

```rust
pub mod cell_renderer { /* ... */ }
```

### Traits

#### Trait `CellRendererImpl`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait CellRendererImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::CellRenderer>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn activate<P: IsA<Widget>>(self: &Self, event: Option<&gdk::Event>, widget: &P, path: &str, background_area: &gdk::Rectangle, cell_area: &gdk::Rectangle, flags: CellRendererState) -> bool { /* ... */ }
  ```

- ```rust
  fn editing_canceled(self: &Self) { /* ... */ }
  ```

- ```rust
  fn editing_started(self: &Self, editable: &CellEditable, path: &str) { /* ... */ }
  ```

- ```rust
  fn aligned_area<P: IsA<Widget>>(self: &Self, widget: &P, flags: CellRendererState, cell_area: &gdk::Rectangle) -> gdk::Rectangle { /* ... */ }
  ```

- ```rust
  fn preferred_height_for_width<P: IsA<Widget>>(self: &Self, widget: &P, width: i32) -> (i32, i32) { /* ... */ }
  ```

- ```rust
  fn preferred_height<P: IsA<Widget>>(self: &Self, widget: &P) -> (i32, i32) { /* ... */ }
  ```

- ```rust
  fn preferred_width_for_height<P: IsA<Widget>>(self: &Self, widget: &P, height: i32) -> (i32, i32) { /* ... */ }
  ```

- ```rust
  fn preferred_width<P: IsA<Widget>>(self: &Self, widget: &P) -> (i32, i32) { /* ... */ }
  ```

- ```rust
  fn request_mode(self: &Self) -> SizeRequestMode { /* ... */ }
  ```

- ```rust
  fn snapshot<P: IsA<Widget>>(self: &Self, snapshot: &Snapshot, widget: &P, background_area: &gdk::Rectangle, cell_area: &gdk::Rectangle, flags: CellRendererState) { /* ... */ }
  ```

- ```rust
  fn start_editing<P: IsA<Widget>>(self: &Self, event: Option<&gdk::Event>, widget: &P, path: &str, background_area: &gdk::Rectangle, cell_area: &gdk::Rectangle, flags: CellRendererState) -> Option<CellEditable> { /* ... */ }
  ```

#### Trait `CellRendererImplExt`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait CellRendererImplExt: CellRendererImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_request_mode(self: &Self) -> SizeRequestMode { /* ... */ }
  ```

- ```rust
  fn parent_preferred_width<P: IsA<Widget>>(self: &Self, widget: &P) -> (i32, i32) { /* ... */ }
  ```

- ```rust
  fn parent_preferred_width_for_height<P: IsA<Widget>>(self: &Self, widget: &P, height: i32) -> (i32, i32) { /* ... */ }
  ```

- ```rust
  fn parent_preferred_height<P: IsA<Widget>>(self: &Self, widget: &P) -> (i32, i32) { /* ... */ }
  ```

- ```rust
  fn parent_preferred_height_for_width<P: IsA<Widget>>(self: &Self, widget: &P, width: i32) -> (i32, i32) { /* ... */ }
  ```

- ```rust
  fn parent_aligned_area<P: IsA<Widget>>(self: &Self, widget: &P, flags: CellRendererState, cell_area: &gdk::Rectangle) -> gdk::Rectangle { /* ... */ }
  ```

- ```rust
  fn parent_snapshot<P: IsA<Widget>>(self: &Self, snapshot: &Snapshot, widget: &P, background_area: &gdk::Rectangle, cell_area: &gdk::Rectangle, flags: CellRendererState) { /* ... */ }
  ```

- ```rust
  fn parent_activate<P: IsA<Widget>>(self: &Self, event: Option<&gdk::Event>, widget: &P, path: &str, background_area: &gdk::Rectangle, cell_area: &gdk::Rectangle, flags: CellRendererState) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_start_editing<P: IsA<Widget>>(self: &Self, event: Option<&gdk::Event>, widget: &P, path: &str, background_area: &gdk::Rectangle, cell_area: &gdk::Rectangle, flags: CellRendererState) -> Option<CellEditable> { /* ... */ }
  ```

- ```rust
  fn parent_editing_canceled(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_editing_started(self: &Self, editable: &CellEditable, path: &str) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: CellRendererImpl>

## Module `cell_renderer_text`

Traits intended for subclassing [`CellRendererText`].

```rust
pub mod cell_renderer_text { /* ... */ }
```

### Traits

#### Trait `CellRendererTextImpl`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait CellRendererTextImpl: CellRendererImpl + ObjectSubclass<Type: IsA<crate::CellRendererText>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn edited(self: &Self, path: &str, new_text: &str) { /* ... */ }
  ```

#### Trait `CellRendererTextImplExt`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait CellRendererTextImplExt: CellRendererTextImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_edited(self: &Self, path: &str, new_text: &str) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: CellRendererTextImpl>

## Module `check_button`

Traits intended for subclassing [`CheckButton`].

```rust
pub mod check_button { /* ... */ }
```

### Traits

#### Trait `CheckButtonImpl`

```rust
pub trait CheckButtonImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::CheckButton> + IsA<crate::Actionable>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn toggled(self: &Self) { /* ... */ }
  ```

- ```rust
  fn activate(self: &Self) { /* ... */ }
  ```

#### Trait `CheckButtonImplExt`

```rust
pub trait CheckButtonImplExt: CheckButtonImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_toggled(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_activate(self: &Self) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: CheckButtonImpl>

## Module `color_chooser`

Traits intended for implementing the [`ColorChooser`] interface.

```rust
pub mod color_chooser { /* ... */ }
```

### Traits

#### Trait `ColorChooserImpl`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait ColorChooserImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::ColorChooser>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Required Items

###### Required Methods

- `rgba`
- `set_rgba`

##### Provided Methods

- ```rust
  fn add_palette(self: &Self, orientation: Orientation, colors_per_line: i32, colors: &[RGBA]) { /* ... */ }
  ```

- ```rust
  fn color_activated(self: &Self, rgba: RGBA) { /* ... */ }
  ```

#### Trait `ColorChooserImplExt`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait ColorChooserImplExt: ColorChooserImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_add_palette(self: &Self, orientation: Orientation, colors_per_line: i32, colors: &[RGBA]) { /* ... */ }
  ```

- ```rust
  fn parent_color_activated(self: &Self, rgba: RGBA) { /* ... */ }
  ```

- ```rust
  fn parent_rgba(self: &Self) -> RGBA { /* ... */ }
  ```

- ```rust
  fn parent_set_rgba(self: &Self, rgba: RGBA) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: ColorChooserImpl>

## Module `combo_box`

Traits intended for subclassing [`ComboBox`].

```rust
pub mod combo_box { /* ... */ }
```

### Traits

#### Trait `ComboBoxImpl`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait ComboBoxImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::ComboBox> + IsA<crate::CellEditable> + IsA<crate::CellLayout>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn activate(self: &Self) { /* ... */ }
  ```

- ```rust
  fn changed(self: &Self) { /* ... */ }
  ```

- ```rust
  fn format_entry_text(self: &Self, path: &str) -> Option<GString> { /* ... */ }
  ```

#### Trait `ComboBoxImplExt`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait ComboBoxImplExt: ComboBoxImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_activate(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_changed(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_format_entry_text(self: &Self, path: &str) -> Option<GString> { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: ComboBoxImpl>

## Module `dialog`

Traits intended for subclassing [`Dialog`].

```rust
pub mod dialog { /* ... */ }
```

### Traits

#### Trait `DialogImpl`

```rust
pub trait DialogImpl: WindowImpl + ObjectSubclass<Type: IsA<crate::Dialog>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn response(self: &Self, response: ResponseType) { /* ... */ }
  ```

- ```rust
  fn close(self: &Self) { /* ... */ }
  ```

#### Trait `DialogImplExt`

```rust
pub trait DialogImplExt: DialogImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_response(self: &Self, response: ResponseType) { /* ... */ }
  ```

- ```rust
  fn parent_close(self: &Self) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: DialogImpl>

## Module `drawing_area`

Traits intended for subclassing [`DrawingArea`].

```rust
pub mod drawing_area { /* ... */ }
```

### Traits

#### Trait `DrawingAreaImpl`

```rust
pub trait DrawingAreaImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::DrawingArea>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn resize(self: &Self, width: i32, height: i32) { /* ... */ }
  ```

#### Trait `DrawingAreaImplExt`

```rust
pub trait DrawingAreaImplExt: DrawingAreaImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_resize(self: &Self, width: i32, height: i32) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: DrawingAreaImpl>

## Module `editable`

Traits intended for implementing the [`Editable`] interface.

```rust
pub mod editable { /* ... */ }
```

### Traits

#### Trait `EditableImpl`

```rust
pub trait EditableImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::Editable>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn insert_text(self: &Self, text: &str, length: i32, position: &mut i32) { /* ... */ }
  ```

- ```rust
  fn delete_text(self: &Self, start_position: i32, end_position: i32) { /* ... */ }
  ```

- ```rust
  fn changed(self: &Self) { /* ... */ }
  ```

- ```rust
  fn text(self: &Self) -> GString { /* ... */ }
  ```

- ```rust
  fn delegate(self: &Self) -> Option<Editable> { /* ... */ }
  ```

- ```rust
  fn do_insert_text(self: &Self, text: &str, length: i32, position: &mut i32) { /* ... */ }
  ```

- ```rust
  fn do_delete_text(self: &Self, start_position: i32, end_position: i32) { /* ... */ }
  ```

- ```rust
  fn selection_bounds(self: &Self) -> Option<(i32, i32)> { /* ... */ }
  ```

- ```rust
  fn set_selection_bounds(self: &Self, start_position: i32, end_position: i32) { /* ... */ }
  ```

#### Trait `EditableImplExt`

```rust
pub trait EditableImplExt: EditableImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn delegate_get_property(self: &Self, prop_id: usize, pspec: &glib::ParamSpec) -> Option<glib::Value> { /* ... */ }
  ```

- ```rust
  fn delegate_set_property(self: &Self, prop_id: usize, value: &glib::Value, pspec: &glib::ParamSpec) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_insert_text(self: &Self, text: &str, length: i32, position: &mut i32) { /* ... */ }
  ```

- ```rust
  fn parent_delete_text(self: &Self, start_position: i32, end_position: i32) { /* ... */ }
  ```

- ```rust
  fn parent_text(self: &Self) -> GString { /* ... */ }
  ```

- ```rust
  fn parent_delegate(self: &Self) -> Option<Editable> { /* ... */ }
  ```

- ```rust
  fn parent_changed(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_do_insert_text(self: &Self, text: &str, length: i32, position: &mut i32) { /* ... */ }
  ```

- ```rust
  fn parent_do_delete_text(self: &Self, start_position: i32, end_position: i32) { /* ... */ }
  ```

- ```rust
  fn parent_selection_bounds(self: &Self) -> Option<(i32, i32)> { /* ... */ }
  ```

- ```rust
  fn parent_set_selection_bounds(self: &Self, start_position: i32, end_position: i32) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: EditableImpl>

## Module `entry`

Traits intended for subclassing [`Entry`].

```rust
pub mod entry { /* ... */ }
```

### Traits

#### Trait `EntryImpl`

```rust
pub trait EntryImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::Entry> + IsA<crate::CellEditable>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn activate(self: &Self) { /* ... */ }
  ```

#### Trait `EntryImplExt`

```rust
pub trait EntryImplExt: EntryImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_activate(self: &Self) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: EntryImpl>

## Module `entry_buffer`

Traits intended for subclassing [`EntryBuffer`].

```rust
pub mod entry_buffer { /* ... */ }
```

### Traits

#### Trait `EntryBufferImpl`

```rust
pub trait EntryBufferImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::EntryBuffer>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn delete_text(self: &Self, position: u32, n_chars: Option<u32>) -> u32 { /* ... */ }
  ```

- ```rust
  fn deleted_text(self: &Self, position: u32, n_chars: Option<u32>) { /* ... */ }
  ```

- ```rust
  fn length(self: &Self) -> u32 { /* ... */ }
  ```

- ```rust
  fn text(self: &Self) -> GString { /* ... */ }
  ```

- ```rust
  fn insert_text(self: &Self, position: u32, chars: &str) -> u32 { /* ... */ }
  ```

- ```rust
  fn inserted_text(self: &Self, position: u32, chars: &str) { /* ... */ }
  ```

#### Trait `EntryBufferImplExt`

```rust
pub trait EntryBufferImplExt: EntryBufferImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_delete_text(self: &Self, position: u32, n_chars: Option<u32>) -> u32 { /* ... */ }
  ```

- ```rust
  fn parent_deleted_text(self: &Self, position: u32, n_chars: Option<u32>) { /* ... */ }
  ```

- ```rust
  fn parent_length(self: &Self) -> u32 { /* ... */ }
  ```

- ```rust
  fn parent_text(self: &Self) -> GString { /* ... */ }
  ```

- ```rust
  fn parent_insert_text(self: &Self, position: u32, text: &str) -> u32 { /* ... */ }
  ```

- ```rust
  fn parent_inserted_text(self: &Self, position: u32, text: &str) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: EntryBufferImpl>

## Module `filter`

Traits intended for subclassing [`Filter`].

```rust
pub mod filter { /* ... */ }
```

### Traits

#### Trait `FilterImpl`

```rust
pub trait FilterImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::Filter>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn strictness(self: &Self) -> FilterMatch { /* ... */ }
  ```

- ```rust
  fn match_(self: &Self, item: &Object) -> bool { /* ... */ }
  ```

#### Trait `FilterImplExt`

```rust
pub trait FilterImplExt: FilterImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_strictness(self: &Self) -> FilterMatch { /* ... */ }
  ```

- ```rust
  fn parent_match_(self: &Self, item: &Object) -> bool { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: FilterImpl>

## Module `fixed`

Traits intended for subclassing [`Fixed`].

```rust
pub mod fixed { /* ... */ }
```

### Traits

#### Trait `FixedImpl`

```rust
pub trait FixedImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::Fixed>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

## Module `flow_box_child`

Traits intended for subclassing [`FlowBoxChild`].

```rust
pub mod flow_box_child { /* ... */ }
```

### Traits

#### Trait `FlowBoxChildImpl`

```rust
pub trait FlowBoxChildImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::FlowBoxChild>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn activate(self: &Self) { /* ... */ }
  ```

#### Trait `FlowBoxChildImplExt`

```rust
pub trait FlowBoxChildImplExt: FlowBoxChildImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_activate(self: &Self) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: FlowBoxChildImpl>

## Module `font_chooser`

Traits intended for implementing the [`FontChooser`] interface.

```rust
pub mod font_chooser { /* ... */ }
```

### Types

#### Struct `FilterCallback`

```rust
pub struct FilterCallback {
    // Some fields omitted
}
```

##### Fields

| Name | Type | Documentation |
|------|------|---------------|
| *private fields* | ... | *Some fields have been omitted* |

##### Implementations

###### Methods

- ```rust
  pub fn call(self: &Self, font_family: &FontFamily, font_face: &FontFace) -> bool { /* ... */ }
  ```

###### Trait Implementations

- **Any**
  - ```rust
    fn type_id(self: &Self) -> TypeId { /* ... */ }
    ```

- **Borrow**
  - ```rust
    fn borrow(self: &Self) -> &T { /* ... */ }
    ```

- **BorrowMut**
  - ```rust
    fn borrow_mut(self: &mut Self) -> &mut T { /* ... */ }
    ```

- **Debug**
  - ```rust
    fn fmt(self: &Self, f: &mut $crate::fmt::Formatter<''_>) -> $crate::fmt::Result { /* ... */ }
    ```

- **Drop**
  - ```rust
    fn drop(self: &mut Self) { /* ... */ }
    ```

- **Freeze**
- **From**
  - ```rust
    fn from(t: T) -> T { /* ... */ }
    ```
    Returns the argument unchanged.

- **Into**
  - ```rust
    fn into(self: Self) -> U { /* ... */ }
    ```
    Calls `U::from(self)`.

- **RefUnwindSafe**
- **Send**
- **Sync**
- **TryFrom**
  - ```rust
    fn try_from(value: U) -> Result<T, <T as TryFrom<U>>::Error> { /* ... */ }
    ```

- **TryInto**
  - ```rust
    fn try_into(self: Self) -> Result<U, <U as TryFrom<T>>::Error> { /* ... */ }
    ```

- **Unpin**
- **UnsafeUnpin**
- **UnwindSafe**
### Traits

#### Trait `FontChooserImpl`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait FontChooserImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::FontChooser>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn font_family(self: &Self) -> Option<FontFamily> { /* ... */ }
  ```

- ```rust
  fn font_face(self: &Self) -> Option<FontFace> { /* ... */ }
  ```

- ```rust
  fn font_size(self: &Self) -> i32 { /* ... */ }
  ```

- ```rust
  fn set_filter_func(self: &Self, callback: Option<FilterCallback>) { /* ... */ }
  ```

- ```rust
  fn set_font_map<P: IsA<FontMap>>(self: &Self, font_map: Option<&P>) { /* ... */ }
  ```

- ```rust
  fn font_map(self: &Self) -> Option<FontMap> { /* ... */ }
  ```

- ```rust
  fn font_activated(self: &Self, font_name: &str) { /* ... */ }
  ```

#### Trait `FontChooserImplExt`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait FontChooserImplExt: FontChooserImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_font_family(self: &Self) -> Option<FontFamily> { /* ... */ }
  ```

- ```rust
  fn parent_font_face(self: &Self) -> Option<FontFace> { /* ... */ }
  ```

- ```rust
  fn parent_font_size(self: &Self) -> i32 { /* ... */ }
  ```

- ```rust
  fn parent_set_filter_func(self: &Self, callback: Option<FilterCallback>) { /* ... */ }
  ```

- ```rust
  fn parent_set_font_map<P: IsA<FontMap>>(self: &Self, font_map: Option<&P>) { /* ... */ }
  ```

- ```rust
  fn parent_font_map(self: &Self) -> Option<FontMap> { /* ... */ }
  ```

- ```rust
  fn parent_font_activated(self: &Self, font_name: &str) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: FontChooserImpl>

## Module `frame`

Traits intended for subclassing [`Frame`].

```rust
pub mod frame { /* ... */ }
```

### Traits

#### Trait `FrameImpl`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait FrameImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::Frame>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn compute_child_allocation(self: &Self) -> Allocation { /* ... */ }
  ```

#### Trait `FrameImplExt`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait FrameImplExt: FrameImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_compute_child_allocation(self: &Self) -> Allocation { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: FrameImpl>

## Module `gl_area`

Traits intended for subclassing [`GLArea`].

```rust
pub mod gl_area { /* ... */ }
```

### Traits

#### Trait `GLAreaImpl`

**Attributes:**

- `Other("#[allow(clippy::upper_case_acronyms)]")`

```rust
pub trait GLAreaImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::GLArea>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn create_context(self: &Self) -> Option<GLContext> { /* ... */ }
  ```

- ```rust
  fn render(self: &Self, context: &GLContext) -> glib::Propagation { /* ... */ }
  ```

- ```rust
  fn resize(self: &Self, width: i32, height: i32) { /* ... */ }
  ```

#### Trait `GLAreaImplExt`

**Attributes:**

- `Other("#[allow(clippy::upper_case_acronyms)]")`

```rust
pub trait GLAreaImplExt: GLAreaImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_create_context(self: &Self) -> Option<GLContext> { /* ... */ }
  ```

- ```rust
  fn parent_render(self: &Self, context: &GLContext) -> glib::Propagation { /* ... */ }
  ```

- ```rust
  fn parent_resize(self: &Self, width: i32, height: i32) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: GLAreaImpl>

## Module `grid`

Traits intended for subclassing [`Grid`].

```rust
pub mod grid { /* ... */ }
```

### Traits

#### Trait `GridImpl`

```rust
pub trait GridImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::Grid> + IsA<crate::Orientable>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

## Module `im_context`

Traits intended for subclassing [`IMContext`].

```rust
pub mod im_context { /* ... */ }
```

### Traits

#### Trait `IMContextImpl`

**Attributes:**

- `Other("#[allow(clippy::upper_case_acronyms)]")`

```rust
pub trait IMContextImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::IMContext>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn commit(self: &Self, string: &str) { /* ... */ }
  ```

- ```rust
  fn delete_surrounding(self: &Self, offset: i32, n_chars: i32) -> bool { /* ... */ }
  ```

- ```rust
  fn filter_keypress(self: &Self, event: &gdk::Event) -> bool { /* ... */ }
  ```

- ```rust
  fn focus_in(self: &Self) { /* ... */ }
  ```

- ```rust
  fn focus_out(self: &Self) { /* ... */ }
  ```

- ```rust
  fn preedit_string(self: &Self) -> (GString, AttrList, i32) { /* ... */ }
  ```

- ```rust
  fn surrounding(self: &Self) -> Option<(GString, i32)> { /* ... */ }
  ```

- ```rust
  fn preedit_changed(self: &Self) { /* ... */ }
  ```

- ```rust
  fn preedit_end(self: &Self) { /* ... */ }
  ```

- ```rust
  fn preedit_start(self: &Self) { /* ... */ }
  ```

- ```rust
  fn reset(self: &Self) { /* ... */ }
  ```

- ```rust
  fn retrieve_surrounding(self: &Self) -> bool { /* ... */ }
  ```

- ```rust
  fn set_client_widget<P: IsA<Widget>>(self: &Self, widget: Option<&P>) { /* ... */ }
  ```

- ```rust
  fn set_cursor_location(self: &Self, area: &gdk::Rectangle) { /* ... */ }
  ```

- ```rust
  fn set_surrounding(self: &Self, text: &str, cursor_index: i32) { /* ... */ }
  ```

- ```rust
  fn set_use_preedit(self: &Self, use_preedit: bool) { /* ... */ }
  ```

- ```rust
  fn activate_osk(self: &Self) { /* ... */ }
  ```

- ```rust
  fn activate_osk_with_event(self: &Self, event: Option<&gdk::Event>) -> bool { /* ... */ }
  ```

- ```rust
  fn invalid_composition(self: &Self, string: &str) -> bool { /* ... */ }
  ```

#### Trait `IMContextImplExt`

**Attributes:**

- `Other("#[allow(clippy::upper_case_acronyms)]")`

```rust
pub trait IMContextImplExt: IMContextImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_commit(self: &Self, string: &str) { /* ... */ }
  ```

- ```rust
  fn parent_delete_surrounding(self: &Self, offset: i32, n_chars: i32) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_filter_keypress(self: &Self, event: &gdk::Event) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_focus_in(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_focus_out(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_surrounding(self: &Self) -> Option<(GString, i32)> { /* ... */ }
  ```

- ```rust
  fn parent_preedit_string(self: &Self) -> (GString, AttrList, i32) { /* ... */ }
  ```

- ```rust
  fn parent_preedit_changed(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_preedit_end(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_preedit_start(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_reset(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_retrieve_surrounding(self: &Self) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_set_client_widget<P: IsA<Widget>>(self: &Self, widget: Option<&P>) { /* ... */ }
  ```

- ```rust
  fn parent_set_cursor_location(self: &Self, area: &gdk::Rectangle) { /* ... */ }
  ```

- ```rust
  fn parent_set_surrounding(self: &Self, text: &str, cursor_index: i32) { /* ... */ }
  ```

- ```rust
  fn parent_set_use_preedit(self: &Self, use_preedit: bool) { /* ... */ }
  ```

- ```rust
  fn parent_activate_osk(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_activate_osk_with_event(self: &Self, event: Option<&gdk::Event>) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_invalid_composition(self: &Self, string: &str) -> bool { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: IMContextImpl>

## Module `layout_child`

Traits intended for subclassing [`LayoutChild`].

```rust
pub mod layout_child { /* ... */ }
```

### Traits

#### Trait `LayoutChildImpl`

```rust
pub trait LayoutChildImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::LayoutChild>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

## Module `layout_manager`

Traits intended for subclassing [`LayoutManager`].

```rust
pub mod layout_manager { /* ... */ }
```

### Traits

#### Trait `LayoutManagerImpl`

```rust
pub trait LayoutManagerImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::LayoutManager>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn allocate(self: &Self, widget: &Widget, width: i32, height: i32, baseline: i32) { /* ... */ }
  ```

- ```rust
  fn create_layout_child(self: &Self, widget: &Widget, for_child: &Widget) -> LayoutChild { /* ... */ }
  ```

- ```rust
  fn layout_child_type() -> Option<glib::Type> { /* ... */ }
  ```
  Only set if the child implemented LayoutChildImpl

- ```rust
  fn request_mode(self: &Self, widget: &Widget) -> SizeRequestMode { /* ... */ }
  ```

- ```rust
  fn measure(self: &Self, widget: &Widget, orientation: Orientation, for_size: i32) -> (i32, i32, i32, i32) { /* ... */ }
  ```

- ```rust
  fn root(self: &Self) { /* ... */ }
  ```

- ```rust
  fn unroot(self: &Self) { /* ... */ }
  ```

#### Trait `LayoutManagerImplExt`

```rust
pub trait LayoutManagerImplExt: LayoutManagerImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_allocate(self: &Self, widget: &Widget, width: i32, height: i32, baseline: i32) { /* ... */ }
  ```

- ```rust
  fn parent_create_layout_child(self: &Self, widget: &Widget, for_child: &Widget) -> LayoutChild { /* ... */ }
  ```

- ```rust
  fn parent_request_mode(self: &Self, widget: &Widget) -> SizeRequestMode { /* ... */ }
  ```

- ```rust
  fn parent_measure(self: &Self, widget: &Widget, orientation: Orientation, for_size: i32) -> (i32, i32, i32, i32) { /* ... */ }
  ```

- ```rust
  fn parent_root(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_unroot(self: &Self) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: LayoutManagerImpl>

## Module `list_box_row`

Traits intended for subclassing [`ListBoxRow`].

```rust
pub mod list_box_row { /* ... */ }
```

### Traits

#### Trait `ListBoxRowImpl`

```rust
pub trait ListBoxRowImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::ListBoxRow> + IsA<crate::Actionable>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn activate(self: &Self) { /* ... */ }
  ```

#### Trait `ListBoxRowImplExt`

```rust
pub trait ListBoxRowImplExt: ListBoxRowImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_activate(self: &Self) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: ListBoxRowImpl>

## Module `media_file`

Traits intended for subclassing [`MediaFile`].

```rust
pub mod media_file { /* ... */ }
```

### Traits

#### Trait `MediaFileImpl`

```rust
pub trait MediaFileImpl: MediaStreamImpl + ObjectSubclass<Type: IsA<crate::MediaFile> + IsA<gdk::Paintable>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn close(self: &Self) { /* ... */ }
  ```

- ```rust
  fn open(self: &Self) { /* ... */ }
  ```

#### Trait `MediaFileImplExt`

```rust
pub trait MediaFileImplExt: MediaFileImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_close(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_open(self: &Self) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: MediaFileImpl>

## Module `media_stream`

Traits intended for subclassing [`MediaStream`].

```rust
pub mod media_stream { /* ... */ }
```

### Traits

#### Trait `MediaStreamImpl`

```rust
pub trait MediaStreamImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::MediaStream> + IsA<gdk::Paintable>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn pause(self: &Self) { /* ... */ }
  ```

- ```rust
  fn play(self: &Self) -> bool { /* ... */ }
  ```

- ```rust
  fn realize(self: &Self, surface: gdk::Surface) { /* ... */ }
  ```

- ```rust
  fn seek(self: &Self, timestamp: i64) { /* ... */ }
  ```

- ```rust
  fn unrealize(self: &Self, surface: gdk::Surface) { /* ... */ }
  ```

- ```rust
  fn update_audio(self: &Self, muted: bool, volume: f64) { /* ... */ }
  ```

#### Trait `MediaStreamImplExt`

```rust
pub trait MediaStreamImplExt: MediaStreamImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_pause(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_play(self: &Self) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_realize(self: &Self, surface: gdk::Surface) { /* ... */ }
  ```

- ```rust
  fn parent_seek(self: &Self, timestamp: i64) { /* ... */ }
  ```

- ```rust
  fn parent_unrealize(self: &Self, surface: gdk::Surface) { /* ... */ }
  ```

- ```rust
  fn parent_update_audio(self: &Self, muted: bool, volume: f64) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: MediaStreamImpl>

## Module `native_dialog`

Traits intended for subclassing [`NativeDialog`].

```rust
pub mod native_dialog { /* ... */ }
```

### Traits

#### Trait `NativeDialogImpl`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait NativeDialogImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::NativeDialog>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn response(self: &Self, response: ResponseType) { /* ... */ }
  ```

- ```rust
  fn show(self: &Self) { /* ... */ }
  ```

- ```rust
  fn hide(self: &Self) { /* ... */ }
  ```

#### Trait `NativeDialogImplExt`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait NativeDialogImplExt: NativeDialogImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_response(self: &Self, response: ResponseType) { /* ... */ }
  ```

- ```rust
  fn parent_show(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_hide(self: &Self) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: NativeDialogImpl>

## Module `orientable`

Traits intended for implementing the [`Orientable`] interface.

```rust
pub mod orientable { /* ... */ }
```

### Traits

#### Trait `OrientableImpl`

```rust
pub trait OrientableImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::Orientable>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

## Module `popover`

Traits intended for subclassing [`Popover`].

```rust
pub mod popover { /* ... */ }
```

### Traits

#### Trait `PopoverImpl`

```rust
pub trait PopoverImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::Popover> + IsA<crate::Native> + IsA<crate::ShortcutManager>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn activate_default(self: &Self) { /* ... */ }
  ```

- ```rust
  fn closed(self: &Self) { /* ... */ }
  ```

#### Trait `PopoverImplExt`

```rust
pub trait PopoverImplExt: PopoverImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_activate_default(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_closed(self: &Self) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: PopoverImpl>

## Module `print_operation`

Traits intended for subclassing [`PrintOperation`].

```rust
pub mod print_operation { /* ... */ }
```

### Traits

#### Trait `PrintOperationImpl`

```rust
pub trait PrintOperationImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::PrintOperation> + IsA<crate::PrintOperationPreview>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn begin_print(self: &Self, context: &PrintContext) { /* ... */ }
  ```

- ```rust
  fn create_custom_widget(self: &Self) -> Option<Widget> { /* ... */ }
  ```

- ```rust
  fn custom_widget_apply(self: &Self, widget: &Widget) { /* ... */ }
  ```

- ```rust
  fn done(self: &Self, result: PrintOperationResult) { /* ... */ }
  ```

- ```rust
  fn draw_page(self: &Self, context: &PrintContext, page_nr: i32) { /* ... */ }
  ```

- ```rust
  fn end_print(self: &Self, context: &PrintContext) { /* ... */ }
  ```

- ```rust
  fn paginate(self: &Self, context: &PrintContext) -> bool { /* ... */ }
  ```

- ```rust
  fn preview(self: &Self, preview: &PrintOperationPreview, context: &PrintContext, parent: Option<&Window>) -> bool { /* ... */ }
  ```

- ```rust
  fn request_page_setup(self: &Self, context: &PrintContext, page_nr: i32, setup: &PageSetup) { /* ... */ }
  ```

- ```rust
  fn status_changed(self: &Self) { /* ... */ }
  ```

- ```rust
  fn update_custom_widget(self: &Self, widget: &Widget, setup: &PageSetup, settings: &PrintSettings) { /* ... */ }
  ```

#### Trait `PrintOperationImplExt`

```rust
pub trait PrintOperationImplExt: PrintOperationImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_begin_print(self: &Self, context: &PrintContext) { /* ... */ }
  ```

- ```rust
  fn parent_create_custom_widget(self: &Self) -> Option<Widget> { /* ... */ }
  ```

- ```rust
  fn parent_custom_widget_apply(self: &Self, widget: &Widget) { /* ... */ }
  ```

- ```rust
  fn parent_done(self: &Self, result: PrintOperationResult) { /* ... */ }
  ```

- ```rust
  fn parent_draw_page(self: &Self, context: &PrintContext, page_nr: i32) { /* ... */ }
  ```

- ```rust
  fn parent_end_print(self: &Self, context: &PrintContext) { /* ... */ }
  ```

- ```rust
  fn parent_paginate(self: &Self, context: &PrintContext) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_preview(self: &Self, preview: &PrintOperationPreview, context: &PrintContext, parent: Option<&Window>) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_request_page_setup(self: &Self, context: &PrintContext, page_nr: i32, setup: &PageSetup) { /* ... */ }
  ```

- ```rust
  fn parent_status_changed(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_update_custom_widget(self: &Self, widget: &Widget, setup: &PageSetup, settings: &PrintSettings) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: PrintOperationImpl>

## Module `print_operation_preview`

Traits intended for implementing the [`PrintOperationPreview`] interface.

```rust
pub mod print_operation_preview { /* ... */ }
```

### Traits

#### Trait `PrintOperationPreviewImpl`

```rust
pub trait PrintOperationPreviewImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::PrintOperationPreview>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Required Items

###### Required Methods

- `render_page`
- `is_selected`
- `end_preview`

##### Provided Methods

- ```rust
  fn ready(self: &Self, context: &PrintContext) { /* ... */ }
  ```

- ```rust
  fn got_page_size(self: &Self, context: &PrintContext, page_setup: &PageSetup) { /* ... */ }
  ```

#### Trait `PrintOperationPreviewImplExt`

```rust
pub trait PrintOperationPreviewImplExt: PrintOperationPreviewImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_ready(self: &Self, context: &PrintContext) { /* ... */ }
  ```

- ```rust
  fn parent_got_page_size(self: &Self, context: &PrintContext, page_setup: &PageSetup) { /* ... */ }
  ```

- ```rust
  fn parent_render_page(self: &Self, page_nr: i32) { /* ... */ }
  ```

- ```rust
  fn parent_is_selected(self: &Self, page_nr: i32) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_end_preview(self: &Self) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: PrintOperationPreviewImpl>

## Module `range`

Traits intended for subclassing [`Range`].

```rust
pub mod range { /* ... */ }
```

### Traits

#### Trait `RangeImpl`

```rust
pub trait RangeImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::Range> + IsA<crate::Orientable>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn adjust_bounds(self: &Self, new_value: f64) { /* ... */ }
  ```

- ```rust
  fn change_value(self: &Self, scroll_type: ScrollType, new_value: f64) -> glib::Propagation { /* ... */ }
  ```

- ```rust
  fn range_border(self: &Self) -> Border { /* ... */ }
  ```

- ```rust
  fn move_slider(self: &Self, scroll_type: ScrollType) { /* ... */ }
  ```

- ```rust
  fn value_changed(self: &Self) { /* ... */ }
  ```

#### Trait `RangeImplExt`

```rust
pub trait RangeImplExt: RangeImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_adjust_bounds(self: &Self, new_value: f64) { /* ... */ }
  ```

- ```rust
  fn parent_change_value(self: &Self, scroll_type: ScrollType, new_value: f64) -> glib::Propagation { /* ... */ }
  ```

- ```rust
  fn parent_range_border(self: &Self) -> Border { /* ... */ }
  ```

- ```rust
  fn parent_move_slider(self: &Self, scroll_type: ScrollType) { /* ... */ }
  ```

- ```rust
  fn parent_value_changed(self: &Self) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: RangeImpl>

## Module `recent_manager`

Traits intended for subclassing [`RecentManager`].

```rust
pub mod recent_manager { /* ... */ }
```

### Traits

#### Trait `RecentManagerImpl`

```rust
pub trait RecentManagerImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::RecentManager>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn changed(self: &Self) { /* ... */ }
  ```

#### Trait `RecentManagerImplExt`

```rust
pub trait RecentManagerImplExt: RecentManagerImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_changed(self: &Self) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: RecentManagerImpl>

## Module `scale`

Traits intended for subclassing [`Scale`].

```rust
pub mod scale { /* ... */ }
```

### Traits

#### Trait `ScaleImpl`

```rust
pub trait ScaleImpl: RangeImpl + ObjectSubclass<Type: IsA<crate::Scale> + IsA<crate::Orientable>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn layout_offsets(self: &Self) -> (i32, i32) { /* ... */ }
  ```

#### Trait `ScaleImplExt`

```rust
pub trait ScaleImplExt: ScaleImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_layout_offsets(self: &Self) -> (i32, i32) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: ScaleImpl>

## Module `scale_button`

Traits intended for subclassing [`ScaleButton`].

```rust
pub mod scale_button { /* ... */ }
```

### Traits

#### Trait `ScaleButtonImpl`

```rust
pub trait ScaleButtonImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::ScaleButton> + IsA<crate::Orientable>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn value_changed(self: &Self, new_value: f64) { /* ... */ }
  ```

#### Trait `ScaleButtonImplExt`

```rust
pub trait ScaleButtonImplExt: ScaleButtonImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_value_changed(self: &Self, new_value: f64) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: ScaleButtonImpl>

## Module `scrollable`

Traits intended for implementing the [`Scrollable`] interface.

```rust
pub mod scrollable { /* ... */ }
```

### Traits

#### Trait `ScrollableImpl`

```rust
pub trait ScrollableImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::Scrollable>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn border(self: &Self) -> Option<Border> { /* ... */ }
  ```

#### Trait `ScrollableImplExt`

```rust
pub trait ScrollableImplExt: ScrollableImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_border(self: &Self) -> Option<Border> { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: ScrollableImpl>

## Module `section_model`

**Attributes:**

- `Other("#[attr = CfgTrace([NameValue { name: \"feature\", value: Some(\"v4_12\"), span: src/subclass/mod.rs:71:7: 71:24 (#0) }])]")`
- `Other("#[attr = CfgAttrTrace]")`
- `Other("#[doc(cfg(feature = \"v4_12\"))]")`

Traits intended for implementing the [`SectionModel`] interface.

```rust
pub mod section_model { /* ... */ }
```

### Traits

#### Trait `SectionModelImpl`

```rust
pub trait SectionModelImpl: ListModelImpl + ObjectSubclass<Type: IsA<crate::SectionModel>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn section(self: &Self, position: u32) -> (u32, u32) { /* ... */ }
  ```

#### Trait `SectionModelImplExt`

```rust
pub trait SectionModelImplExt: SectionModelImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_section(self: &Self, position: u32) -> (u32, u32) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: SectionModelImpl>

## Module `selection_model`

Traits intended for implementing the [`SelectionModel`] interface.

```rust
pub mod selection_model { /* ... */ }
```

### Traits

#### Trait `SelectionModelImpl`

```rust
pub trait SelectionModelImpl: ListModelImpl + ObjectSubclass<Type: IsA<crate::SelectionModel>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn selection_in_range(self: &Self, position: u32, n_items: u32) -> Bitset { /* ... */ }
  ```

- ```rust
  fn is_selected(self: &Self, position: u32) -> bool { /* ... */ }
  ```

- ```rust
  fn select_all(self: &Self) -> bool { /* ... */ }
  ```

- ```rust
  fn select_item(self: &Self, position: u32, unselect_rest: bool) -> bool { /* ... */ }
  ```

- ```rust
  fn select_range(self: &Self, position: u32, n_items: u32, unselect_rest: bool) -> bool { /* ... */ }
  ```

- ```rust
  fn set_selection(self: &Self, selected: &Bitset, mask: &Bitset) -> bool { /* ... */ }
  ```

- ```rust
  fn unselect_all(self: &Self) -> bool { /* ... */ }
  ```

- ```rust
  fn unselect_item(self: &Self, position: u32) -> bool { /* ... */ }
  ```

- ```rust
  fn unselect_range(self: &Self, position: u32, n_items: u32) -> bool { /* ... */ }
  ```

#### Trait `SelectionModelImplExt`

```rust
pub trait SelectionModelImplExt: SelectionModelImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_selection_in_range(self: &Self, position: u32, n_items: u32) -> Bitset { /* ... */ }
  ```

- ```rust
  fn parent_is_selected(self: &Self, position: u32) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_select_all(self: &Self) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_select_item(self: &Self, position: u32, unselect_rest: bool) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_select_range(self: &Self, position: u32, n_items: u32, unselect_rest: bool) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_set_selection(self: &Self, selected: &Bitset, mask: &Bitset) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_unselect_all(self: &Self) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_unselect_item(self: &Self, position: u32) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_unselect_range(self: &Self, position: u32, n_items: u32) -> bool { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: SelectionModelImpl>

## Module `shortcut_manager`

Traits intended for implementing the [`ShortcutManager`] interface.

```rust
pub mod shortcut_manager { /* ... */ }
```

### Traits

#### Trait `ShortcutManagerImpl`

```rust
pub trait ShortcutManagerImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::ShortcutManager>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn add_controller(self: &Self, controller: &ShortcutController) { /* ... */ }
  ```

- ```rust
  fn remove_controller(self: &Self, controller: &ShortcutController) { /* ... */ }
  ```

#### Trait `ShortcutManagerImplExt`

```rust
pub trait ShortcutManagerImplExt: ShortcutManagerImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_add_controller(self: &Self, controller: &ShortcutController) { /* ... */ }
  ```

- ```rust
  fn parent_remove_controller(self: &Self, controller: &ShortcutController) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: ShortcutManagerImpl>

## Module `sorter`

Traits intended for subclassing [`Sorter`].

```rust
pub mod sorter { /* ... */ }
```

### Traits

#### Trait `SorterImpl`

```rust
pub trait SorterImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::Sorter>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn compare(self: &Self, item1: &Object, item2: &Object) -> Ordering { /* ... */ }
  ```

- ```rust
  fn order(self: &Self) -> SorterOrder { /* ... */ }
  ```

#### Trait `SorterImplExt`

```rust
pub trait SorterImplExt: SorterImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_compare(self: &Self, item1: &Object, item2: &Object) -> Ordering { /* ... */ }
  ```

- ```rust
  fn parent_order(self: &Self) -> SorterOrder { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: SorterImpl>

## Module `style_context`

Traits intended for subclassing [`StyleContext`].

```rust
pub mod style_context { /* ... */ }
```

### Traits

#### Trait `StyleContextImpl`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait StyleContextImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::StyleContext>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn changed(self: &Self) { /* ... */ }
  ```

#### Trait `StyleContextImplExt`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait StyleContextImplExt: StyleContextImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_changed(self: &Self) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: StyleContextImpl>

## Module `symbolic_paintable`

**Attributes:**

- `Other("#[attr = CfgTrace([NameValue { name: \"feature\", value: Some(\"v4_6\"), span: src/subclass/mod.rs:78:7: 78:23 (#0) }])]")`
- `Other("#[attr = CfgAttrTrace]")`
- `Other("#[doc(cfg(feature = \"v4_6\"))]")`

Traits intended for implementing the [`SymbolicPaintable`] interface.

```rust
pub mod symbolic_paintable { /* ... */ }
```

### Traits

#### Trait `SymbolicPaintableImpl`

```rust
pub trait SymbolicPaintableImpl: PaintableImpl + ObjectSubclass<Type: IsA<crate::SymbolicPaintable>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn snapshot_symbolic(self: &Self, snapshot: &gdk::Snapshot, width: f64, height: f64, colors: &[gdk::RGBA]) { /* ... */ }
  ```

- ```rust
  fn snapshot_with_weight(self: &Self, snapshot: &gdk::Snapshot, width: f64, height: f64, colors: &[gdk::RGBA], weight: f64) { /* ... */ }
  ```

#### Trait `SymbolicPaintableImplExt`

```rust
pub trait SymbolicPaintableImplExt: SymbolicPaintableImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_snapshot_symbolic(self: &Self, snapshot: &gdk::Snapshot, width: f64, height: f64, colors: &[gdk::RGBA]) { /* ... */ }
  ```

- ```rust
  fn parent_snapshot_with_weight(self: &Self, snapshot: &gdk::Snapshot, width: f64, height: f64, colors: &[gdk::RGBA], weight: f64) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: SymbolicPaintableImpl>

## Module `text_buffer`

Traits intended for subclassing [`TextBuffer`].

```rust
pub mod text_buffer { /* ... */ }
```

### Traits

#### Trait `TextBufferImpl`

```rust
pub trait TextBufferImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::TextBuffer>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn apply_tag(self: &Self, tag: &TextTag, start: &TextIter, end: &TextIter) { /* ... */ }
  ```

- ```rust
  fn begin_user_action(self: &Self) { /* ... */ }
  ```

- ```rust
  fn changed(self: &Self) { /* ... */ }
  ```

- ```rust
  fn delete_range(self: &Self, start: &mut TextIter, end: &mut TextIter) { /* ... */ }
  ```

- ```rust
  fn end_user_action(self: &Self) { /* ... */ }
  ```

- ```rust
  fn insert_child_anchor(self: &Self, iter: &mut TextIter, anchor: &TextChildAnchor) { /* ... */ }
  ```

- ```rust
  fn insert_paintable(self: &Self, iter: &mut TextIter, paintable: &gdk::Paintable) { /* ... */ }
  ```

- ```rust
  fn insert_text(self: &Self, iter: &mut TextIter, new_text: &str) { /* ... */ }
  ```

- ```rust
  fn mark_deleted(self: &Self, mark: &TextMark) { /* ... */ }
  ```

- ```rust
  fn mark_set(self: &Self, location: &TextIter, mark: &TextMark) { /* ... */ }
  ```

- ```rust
  fn modified_changed(self: &Self) { /* ... */ }
  ```

- ```rust
  fn paste_done(self: &Self, clipboard: &gdk::Clipboard) { /* ... */ }
  ```

- ```rust
  fn redo(self: &Self) { /* ... */ }
  ```

- ```rust
  fn remove_tag(self: &Self, tag: &TextTag, start: &TextIter, end: &TextIter) { /* ... */ }
  ```

- ```rust
  fn undo(self: &Self) { /* ... */ }
  ```

#### Trait `TextBufferImplExt`

```rust
pub trait TextBufferImplExt: TextBufferImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_apply_tag(self: &Self, tag: &TextTag, start: &TextIter, end: &TextIter) { /* ... */ }
  ```

- ```rust
  fn parent_begin_user_action(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_changed(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_delete_range(self: &Self, start: &mut TextIter, end: &mut TextIter) { /* ... */ }
  ```

- ```rust
  fn parent_end_user_action(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_insert_child_anchor(self: &Self, iter: &mut TextIter, anchor: &TextChildAnchor) { /* ... */ }
  ```

- ```rust
  fn parent_insert_paintable(self: &Self, iter: &mut TextIter, paintable: &gdk::Paintable) { /* ... */ }
  ```

- ```rust
  fn parent_insert_text(self: &Self, iter: &mut TextIter, new_text: &str) { /* ... */ }
  ```

- ```rust
  fn parent_mark_deleted(self: &Self, mark: &TextMark) { /* ... */ }
  ```

- ```rust
  fn parent_mark_set(self: &Self, location: &TextIter, mark: &TextMark) { /* ... */ }
  ```

- ```rust
  fn parent_modified_changed(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_paste_done(self: &Self, clipboard: &gdk::Clipboard) { /* ... */ }
  ```

- ```rust
  fn parent_redo(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_remove_tag(self: &Self, tag: &TextTag, start: &TextIter, end: &TextIter) { /* ... */ }
  ```

- ```rust
  fn parent_undo(self: &Self) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: TextBufferImpl>

## Module `text_view`

Traits intended for subclassing [`TextView`].

```rust
pub mod text_view { /* ... */ }
```

### Traits

#### Trait `TextViewImpl`

```rust
pub trait TextViewImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::TextView> + IsA<crate::Scrollable>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn backspace(self: &Self) { /* ... */ }
  ```

- ```rust
  fn copy_clipboard(self: &Self) { /* ... */ }
  ```

- ```rust
  fn cut_clipboard(self: &Self) { /* ... */ }
  ```

- ```rust
  fn delete_from_cursor(self: &Self, type_: DeleteType, count: i32) { /* ... */ }
  ```

- ```rust
  fn extend_selection(self: &Self, granularity: TextExtendSelection, location: &TextIter, start: &mut TextIter, end: &mut TextIter) -> glib::ControlFlow { /* ... */ }
  ```

- ```rust
  fn insert_at_cursor(self: &Self, text: &str) { /* ... */ }
  ```

- ```rust
  fn insert_emoji(self: &Self) { /* ... */ }
  ```

- ```rust
  fn move_cursor(self: &Self, step: MovementStep, count: i32, extend_selection: bool) { /* ... */ }
  ```

- ```rust
  fn paste_clipboard(self: &Self) { /* ... */ }
  ```

- ```rust
  fn set_anchor(self: &Self) { /* ... */ }
  ```

- ```rust
  fn snapshot_layer(self: &Self, layer: TextViewLayer, snapshot: Snapshot) { /* ... */ }
  ```

- ```rust
  fn toggle_overwrite(self: &Self) { /* ... */ }
  ```

#### Trait `TextViewImplExt`

```rust
pub trait TextViewImplExt: TextViewImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_backspace(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_copy_clipboard(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_cut_clipboard(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_delete_from_cursor(self: &Self, type_: DeleteType, count: i32) { /* ... */ }
  ```

- ```rust
  fn parent_extend_selection(self: &Self, granularity: TextExtendSelection, location: &TextIter, start: &mut TextIter, end: &mut TextIter) -> glib::ControlFlow { /* ... */ }
  ```

- ```rust
  fn parent_insert_at_cursor(self: &Self, text: &str) { /* ... */ }
  ```

- ```rust
  fn parent_insert_emoji(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_move_cursor(self: &Self, step: MovementStep, count: i32, extend_selection: bool) { /* ... */ }
  ```

- ```rust
  fn parent_paste_clipboard(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_set_anchor(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_snapshot_layer(self: &Self, layer: TextViewLayer, snapshot: Snapshot) { /* ... */ }
  ```

- ```rust
  fn parent_toggle_overwrite(self: &Self) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: TextViewImpl>

## Module `toggle_button`

Traits intended for subclassing [`ToggleButton`].

```rust
pub mod toggle_button { /* ... */ }
```

### Traits

#### Trait `ToggleButtonImpl`

```rust
pub trait ToggleButtonImpl: ButtonImpl + ObjectSubclass<Type: IsA<crate::ToggleButton> + IsA<crate::Actionable>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn toggled(self: &Self) { /* ... */ }
  ```

#### Trait `ToggleButtonImplExt`

```rust
pub trait ToggleButtonImplExt: ToggleButtonImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_toggled(self: &Self) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: ToggleButtonImpl>

## Module `tree_drag_dest`

Traits intended for implementing the [`TreeDragDest`] interface.

```rust
pub mod tree_drag_dest { /* ... */ }
```

### Traits

#### Trait `TreeDragDestImpl`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait TreeDragDestImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::TreeDragDest>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Required Items

###### Required Methods

- `drag_data_received`
- `row_drop_possible`

#### Trait `TreeDragDestImplExt`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait TreeDragDestImplExt: TreeDragDestImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_drag_data_received(self: &Self, dest: &TreePath, value: Value) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_row_drop_possible(self: &Self, dest: &TreePath, value: Value) -> bool { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: TreeDragDestImpl>

## Module `tree_drag_source`

Traits intended for implementing the [`TreeDragSource`] interface.

```rust
pub mod tree_drag_source { /* ... */ }
```

### Traits

#### Trait `TreeDragSourceImpl`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait TreeDragSourceImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::TreeDragSource>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Required Items

###### Required Methods

- `drag_data_get`
- `drag_data_delete`

##### Provided Methods

- ```rust
  fn row_draggable(self: &Self, path: &TreePath) -> bool { /* ... */ }
  ```

#### Trait `TreeDragSourceImplExt`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait TreeDragSourceImplExt: TreeDragSourceImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_row_draggable(self: &Self, path: &TreePath) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_drag_data_get(self: &Self, path: &TreePath) -> gdk::ContentProvider { /* ... */ }
  ```

- ```rust
  fn parent_drag_data_delete(self: &Self, path: &TreePath) -> bool { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: TreeDragSourceImpl>

## Module `tree_model_filter`

Traits intended for subclassing [`TreeModelFilter`].

```rust
pub mod tree_model_filter { /* ... */ }
```

### Traits

#### Trait `TreeModelFilterImpl`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait TreeModelFilterImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::TreeModelFilter>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn visible<M: IsA<TreeModel>>(self: &Self, child_model: &M, iter: &TreeIter) -> bool { /* ... */ }
  ```

- ```rust
  fn modify<M: IsA<TreeModel>>(self: &Self, child_model: &M, iter: &TreeIter, value: Value, column: i32) { /* ... */ }
  ```

#### Trait `TreeModelFilterImplExt`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait TreeModelFilterImplExt: TreeModelFilterImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_visible<M: IsA<TreeModel>>(self: &Self, child_model: &M, iter: &TreeIter) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_modify<M: IsA<TreeModel>>(self: &Self, child_model: &M, iter: &TreeIter, value: Value, index: i32) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: TreeModelFilterImpl>

## Module `tree_view`

Traits intended for subclassing [`TreeView`].

```rust
pub mod tree_view { /* ... */ }
```

### Traits

#### Trait `TreeViewImpl`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait TreeViewImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::TreeView> + IsA<crate::Scrollable>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn columns_changed(self: &Self) { /* ... */ }
  ```

- ```rust
  fn cursor_changed(self: &Self) { /* ... */ }
  ```

- ```rust
  fn expand_collapse_cursor_row(self: &Self, logical: bool, expand: bool, open_all: bool) -> bool { /* ... */ }
  ```

- ```rust
  fn move_cursor(self: &Self, step: MovementStep, count: i32, expand: bool, modify: bool) -> bool { /* ... */ }
  ```

- ```rust
  fn row_activated(self: &Self, path: &TreePath, column: &TreeViewColumn) { /* ... */ }
  ```

- ```rust
  fn row_collapsed(self: &Self, iter: &TreeIter, path: &TreePath) { /* ... */ }
  ```

- ```rust
  fn row_expanded(self: &Self, iter: &TreeIter, path: &TreePath) { /* ... */ }
  ```

- ```rust
  fn select_all(self: &Self) -> bool { /* ... */ }
  ```

- ```rust
  fn select_cursor_parent(self: &Self) -> bool { /* ... */ }
  ```

- ```rust
  fn select_cursor_row(self: &Self, start_editing: bool) -> bool { /* ... */ }
  ```

- ```rust
  fn start_interactive_search(self: &Self) -> bool { /* ... */ }
  ```

- ```rust
  fn test_collapse_row(self: &Self, iter: &TreeIter, path: &TreePath) -> bool { /* ... */ }
  ```

- ```rust
  fn test_expand_row(self: &Self, iter: &TreeIter, path: &TreePath) -> bool { /* ... */ }
  ```

- ```rust
  fn toggle_cursor_row(self: &Self) -> bool { /* ... */ }
  ```

- ```rust
  fn unselect_all(self: &Self) -> bool { /* ... */ }
  ```

#### Trait `TreeViewImplExt`

**Attributes:**

- `Other("#[allow(deprecated)]")`
- `Other("#[attr = CfgAttrTrace]")`

**⚠️ Deprecated**: Since 4.10

```rust
pub trait TreeViewImplExt: TreeViewImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_columns_changed(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_cursor_changed(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_expand_collapse_cursor_row(self: &Self, logical: bool, expand: bool, open_all: bool) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_move_cursor(self: &Self, step: MovementStep, count: i32, extend: bool, modify: bool) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_row_activated(self: &Self, path: &TreePath, column: &TreeViewColumn) { /* ... */ }
  ```

- ```rust
  fn parent_row_collapsed(self: &Self, iter: &TreeIter, path: &TreePath) { /* ... */ }
  ```

- ```rust
  fn parent_row_expanded(self: &Self, iter: &TreeIter, path: &TreePath) { /* ... */ }
  ```

- ```rust
  fn parent_select_all(self: &Self) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_select_cursor_parent(self: &Self) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_select_cursor_row(self: &Self, start_editing: bool) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_start_interactive_search(self: &Self) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_test_collapse_row(self: &Self, iter: &TreeIter, path: &TreePath) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_test_expand_row(self: &Self, iter: &TreeIter, path: &TreePath) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_toggle_cursor_row(self: &Self) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_unselect_all(self: &Self) -> bool { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: TreeViewImpl>

## Module `widget`

Traits intended for subclassing [`Widget`].

```rust
pub mod widget { /* ... */ }
```

### Types

#### Struct `WidgetActionIter`

```rust
pub struct WidgetActionIter(/* private field */, /* private field */);
```

##### Fields

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `private` | *Private field* |
| 1 | `private` | *Private field* |

##### Implementations

###### Trait Implementations

- **Any**
  - ```rust
    fn type_id(self: &Self) -> TypeId { /* ... */ }
    ```

- **Borrow**
  - ```rust
    fn borrow(self: &Self) -> &T { /* ... */ }
    ```

- **BorrowMut**
  - ```rust
    fn borrow_mut(self: &mut Self) -> &mut T { /* ... */ }
    ```

- **Freeze**
- **From**
  - ```rust
    fn from(t: T) -> T { /* ... */ }
    ```
    Returns the argument unchanged.

- **FusedIterator**
- **Into**
  - ```rust
    fn into(self: Self) -> U { /* ... */ }
    ```
    Calls `U::from(self)`.

- **IntoIterator**
  - ```rust
    fn into_iter(self: Self) -> I { /* ... */ }
    ```

- **Iterator**
  - ```rust
    fn next(self: &mut Self) -> Option<<Self as >::Item> { /* ... */ }
    ```

- **RefUnwindSafe**
- **Send**
- **Sync**
- **TryFrom**
  - ```rust
    fn try_from(value: U) -> Result<T, <T as TryFrom<U>>::Error> { /* ... */ }
    ```

- **TryInto**
  - ```rust
    fn try_into(self: Self) -> Result<U, <U as TryFrom<T>>::Error> { /* ... */ }
    ```

- **Unpin**
- **UnsafeUnpin**
- **UnwindSafe**
#### Struct `WidgetAction`

```rust
pub struct WidgetAction(/* private field */, /* private field */, /* private field */, /* private field */);
```

##### Fields

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `private` | *Private field* |
| 1 | `private` | *Private field* |
| 2 | `private` | *Private field* |
| 3 | `private` | *Private field* |

##### Implementations

###### Methods

- ```rust
  pub fn owner(self: &Self) -> glib::Type { /* ... */ }
  ```
  The type where the action was defined

- ```rust
  pub fn name(self: &Self) -> &str { /* ... */ }
  ```
  The action name

- ```rust
  pub fn parameter_type(self: &Self) -> Option<&glib::VariantType> { /* ... */ }
  ```
  The action parameter type

- ```rust
  pub fn property_name(self: &Self) -> Option<&str> { /* ... */ }
  ```
  The action property name

###### Trait Implementations

- **Any**
  - ```rust
    fn type_id(self: &Self) -> TypeId { /* ... */ }
    ```

- **Borrow**
  - ```rust
    fn borrow(self: &Self) -> &T { /* ... */ }
    ```

- **BorrowMut**
  - ```rust
    fn borrow_mut(self: &mut Self) -> &mut T { /* ... */ }
    ```

- **Debug**
  - ```rust
    fn fmt(self: &Self, f: &mut fmt::Formatter<''_>) -> fmt::Result { /* ... */ }
    ```

- **Freeze**
- **From**
  - ```rust
    fn from(t: T) -> T { /* ... */ }
    ```
    Returns the argument unchanged.

- **Into**
  - ```rust
    fn into(self: Self) -> U { /* ... */ }
    ```
    Calls `U::from(self)`.

- **RefUnwindSafe**
- **Send**
- **Sync**
- **TryFrom**
  - ```rust
    fn try_from(value: U) -> Result<T, <T as TryFrom<U>>::Error> { /* ... */ }
    ```

- **TryInto**
  - ```rust
    fn try_into(self: Self) -> Result<U, <U as TryFrom<T>>::Error> { /* ... */ }
    ```

- **Unpin**
- **UnsafeUnpin**
- **UnwindSafe**
#### Struct `TemplateChild`

**Attributes:**

- `Repr(AttributeRepr { kind: Transparent, align: None, packed: None, int: None })`

```rust
pub struct TemplateChild<T> {
    // Some fields omitted
}
```

##### Fields

| Name | Type | Documentation |
|------|------|---------------|
| *private fields* | ... | *Some fields have been omitted* |

##### Implementations

###### Methods

- ```rust
  pub fn get(self: &Self) -> T { /* ... */ }
  ```

- ```rust
  pub fn is_bound(self: &Self) -> bool { /* ... */ }
  ```
  Determines if the child has been bound. This is primarily

- ```rust
  pub fn try_get(self: &Self) -> Option<T> { /* ... */ }
  ```
  Returns Some(child) if the widget has been bound.

###### Trait Implementations

- **Any**
  - ```rust
    fn type_id(self: &Self) -> TypeId { /* ... */ }
    ```

- **Borrow**
  - ```rust
    fn borrow(self: &Self) -> &T { /* ... */ }
    ```

- **BorrowMut**
  - ```rust
    fn borrow_mut(self: &mut Self) -> &mut T { /* ... */ }
    ```

- **Debug**
  - ```rust
    fn fmt(self: &Self, f: &mut $crate::fmt::Formatter<''_>) -> $crate::fmt::Result { /* ... */ }
    ```

- **Default**
  - ```rust
    fn default() -> Self { /* ... */ }
    ```

- **Deref**
  - ```rust
    fn deref(self: &Self) -> &<Self as >::Target { /* ... */ }
    ```

- **Downgrade**
  - ```rust
    fn downgrade(self: &Self) -> <Self as >::Weak { /* ... */ }
    ```

- **Eq**
- **Freeze**
- **From**
  - ```rust
    fn from(t: T) -> T { /* ... */ }
    ```
    Returns the argument unchanged.

- **Into**
  - ```rust
    fn into(self: Self) -> U { /* ... */ }
    ```
    Calls `U::from(self)`.

- **PartialEq**
  - ```rust
    fn eq(self: &Self, other: &TemplateChild<T>) -> bool { /* ... */ }
    ```

- **Property**
- **PropertyGet**
  - ```rust
    fn get<R, F: Fn(&<Self as >::Value) -> R>(self: &Self, f: F) -> R { /* ... */ }
    ```

- **Receiver**
- **RefUnwindSafe**
- **Send**
- **StructuralPartialEq**
- **Sync**
- **TryFrom**
  - ```rust
    fn try_from(value: U) -> Result<T, <T as TryFrom<U>>::Error> { /* ... */ }
    ```

- **TryInto**
  - ```rust
    fn try_into(self: Self) -> Result<U, <U as TryFrom<T>>::Error> { /* ... */ }
    ```

- **Unpin**
- **UnsafeUnpin**
- **UnwindSafe**
#### Type Alias `TemplateCallback`

```rust
pub type TemplateCallback = (&''static str, fn(&[glib::Value]) -> Option<glib::Value>);
```

### Traits

#### Trait `WidgetImpl`

```rust
pub trait WidgetImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::Widget> + IsA<crate::Accessible> + IsA<crate::Buildable> + IsA<crate::ConstraintTarget>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn compute_expand(self: &Self, hexpand: &mut bool, vexpand: &mut bool) { /* ... */ }
  ```

- ```rust
  fn contains(self: &Self, x: f64, y: f64) -> bool { /* ... */ }
  ```

- ```rust
  fn direction_changed(self: &Self, previous_direction: TextDirection) { /* ... */ }
  ```

- ```rust
  fn focus(self: &Self, direction_type: DirectionType) -> bool { /* ... */ }
  ```

- ```rust
  fn request_mode(self: &Self) -> SizeRequestMode { /* ... */ }
  ```

- ```rust
  fn grab_focus(self: &Self) -> bool { /* ... */ }
  ```

- ```rust
  fn hide(self: &Self) { /* ... */ }
  ```

- ```rust
  fn keynav_failed(self: &Self, direction_type: DirectionType) -> bool { /* ... */ }
  ```

- ```rust
  fn map(self: &Self) { /* ... */ }
  ```

- ```rust
  fn measure(self: &Self, orientation: Orientation, for_size: i32) -> (i32, i32, i32, i32) { /* ... */ }
  ```

- ```rust
  fn mnemonic_activate(self: &Self, group_cycling: bool) -> bool { /* ... */ }
  ```

- ```rust
  fn move_focus(self: &Self, direction_type: DirectionType) { /* ... */ }
  ```

- ```rust
  fn query_tooltip(self: &Self, x: i32, y: i32, keyboard_tooltip: bool, tooltip: &Tooltip) -> bool { /* ... */ }
  ```

- ```rust
  fn realize(self: &Self) { /* ... */ }
  ```

- ```rust
  fn root(self: &Self) { /* ... */ }
  ```

- ```rust
  fn set_focus_child(self: &Self, child: Option<&Widget>) { /* ... */ }
  ```

- ```rust
  fn show(self: &Self) { /* ... */ }
  ```

- ```rust
  fn size_allocate(self: &Self, width: i32, height: i32, baseline: i32) { /* ... */ }
  ```

- ```rust
  fn snapshot(self: &Self, snapshot: &Snapshot) { /* ... */ }
  ```

- ```rust
  fn state_flags_changed(self: &Self, state_flags: &StateFlags) { /* ... */ }
  ```

- ```rust
  fn system_setting_changed(self: &Self, settings: &SystemSetting) { /* ... */ }
  ```

- ```rust
  fn unmap(self: &Self) { /* ... */ }
  ```

- ```rust
  fn unrealize(self: &Self) { /* ... */ }
  ```

- ```rust
  fn unroot(self: &Self) { /* ... */ }
  ```

#### Trait `WidgetImplExt`

```rust
pub trait WidgetImplExt: WidgetImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_compute_expand(self: &Self, hexpand: &mut bool, vexpand: &mut bool) { /* ... */ }
  ```

- ```rust
  fn parent_contains(self: &Self, x: f64, y: f64) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_direction_changed(self: &Self, previous_direction: TextDirection) { /* ... */ }
  ```

- ```rust
  fn parent_focus(self: &Self, direction_type: DirectionType) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_request_mode(self: &Self) -> SizeRequestMode { /* ... */ }
  ```

- ```rust
  fn parent_grab_focus(self: &Self) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_hide(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_keynav_failed(self: &Self, direction_type: DirectionType) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_map(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_measure(self: &Self, orientation: Orientation, for_size: i32) -> (i32, i32, i32, i32) { /* ... */ }
  ```

- ```rust
  fn parent_mnemonic_activate(self: &Self, group_cycling: bool) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_move_focus(self: &Self, direction_type: DirectionType) { /* ... */ }
  ```

- ```rust
  fn parent_query_tooltip(self: &Self, x: i32, y: i32, keyboard_tooltip: bool, tooltip: &Tooltip) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_realize(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_root(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_set_focus_child(self: &Self, child: Option<&Widget>) { /* ... */ }
  ```

- ```rust
  fn parent_show(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_size_allocate(self: &Self, width: i32, height: i32, baseline: i32) { /* ... */ }
  ```

- ```rust
  fn parent_snapshot(self: &Self, snapshot: &Snapshot) { /* ... */ }
  ```

- ```rust
  fn parent_state_flags_changed(self: &Self, state_flags: &StateFlags) { /* ... */ }
  ```

- ```rust
  fn parent_system_setting_changed(self: &Self, settings: &SystemSetting) { /* ... */ }
  ```

- ```rust
  fn parent_unmap(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_unrealize(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_unroot(self: &Self) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: WidgetImpl>

#### Trait `WidgetClassExt`

**Attributes:**

- `Other("#[allow(clippy::missing_safety_doc)]")`

```rust
pub unsafe trait WidgetClassExt: ClassStruct {
    /* Associated items */
}
```

> This trait is unsafe to implement.

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn set_template_bytes(self: &mut Self, template: &glib::Bytes) { /* ... */ }
  ```

- ```rust
  fn set_template(self: &mut Self, template: &[u8]) { /* ... */ }
  ```

- ```rust
  fn set_template_static(self: &mut Self, template: &''static [u8]) { /* ... */ }
  ```

- ```rust
  fn set_template_from_resource(self: &mut Self, resource_name: &str) { /* ... */ }
  ```

- ```rust
  fn install_action_async<Fut, F>(self: &mut Self, action_name: &str, parameter_type: Option<&glib::VariantTy>, activate: F)
where
    F: Fn(<<Self as ClassStruct>::Type as ObjectSubclass>::Type, String, Option<Variant>) -> Fut + ''static + Clone,
    Fut: Future<Output = ()> { /* ... */ }
  ```

- ```rust
  fn install_action<F>(self: &mut Self, action_name: &str, parameter_type: Option<&glib::VariantTy>, activate: F)
where
    F: Fn(&<<Self as ClassStruct>::Type as ObjectSubclass>::Type, &str, Option<&Variant>) + ''static { /* ... */ }
  ```

- ```rust
  fn query_action(self: &Self) -> WidgetActionIter { /* ... */ }
  ```

- ```rust
  fn set_template_scope<S: IsA<BuilderScope>>(self: &mut Self, scope: &S) { /* ... */ }
  ```

- ```rust
  fn add_binding<F: Fn(&<<Self as ClassStruct>::Type as ObjectSubclass>::Type) -> glib::Propagation + ''static>(self: &mut Self, keyval: gdk::Key, mods: gdk::ModifierType, callback: F) { /* ... */ }
  ```

- ```rust
  fn add_binding_action(self: &mut Self, keyval: gdk::Key, mods: gdk::ModifierType, action_name: &str) { /* ... */ }
  ```

- ```rust
  fn add_binding_signal(self: &mut Self, keyval: gdk::Key, mods: gdk::ModifierType, signal_name: &str) { /* ... */ }
  ```

- ```rust
  fn add_shortcut(self: &mut Self, shortcut: &Shortcut) { /* ... */ }
  ```

- ```rust
  fn install_property_action(self: &mut Self, action_name: &str, property_name: &str) { /* ... */ }
  ```

- ```rust
  fn activate_signal(self: &Self) -> Option<SignalId> { /* ... */ }
  ```

- ```rust
  fn set_activate_signal(self: &mut Self, signal_id: SignalId) { /* ... */ }
  ```

- ```rust
  fn set_activate_signal_from_name(self: &mut Self, signal_name: &str) { /* ... */ }
  ```

- ```rust
  fn set_layout_manager_type<T: IsA<LayoutManager>>(self: &mut Self) { /* ... */ }
  ```

- ```rust
  fn layout_manager_type(self: &Self) -> glib::Type { /* ... */ }
  ```

- ```rust
  fn set_css_name(self: &mut Self, name: &str) { /* ... */ }
  ```

- ```rust
  fn css_name(self: &Self) -> glib::GString { /* ... */ }
  ```

- ```rust
  fn set_accessible_role(self: &mut Self, role: AccessibleRole) { /* ... */ }
  ```

- ```rust
  fn accessible_role(self: &Self) -> AccessibleRole { /* ... */ }
  ```

- ```rust
  unsafe fn bind_template_child_with_offset<T>(self: &mut Self, name: &str, internal: bool, offset: field_offset::FieldOffset<<Self as >::Type, TemplateChild<T>>)
where
    T: ObjectType + FromGlibPtrNone<*mut <T as ObjectType>::GlibType> { /* ... */ }
  ```

- ```rust
  fn rust_template_scope(self: &mut Self) -> BuilderRustScope { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: ClassStruct>

#### Trait `CompositeTemplate`

A trait for setting up template children inside
[`class_init`](glib::subclass::types::ObjectSubclass::class_init). This
trait is implemented automatically by the
[`CompositeTemplate`](crate::CompositeTemplate) macro.

```rust
pub trait CompositeTemplate: WidgetImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Required Items

###### Required Methods

- `bind_template`
- `check_template_children`

#### Trait `CompositeTemplateClass`

An extension trait for [`ClassStruct`](glib::subclass::types::ClassStruct)
types to allow binding a composite template directly on `self`. This is a
convenience wrapper around the [`CompositeTemplate`] trait.

```rust
pub trait CompositeTemplateClass {
    /* Associated items */
}
```

##### Required Items

###### Required Methods

- `bind_template`: Binds the template callbacks from this type into the default template

##### Implementations

This trait is implemented for the following types:

- `T` with <T, U>

#### Trait `CompositeTemplateCallbacks`

A trait for setting up template callbacks inside
[`class_init`](glib::subclass::types::ObjectSubclass::class_init). This
trait is implemented automatically by the
[`template_callbacks`](crate::template_callbacks) macro.

```rust
pub trait CompositeTemplateCallbacks {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Required Items

###### Associated Constants

- `CALLBACKS`

##### Provided Methods

- ```rust
  fn bind_template_callbacks<T: WidgetClassExt>(klass: &mut T) { /* ... */ }
  ```
  Binds the template callbacks from this type into the default template

- ```rust
  fn bind_template_callbacks_prefixed<T: WidgetClassExt>(klass: &mut T, prefix: &str) { /* ... */ }
  ```
  Binds the template callbacks from this type into the default template

- ```rust
  fn add_callbacks_to_scope(scope: &BuilderRustScope) { /* ... */ }
  ```
  Binds the template callbacks from this type into `scope`.

- ```rust
  fn add_callbacks_to_scope_prefixed(scope: &BuilderRustScope, prefix: &str) { /* ... */ }
  ```
  Binds the template callbacks from this type into `scope`, prepending

#### Trait `CompositeTemplateCallbacksClass`

An extension trait for [`ClassStruct`](glib::subclass::types::ClassStruct)
types to allow binding private template callbacks directly on `self`. This
is a convenience wrapper around the [`CompositeTemplateCallbacks`] trait.

```rust
pub trait CompositeTemplateCallbacksClass {
    /* Associated items */
}
```

##### Required Items

###### Required Methods

- `bind_template_callbacks`: Binds the template callbacks from the subclass type into the default

##### Implementations

This trait is implemented for the following types:

- `T` with <T, U>

#### Trait `CompositeTemplateInstanceCallbacksClass`

An extension trait for [`ClassStruct`](glib::subclass::types::ClassStruct)
types to allow binding the instance template callbacks directly on `self`.
This is a convenience wrapper around the [`CompositeTemplateCallbacks`]
trait.

```rust
pub trait CompositeTemplateInstanceCallbacksClass {
    /* Associated items */
}
```

##### Required Items

###### Required Methods

- `bind_template_instance_callbacks`: Binds the template callbacks from the instance type into the default

##### Implementations

This trait is implemented for the following types:

- `T` with <T, U, V>

#### Trait `CompositeTemplateInitializingExt`

```rust
pub trait CompositeTemplateInitializingExt {
    /* Associated items */
}
```

##### Required Items

###### Required Methods

- `init_template`

##### Implementations

This trait is implemented for the following types:

- `glib::subclass::InitializingObject<T>` with <T: WidgetImpl + CompositeTemplate>

#### Trait `CompositeTemplateDisposeExt`

```rust
pub trait CompositeTemplateDisposeExt {
    /* Associated items */
}
```

##### Required Items

###### Required Methods

- `dispose_template`

##### Implementations

This trait is implemented for the following types:

- `T` with <T: WidgetImpl + CompositeTemplate>

## Module `window`

Traits intended for subclassing [`Window`].

```rust
pub mod window { /* ... */ }
```

### Traits

#### Trait `WindowImpl`

```rust
pub trait WindowImpl: WidgetImpl + ObjectSubclass<Type: IsA<crate::Window> + IsA<crate::Native> + IsA<crate::Root> + IsA<crate::ShortcutManager>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn activate_focus(self: &Self) { /* ... */ }
  ```

- ```rust
  fn activate_default(self: &Self) { /* ... */ }
  ```

- ```rust
  fn keys_changed(self: &Self) { /* ... */ }
  ```

- ```rust
  fn enable_debugging(self: &Self, toggle: bool) -> bool { /* ... */ }
  ```

- ```rust
  fn close_request(self: &Self) -> glib::Propagation { /* ... */ }
  ```

#### Trait `WindowImplExt`

```rust
pub trait WindowImplExt: WindowImpl {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn parent_activate_focus(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_activate_default(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_keys_changed(self: &Self) { /* ... */ }
  ```

- ```rust
  fn parent_enable_debugging(self: &Self, toggle: bool) -> bool { /* ... */ }
  ```

- ```rust
  fn parent_close_request(self: &Self) -> glib::Propagation { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `T` with <T: WindowImpl>

## Module `window_group`

Traits intended for subclassing [`WindowGroup`].

```rust
pub mod window_group { /* ... */ }
```

### Traits

#### Trait `WindowGroupImpl`

```rust
pub trait WindowGroupImpl: ObjectImpl + ObjectSubclass<Type: IsA<crate::WindowGroup>> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

## Module `prelude`

Traits intended for blanket imports.

```rust
pub mod prelude { /* ... */ }
```

### Re-exports

#### Re-export `AccessibleImpl`

**Attributes:**

- `Other("#[attr = CfgTrace([NameValue { name: \"feature\", value: Some(\"v4_10\"), span: src/subclass/mod.rs:98:11: 98:28 (#0) }])]")`
- `Other("#[attr = CfgAttrTrace]")`
- `Other("#[doc(cfg(feature = \"v4_10\"))]")`

```rust
pub use super::accessible::AccessibleImpl;
```

#### Re-export `AccessibleImplExt`

**Attributes:**

- `Other("#[attr = CfgTrace([NameValue { name: \"feature\", value: Some(\"v4_10\"), span: src/subclass/mod.rs:98:11: 98:28 (#0) }])]")`
- `Other("#[attr = CfgAttrTrace]")`
- `Other("#[doc(cfg(feature = \"v4_10\"))]")`

```rust
pub use super::accessible::AccessibleImplExt;
```

#### Re-export `AccessibleRangeImpl`

**Attributes:**

- `Other("#[attr = CfgTrace([NameValue { name: \"feature\", value: Some(\"v4_10\"), span: src/subclass/mod.rs:101:11: 101:28 (#0) }])]")`
- `Other("#[attr = CfgAttrTrace]")`
- `Other("#[doc(cfg(feature = \"v4_10\"))]")`

```rust
pub use super::accessible_range::AccessibleRangeImpl;
```

#### Re-export `AccessibleRangeImplExt`

**Attributes:**

- `Other("#[attr = CfgTrace([NameValue { name: \"feature\", value: Some(\"v4_10\"), span: src/subclass/mod.rs:101:11: 101:28 (#0) }])]")`
- `Other("#[attr = CfgAttrTrace]")`
- `Other("#[doc(cfg(feature = \"v4_10\"))]")`

```rust
pub use super::accessible_range::AccessibleRangeImplExt;
```

#### Re-export `AccessibleTextImpl`

**Attributes:**

- `Other("#[attr = CfgTrace([NameValue { name: \"feature\", value: Some(\"v4_14\"), span: src/subclass/mod.rs:104:11: 104:28 (#0) }])]")`
- `Other("#[attr = CfgAttrTrace]")`
- `Other("#[doc(cfg(feature = \"v4_14\"))]")`

```rust
pub use super::accessible_text::AccessibleTextImpl;
```

#### Re-export `AccessibleTextImplExt`

**Attributes:**

- `Other("#[attr = CfgTrace([NameValue { name: \"feature\", value: Some(\"v4_14\"), span: src/subclass/mod.rs:104:11: 104:28 (#0) }])]")`
- `Other("#[attr = CfgAttrTrace]")`
- `Other("#[doc(cfg(feature = \"v4_14\"))]")`

```rust
pub use super::accessible_text::AccessibleTextImplExt;
```

#### Re-export `SectionModelImpl`

**Attributes:**

- `Other("#[attr = CfgTrace([NameValue { name: \"feature\", value: Some(\"v4_12\"), span: src/subclass/mod.rs:107:11: 107:28 (#0) }])]")`
- `Other("#[attr = CfgAttrTrace]")`
- `Other("#[doc(cfg(feature = \"v4_12\"))]")`

```rust
pub use super::section_model::SectionModelImpl;
```

#### Re-export `SectionModelImplExt`

**Attributes:**

- `Other("#[attr = CfgTrace([NameValue { name: \"feature\", value: Some(\"v4_12\"), span: src/subclass/mod.rs:107:11: 107:28 (#0) }])]")`
- `Other("#[attr = CfgAttrTrace]")`
- `Other("#[doc(cfg(feature = \"v4_12\"))]")`

```rust
pub use super::section_model::SectionModelImplExt;
```

#### Re-export `SymbolicPaintableImpl`

**Attributes:**

- `Other("#[attr = CfgTrace([NameValue { name: \"feature\", value: Some(\"v4_6\"), span: src/subclass/mod.rs:110:11: 110:27 (#0) }])]")`
- `Other("#[attr = CfgAttrTrace]")`
- `Other("#[doc(cfg(feature = \"v4_6\"))]")`

```rust
pub use super::symbolic_paintable::SymbolicPaintableImpl;
```

#### Re-export `SymbolicPaintableImplExt`

**Attributes:**

- `Other("#[attr = CfgTrace([NameValue { name: \"feature\", value: Some(\"v4_6\"), span: src/subclass/mod.rs:110:11: 110:27 (#0) }])]")`
- `Other("#[attr = CfgAttrTrace]")`
- `Other("#[doc(cfg(feature = \"v4_6\"))]")`

```rust
pub use super::symbolic_paintable::SymbolicPaintableImplExt;
```

#### Re-export `ActionableImpl`

```rust
pub use super::actionable::ActionableImpl;
```

#### Re-export `ActionableImplExt`

```rust
pub use super::actionable::ActionableImplExt;
```

#### Re-export `AdjustmentImpl`

```rust
pub use super::adjustment::AdjustmentImpl;
```

#### Re-export `AdjustmentImplExt`

```rust
pub use super::adjustment::AdjustmentImplExt;
```

#### Re-export `GtkApplicationImpl`

```rust
pub use super::application::GtkApplicationImpl;
```

#### Re-export `GtkApplicationImplExt`

```rust
pub use super::application::GtkApplicationImplExt;
```

#### Re-export `ApplicationWindowImpl`

```rust
pub use super::application_window::ApplicationWindowImpl;
```

#### Re-export `ApplicationWindowImplExt`

```rust
pub use super::application_window::ApplicationWindowImplExt;
```

#### Re-export `BoxImpl`

```rust
pub use super::box_::BoxImpl;
```

#### Re-export `BuildableImpl`

```rust
pub use super::buildable::BuildableImpl;
```

#### Re-export `BuildableImplExt`

```rust
pub use super::buildable::BuildableImplExt;
```

#### Re-export `BuilderCScopeImpl`

```rust
pub use super::builder_scope::BuilderCScopeImpl;
```

#### Re-export `BuilderScopeImpl`

```rust
pub use super::builder_scope::BuilderScopeImpl;
```

#### Re-export `BuilderScopeImplExt`

```rust
pub use super::builder_scope::BuilderScopeImplExt;
```

#### Re-export `ButtonImpl`

```rust
pub use super::button::ButtonImpl;
```

#### Re-export `ButtonImplExt`

```rust
pub use super::button::ButtonImplExt;
```

#### Re-export `CellAreaClassExt`

```rust
pub use super::cell_area::CellAreaClassExt;
```

#### Re-export `CellAreaImpl`

```rust
pub use super::cell_area::CellAreaImpl;
```

#### Re-export `CellAreaImplExt`

```rust
pub use super::cell_area::CellAreaImplExt;
```

#### Re-export `CellAreaContextImpl`

```rust
pub use super::cell_area_context::CellAreaContextImpl;
```

#### Re-export `CellAreaContextImplExt`

```rust
pub use super::cell_area_context::CellAreaContextImplExt;
```

#### Re-export `CellEditableImpl`

```rust
pub use super::cell_editable::CellEditableImpl;
```

#### Re-export `CellEditableImplExt`

```rust
pub use super::cell_editable::CellEditableImplExt;
```

#### Re-export `CellLayoutImpl`

```rust
pub use super::cell_layout::CellLayoutImpl;
```

#### Re-export `CellLayoutImplExt`

```rust
pub use super::cell_layout::CellLayoutImplExt;
```

#### Re-export `CellRendererImpl`

```rust
pub use super::cell_renderer::CellRendererImpl;
```

#### Re-export `CellRendererImplExt`

```rust
pub use super::cell_renderer::CellRendererImplExt;
```

#### Re-export `CellRendererTextImpl`

```rust
pub use super::cell_renderer_text::CellRendererTextImpl;
```

#### Re-export `CellRendererTextImplExt`

```rust
pub use super::cell_renderer_text::CellRendererTextImplExt;
```

#### Re-export `CheckButtonImpl`

```rust
pub use super::check_button::CheckButtonImpl;
```

#### Re-export `CheckButtonImplExt`

```rust
pub use super::check_button::CheckButtonImplExt;
```

#### Re-export `ColorChooserImpl`

```rust
pub use super::color_chooser::ColorChooserImpl;
```

#### Re-export `ColorChooserImplExt`

```rust
pub use super::color_chooser::ColorChooserImplExt;
```

#### Re-export `ComboBoxImpl`

```rust
pub use super::combo_box::ComboBoxImpl;
```

#### Re-export `ComboBoxImplExt`

```rust
pub use super::combo_box::ComboBoxImplExt;
```

#### Re-export `DialogImpl`

```rust
pub use super::dialog::DialogImpl;
```

#### Re-export `DialogImplExt`

```rust
pub use super::dialog::DialogImplExt;
```

#### Re-export `DrawingAreaImpl`

```rust
pub use super::drawing_area::DrawingAreaImpl;
```

#### Re-export `DrawingAreaImplExt`

```rust
pub use super::drawing_area::DrawingAreaImplExt;
```

#### Re-export `EditableImpl`

```rust
pub use super::editable::EditableImpl;
```

#### Re-export `EditableImplExt`

```rust
pub use super::editable::EditableImplExt;
```

#### Re-export `EntryImpl`

```rust
pub use super::entry::EntryImpl;
```

#### Re-export `EntryImplExt`

```rust
pub use super::entry::EntryImplExt;
```

#### Re-export `EntryBufferImpl`

```rust
pub use super::entry_buffer::EntryBufferImpl;
```

#### Re-export `EntryBufferImplExt`

```rust
pub use super::entry_buffer::EntryBufferImplExt;
```

#### Re-export `FilterImpl`

```rust
pub use super::filter::FilterImpl;
```

#### Re-export `FilterImplExt`

```rust
pub use super::filter::FilterImplExt;
```

#### Re-export `FixedImpl`

```rust
pub use super::fixed::FixedImpl;
```

#### Re-export `FlowBoxChildImpl`

```rust
pub use super::flow_box_child::FlowBoxChildImpl;
```

#### Re-export `FlowBoxChildImplExt`

```rust
pub use super::flow_box_child::FlowBoxChildImplExt;
```

#### Re-export `FontChooserImpl`

```rust
pub use super::font_chooser::FontChooserImpl;
```

#### Re-export `FontChooserImplExt`

```rust
pub use super::font_chooser::FontChooserImplExt;
```

#### Re-export `FrameImpl`

```rust
pub use super::frame::FrameImpl;
```

#### Re-export `FrameImplExt`

```rust
pub use super::frame::FrameImplExt;
```

#### Re-export `GLAreaImpl`

```rust
pub use super::gl_area::GLAreaImpl;
```

#### Re-export `GLAreaImplExt`

```rust
pub use super::gl_area::GLAreaImplExt;
```

#### Re-export `GridImpl`

```rust
pub use super::grid::GridImpl;
```

#### Re-export `IMContextImpl`

```rust
pub use super::im_context::IMContextImpl;
```

#### Re-export `IMContextImplExt`

```rust
pub use super::im_context::IMContextImplExt;
```

#### Re-export `LayoutChildImpl`

```rust
pub use super::layout_child::LayoutChildImpl;
```

#### Re-export `LayoutManagerImpl`

```rust
pub use super::layout_manager::LayoutManagerImpl;
```

#### Re-export `LayoutManagerImplExt`

```rust
pub use super::layout_manager::LayoutManagerImplExt;
```

#### Re-export `ListBoxRowImpl`

```rust
pub use super::list_box_row::ListBoxRowImpl;
```

#### Re-export `ListBoxRowImplExt`

```rust
pub use super::list_box_row::ListBoxRowImplExt;
```

#### Re-export `MediaFileImpl`

```rust
pub use super::media_file::MediaFileImpl;
```

#### Re-export `MediaFileImplExt`

```rust
pub use super::media_file::MediaFileImplExt;
```

#### Re-export `MediaStreamImpl`

```rust
pub use super::media_stream::MediaStreamImpl;
```

#### Re-export `MediaStreamImplExt`

```rust
pub use super::media_stream::MediaStreamImplExt;
```

#### Re-export `NativeDialogImpl`

```rust
pub use super::native_dialog::NativeDialogImpl;
```

#### Re-export `NativeDialogImplExt`

```rust
pub use super::native_dialog::NativeDialogImplExt;
```

#### Re-export `OrientableImpl`

```rust
pub use super::orientable::OrientableImpl;
```

#### Re-export `PopoverImpl`

```rust
pub use super::popover::PopoverImpl;
```

#### Re-export `PopoverImplExt`

```rust
pub use super::popover::PopoverImplExt;
```

#### Re-export `PrintOperationImpl`

```rust
pub use super::print_operation::PrintOperationImpl;
```

#### Re-export `PrintOperationImplExt`

```rust
pub use super::print_operation::PrintOperationImplExt;
```

#### Re-export `PrintOperationPreviewImpl`

```rust
pub use super::print_operation_preview::PrintOperationPreviewImpl;
```

#### Re-export `RangeImpl`

```rust
pub use super::range::RangeImpl;
```

#### Re-export `RangeImplExt`

```rust
pub use super::range::RangeImplExt;
```

#### Re-export `RecentManagerImpl`

```rust
pub use super::recent_manager::RecentManagerImpl;
```

#### Re-export `RecentManagerImplExt`

```rust
pub use super::recent_manager::RecentManagerImplExt;
```

#### Re-export `ScaleImpl`

```rust
pub use super::scale::ScaleImpl;
```

#### Re-export `ScaleImplExt`

```rust
pub use super::scale::ScaleImplExt;
```

#### Re-export `ScaleButtonImpl`

```rust
pub use super::scale_button::ScaleButtonImpl;
```

#### Re-export `ScaleButtonImplExt`

```rust
pub use super::scale_button::ScaleButtonImplExt;
```

#### Re-export `ScrollableImpl`

```rust
pub use super::scrollable::ScrollableImpl;
```

#### Re-export `ScrollableImplExt`

```rust
pub use super::scrollable::ScrollableImplExt;
```

#### Re-export `SelectionModelImpl`

```rust
pub use super::selection_model::SelectionModelImpl;
```

#### Re-export `SelectionModelImplExt`

```rust
pub use super::selection_model::SelectionModelImplExt;
```

#### Re-export `ShortcutManagerImpl`

```rust
pub use super::shortcut_manager::ShortcutManagerImpl;
```

#### Re-export `ShortcutManagerImplExt`

```rust
pub use super::shortcut_manager::ShortcutManagerImplExt;
```

#### Re-export `SorterImpl`

```rust
pub use super::sorter::SorterImpl;
```

#### Re-export `SorterImplExt`

```rust
pub use super::sorter::SorterImplExt;
```

#### Re-export `StyleContextImpl`

```rust
pub use super::style_context::StyleContextImpl;
```

#### Re-export `StyleContextImplExt`

```rust
pub use super::style_context::StyleContextImplExt;
```

#### Re-export `TextBufferImpl`

```rust
pub use super::text_buffer::TextBufferImpl;
```

#### Re-export `TextBufferImplExt`

```rust
pub use super::text_buffer::TextBufferImplExt;
```

#### Re-export `TextViewImpl`

```rust
pub use super::text_view::TextViewImpl;
```

#### Re-export `TextViewImplExt`

```rust
pub use super::text_view::TextViewImplExt;
```

#### Re-export `ToggleButtonImpl`

```rust
pub use super::toggle_button::ToggleButtonImpl;
```

#### Re-export `ToggleButtonImplExt`

```rust
pub use super::toggle_button::ToggleButtonImplExt;
```

#### Re-export `TreeDragDestImpl`

```rust
pub use super::tree_drag_dest::TreeDragDestImpl;
```

#### Re-export `TreeDragDestImplExt`

```rust
pub use super::tree_drag_dest::TreeDragDestImplExt;
```

#### Re-export `TreeDragSourceImpl`

```rust
pub use super::tree_drag_source::TreeDragSourceImpl;
```

#### Re-export `TreeDragSourceImplExt`

```rust
pub use super::tree_drag_source::TreeDragSourceImplExt;
```

#### Re-export `TreeModelFilterImpl`

```rust
pub use super::tree_model_filter::TreeModelFilterImpl;
```

#### Re-export `TreeModelFilterImplExt`

```rust
pub use super::tree_model_filter::TreeModelFilterImplExt;
```

#### Re-export `TreeViewImpl`

```rust
pub use super::tree_view::TreeViewImpl;
```

#### Re-export `TreeViewImplExt`

```rust
pub use super::tree_view::TreeViewImplExt;
```

#### Re-export `CompositeTemplate`

```rust
pub use super::widget::CompositeTemplate;
```

#### Re-export `CompositeTemplateCallbacks`

```rust
pub use super::widget::CompositeTemplateCallbacks;
```

#### Re-export `CompositeTemplateCallbacksClass`

```rust
pub use super::widget::CompositeTemplateCallbacksClass;
```

#### Re-export `CompositeTemplateClass`

```rust
pub use super::widget::CompositeTemplateClass;
```

#### Re-export `CompositeTemplateDisposeExt`

```rust
pub use super::widget::CompositeTemplateDisposeExt;
```

#### Re-export `CompositeTemplateInitializingExt`

```rust
pub use super::widget::CompositeTemplateInitializingExt;
```

#### Re-export `CompositeTemplateInstanceCallbacksClass`

```rust
pub use super::widget::CompositeTemplateInstanceCallbacksClass;
```

#### Re-export `TemplateChild`

```rust
pub use super::widget::TemplateChild;
```

#### Re-export `WidgetClassExt`

```rust
pub use super::widget::WidgetClassExt;
```

#### Re-export `WidgetImpl`

```rust
pub use super::widget::WidgetImpl;
```

#### Re-export `WidgetImplExt`

```rust
pub use super::widget::WidgetImplExt;
```

#### Re-export `WindowImpl`

```rust
pub use super::window::WindowImpl;
```

#### Re-export `WindowImplExt`

```rust
pub use super::window::WindowImplExt;
```

#### Re-export `WindowGroupImpl`

```rust
pub use super::window_group::WindowGroupImpl;
```

## Module `builders`

Builder pattern types.

```rust
pub mod builders { /* ... */ }
```

### Re-exports

#### Re-export `BookmarkListBuilder`

```rust
pub use crate::bookmark_list::BookmarkListBuilder;
```

#### Re-export `BorderBuilder`

```rust
pub use crate::border::BorderBuilder;
```

#### Re-export `DirectoryListBuilder`

```rust
pub use crate::directory_list::DirectoryListBuilder;
```

#### Re-export `ParamSpecExpressionBuilder`

```rust
pub use crate::param_spec_expression::ParamSpecExpressionBuilder;
```

#### Re-export `crate::auto::builders::*`

```rust
pub use crate::auto::builders::*;
```

## Module `prelude`

Traits intended for blanket imports.

```rust
pub mod prelude { /* ... */ }
```

### Re-exports

#### Re-export `AccessibleExtManual`

```rust
pub use crate::accessible::AccessibleExtManual;
```

#### Re-export `ActionableExtManual`

```rust
pub use crate::actionable::ActionableExtManual;
```

#### Re-export `CellAreaExtManual`

```rust
pub use crate::cell_area::CellAreaExtManual;
```

#### Re-export `CellLayoutExtManual`

```rust
pub use crate::cell_layout::CellLayoutExtManual;
```

#### Re-export `ColorChooserExtManual`

```rust
pub use crate::color_chooser::ColorChooserExtManual;
```

#### Re-export `ComboBoxExtManual`

```rust
pub use crate::combo_box::ComboBoxExtManual;
```

#### Re-export `DialogExtManual`

```rust
pub use crate::dialog::DialogExtManual;
```

#### Re-export `DrawingAreaExtManual`

```rust
pub use crate::drawing_area::DrawingAreaExtManual;
```

#### Re-export `EditableExtManual`

```rust
pub use crate::editable::EditableExtManual;
```

#### Re-export `EntryExtManual`

```rust
pub use crate::entry::EntryExtManual;
```

#### Re-export `EntryBufferExtManual`

```rust
pub use crate::entry_buffer::EntryBufferExtManual;
```

#### Re-export `EventControllerExtManual`

```rust
pub use crate::event_controller::EventControllerExtManual;
```

#### Re-export `GObjectPropertyExpressionExt`

```rust
pub use crate::expression::GObjectPropertyExpressionExt;
```

#### Re-export `IsExpression`

```rust
pub use crate::expression::IsExpression;
```

#### Re-export `FileChooserExtManual`

```rust
pub use crate::file_chooser::FileChooserExtManual;
```

#### Re-export `FontChooserExtManual`

```rust
pub use crate::font_chooser::FontChooserExtManual;
```

#### Re-export `MediaStreamExtManual`

```rust
pub use crate::media_stream::MediaStreamExtManual;
```

#### Re-export `NativeDialogExtManual`

```rust
pub use crate::native_dialog::NativeDialogExtManual;
```

#### Re-export `ScaleExtManual`

```rust
pub use crate::scale::ScaleExtManual;
```

#### Re-export `ShortcutTriggerExtManual`

```rust
pub use crate::shortcut_trigger::ShortcutTriggerExtManual;
```

#### Re-export `SnapshotExtManual`

```rust
pub use crate::snapshot::SnapshotExtManual;
```

#### Re-export `TextBufferExtManual`

```rust
pub use crate::text_buffer::TextBufferExtManual;
```

#### Re-export `TreeModelExtManual`

```rust
pub use crate::tree_model::TreeModelExtManual;
```

#### Re-export `TreeModelFilterExtManual`

```rust
pub use crate::tree_model_filter::TreeModelFilterExtManual;
```

#### Re-export `TreeSortableExtManual`

```rust
pub use crate::tree_sortable::TreeSortableExtManual;
```

#### Re-export `TreeViewExtManual`

```rust
pub use crate::tree_view::TreeViewExtManual;
```

#### Re-export `WidgetExtManual`

```rust
pub use crate::widget::WidgetExtManual;
```

#### Re-export `crate::auto::traits::*`

```rust
pub use crate::auto::traits::*;
```

## Module `accessible`

```rust
pub mod accessible { /* ... */ }
```

### Types

#### Enum `Property`

**Attributes:**

- `NonExhaustive`

Type-safe enum container for
[`AccessibleProperty`](crate::AccessibleProperty) values.

```rust
pub enum Property<''p> {
    Autocomplete(crate::AccessibleAutocomplete),
    Description(&''p str),
    HasPopup(bool),
    KeyShortcuts(&''p str),
    Label(&''p str),
    Level(i32),
    Modal(bool),
    MultiLine(bool),
    MultiSelectable(bool),
    Orientation(crate::Orientation),
    Placeholder(&''p str),
    ReadOnly(bool),
    Required(bool),
    RoleDescription(&''p str),
    Sort(crate::AccessibleSort),
    ValueMax(f64),
    ValueMin(f64),
    ValueNow(f64),
    ValueText(&''p str),
}
```

##### Variants

###### `Autocomplete`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `crate::AccessibleAutocomplete` |  |

###### `Description`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `&''p str` |  |

###### `HasPopup`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `bool` |  |

###### `KeyShortcuts`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `&''p str` |  |

###### `Label`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `&''p str` |  |

###### `Level`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `i32` |  |

###### `Modal`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `bool` |  |

###### `MultiLine`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `bool` |  |

###### `MultiSelectable`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `bool` |  |

###### `Orientation`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `crate::Orientation` |  |

###### `Placeholder`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `&''p str` |  |

###### `ReadOnly`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `bool` |  |

###### `Required`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `bool` |  |

###### `RoleDescription`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `&''p str` |  |

###### `Sort`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `crate::AccessibleSort` |  |

###### `ValueMax`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `f64` |  |

###### `ValueMin`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `f64` |  |

###### `ValueNow`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `f64` |  |

###### `ValueText`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `&''p str` |  |

##### Implementations

###### Trait Implementations

- **Any**
  - ```rust
    fn type_id(self: &Self) -> TypeId { /* ... */ }
    ```

- **Borrow**
  - ```rust
    fn borrow(self: &Self) -> &T { /* ... */ }
    ```

- **BorrowMut**
  - ```rust
    fn borrow_mut(self: &mut Self) -> &mut T { /* ... */ }
    ```

- **Debug**
  - ```rust
    fn fmt(self: &Self, f: &mut $crate::fmt::Formatter<''_>) -> $crate::fmt::Result { /* ... */ }
    ```

- **Freeze**
- **From**
  - ```rust
    fn from(t: T) -> T { /* ... */ }
    ```
    Returns the argument unchanged.

- **Into**
  - ```rust
    fn into(self: Self) -> U { /* ... */ }
    ```
    Calls `U::from(self)`.

- **RefUnwindSafe**
- **Send**
- **Sync**
- **TryFrom**
  - ```rust
    fn try_from(value: U) -> Result<T, <T as TryFrom<U>>::Error> { /* ... */ }
    ```

- **TryInto**
  - ```rust
    fn try_into(self: Self) -> Result<U, <U as TryFrom<T>>::Error> { /* ... */ }
    ```

- **Unpin**
- **UnsafeUnpin**
- **UnwindSafe**
#### Enum `Relation`

**Attributes:**

- `NonExhaustive`

Type-safe enum container for
[`AccessibleRelation`](crate::AccessibleRelation) values.

```rust
pub enum Relation<''r> {
    ActiveDescendant(&''r crate::Accessible),
    ColCount(i32),
    ColIndex(i32),
    ColIndexText(&''r str),
    ColSpan(i32),
    Controls(&''r [&''r crate::Accessible]),
    DescribedBy(&''r [&''r crate::Accessible]),
    Details(&''r [&''r crate::Accessible]),
    ErrorMessage(&''r [&''r crate::Accessible]),
    FlowTo(&''r [&''r crate::Accessible]),
    LabelledBy(&''r [&''r crate::Accessible]),
    Owns(&''r [&''r crate::Accessible]),
    PosInSet(i32),
    RowCount(i32),
    RowIndex(i32),
    RowIndexText(&''r str),
    RowSpan(i32),
    SetSize(i32),
}
```

##### Variants

###### `ActiveDescendant`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `&''r crate::Accessible` |  |

###### `ColCount`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `i32` |  |

###### `ColIndex`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `i32` |  |

###### `ColIndexText`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `&''r str` |  |

###### `ColSpan`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `i32` |  |

###### `Controls`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `&''r [&''r crate::Accessible]` |  |

###### `DescribedBy`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `&''r [&''r crate::Accessible]` |  |

###### `Details`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `&''r [&''r crate::Accessible]` |  |

###### `ErrorMessage`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `&''r [&''r crate::Accessible]` |  |

###### `FlowTo`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `&''r [&''r crate::Accessible]` |  |

###### `LabelledBy`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `&''r [&''r crate::Accessible]` |  |

###### `Owns`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `&''r [&''r crate::Accessible]` |  |

###### `PosInSet`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `i32` |  |

###### `RowCount`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `i32` |  |

###### `RowIndex`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `i32` |  |

###### `RowIndexText`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `&''r str` |  |

###### `RowSpan`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `i32` |  |

###### `SetSize`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `i32` |  |

##### Implementations

###### Trait Implementations

- **Any**
  - ```rust
    fn type_id(self: &Self) -> TypeId { /* ... */ }
    ```

- **Borrow**
  - ```rust
    fn borrow(self: &Self) -> &T { /* ... */ }
    ```

- **BorrowMut**
  - ```rust
    fn borrow_mut(self: &mut Self) -> &mut T { /* ... */ }
    ```

- **Debug**
  - ```rust
    fn fmt(self: &Self, f: &mut $crate::fmt::Formatter<''_>) -> $crate::fmt::Result { /* ... */ }
    ```

- **Freeze**
- **From**
  - ```rust
    fn from(t: T) -> T { /* ... */ }
    ```
    Returns the argument unchanged.

- **Into**
  - ```rust
    fn into(self: Self) -> U { /* ... */ }
    ```
    Calls `U::from(self)`.

- **RefUnwindSafe**
- **Send**
- **Sync**
- **TryFrom**
  - ```rust
    fn try_from(value: U) -> Result<T, <T as TryFrom<U>>::Error> { /* ... */ }
    ```

- **TryInto**
  - ```rust
    fn try_into(self: Self) -> Result<U, <U as TryFrom<T>>::Error> { /* ... */ }
    ```

- **Unpin**
- **UnsafeUnpin**
- **UnwindSafe**
#### Enum `State`

**Attributes:**

- `NonExhaustive`

Type-safe enum container for [`AccessibleState`](crate::AccessibleState)
values.

```rust
pub enum State {
    Busy(bool),
    Checked(crate::AccessibleTristate),
    Disabled(bool),
    Expanded(Option<bool>),
    Hidden(bool),
    Invalid(crate::AccessibleInvalidState),
    Pressed(crate::AccessibleTristate),
    Selected(Option<bool>),
}
```

##### Variants

###### `Busy`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `bool` |  |

###### `Checked`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `crate::AccessibleTristate` |  |

###### `Disabled`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `bool` |  |

###### `Expanded`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `Option<bool>` |  |

###### `Hidden`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `bool` |  |

###### `Invalid`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `crate::AccessibleInvalidState` |  |

###### `Pressed`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `crate::AccessibleTristate` |  |

###### `Selected`

Fields:

| Index | Type | Documentation |
|-------|------|---------------|
| 0 | `Option<bool>` |  |

##### Implementations

###### Trait Implementations

- **Any**
  - ```rust
    fn type_id(self: &Self) -> TypeId { /* ... */ }
    ```

- **Borrow**
  - ```rust
    fn borrow(self: &Self) -> &T { /* ... */ }
    ```

- **BorrowMut**
  - ```rust
    fn borrow_mut(self: &mut Self) -> &mut T { /* ... */ }
    ```

- **Debug**
  - ```rust
    fn fmt(self: &Self, f: &mut $crate::fmt::Formatter<''_>) -> $crate::fmt::Result { /* ... */ }
    ```

- **Freeze**
- **From**
  - ```rust
    fn from(t: T) -> T { /* ... */ }
    ```
    Returns the argument unchanged.

- **Into**
  - ```rust
    fn into(self: Self) -> U { /* ... */ }
    ```
    Calls `U::from(self)`.

- **RefUnwindSafe**
- **Send**
- **Sync**
- **TryFrom**
  - ```rust
    fn try_from(value: U) -> Result<T, <T as TryFrom<U>>::Error> { /* ... */ }
    ```

- **TryInto**
  - ```rust
    fn try_into(self: Self) -> Result<U, <U as TryFrom<T>>::Error> { /* ... */ }
    ```

- **Unpin**
- **UnsafeUnpin**
- **UnwindSafe**
### Traits

#### Trait `AccessibleExtManual`

Trait containing manually implemented methods of
[`Accessible`](crate::Accessible).

```no_run
# use gtk4 as gtk;
# use gtk::prelude::*;
let entry = gtk::Entry::new();
let label = gtk::Label::new(Some("Entry"));
entry.update_property(&[
    gtk::accessible::Property::Description("Test"),
    gtk::accessible::Property::Orientation(gtk::Orientation::Horizontal),
]);
entry.update_relation(&[gtk::accessible::Relation::LabelledBy(&[label.upcast_ref()])]);
entry.update_state(&[gtk::accessible::State::Invalid(
    gtk::AccessibleInvalidState::Grammar,
)]);
```

```rust
pub trait AccessibleExtManual: IsA<crate::Accessible> {
    /* Associated items */
}
```

> This trait is not object-safe and cannot be used in dynamic trait objects.

##### Provided Methods

- ```rust
  fn update_property(self: &Self, properties: &[Property<''_>]) { /* ... */ }
  ```

- ```rust
  fn update_relation(self: &Self, relations: &[Relation<''_>]) { /* ... */ }
  ```

- ```rust
  fn update_state(self: &Self, states: &[State]) { /* ... */ }
  ```

##### Implementations

This trait is implemented for the following types:

- `O` with <O: IsA<crate::Accessible>>

## Constants and Statics

### Constant `STYLE_PROVIDER_PRIORITY_FALLBACK`

**Attributes:**

- `Other("#[doc(alias = \"GTK_STYLE_PROVIDER_PRIORITY_FALLBACK\")]")`

```rust
pub const STYLE_PROVIDER_PRIORITY_FALLBACK: u32 = _;
```

### Constant `STYLE_PROVIDER_PRIORITY_THEME`

**Attributes:**

- `Other("#[doc(alias = \"GTK_STYLE_PROVIDER_PRIORITY_THEME\")]")`

```rust
pub const STYLE_PROVIDER_PRIORITY_THEME: u32 = _;
```

### Constant `STYLE_PROVIDER_PRIORITY_SETTINGS`

**Attributes:**

- `Other("#[doc(alias = \"GTK_STYLE_PROVIDER_PRIORITY_SETTINGS\")]")`

```rust
pub const STYLE_PROVIDER_PRIORITY_SETTINGS: u32 = _;
```

### Constant `STYLE_PROVIDER_PRIORITY_APPLICATION`

**Attributes:**

- `Other("#[doc(alias = \"GTK_STYLE_PROVIDER_PRIORITY_APPLICATION\")]")`

```rust
pub const STYLE_PROVIDER_PRIORITY_APPLICATION: u32 = _;
```

### Constant `STYLE_PROVIDER_PRIORITY_USER`

**Attributes:**

- `Other("#[doc(alias = \"GTK_STYLE_PROVIDER_PRIORITY_USER\")]")`

```rust
pub const STYLE_PROVIDER_PRIORITY_USER: u32 = _;
```

### Constant `ACCESSIBLE_VALUE_UNDEFINED`

**Attributes:**

- `Other("#[doc(alias = \"GTK_ACCESSIBLE_VALUE_UNDEFINED\")]")`

```rust
pub const ACCESSIBLE_VALUE_UNDEFINED: i32 = _;
```

### Constant `INVALID_LIST_POSITION`

**Attributes:**

- `Other("#[doc(alias = \"GTK_INVALID_LIST_POSITION\")]")`

```rust
pub const INVALID_LIST_POSITION: u32 = _;
```

### Constant `PRIORITY_RESIZE`

**Attributes:**

- `Other("#[doc(alias = \"GTK_PRIORITY_RESIZE\")]")`

```rust
pub const PRIORITY_RESIZE: u32 = _;
```

### Constant `TEXT_VIEW_PRIORITY_VALIDATE`

**Attributes:**

- `Other("#[doc(alias = \"GTK_TEXT_VIEW_PRIORITY_VALIDATE\")]")`

```rust
pub const TEXT_VIEW_PRIORITY_VALIDATE: u32 = _;
```

## Re-exports

### Re-export `cairo`

```rust
pub use cairo;
```

### Re-export `gdk`

```rust
pub use gdk;
```

### Re-export `gdk_pixbuf`

```rust
pub use gdk_pixbuf;
```

### Re-export `gio`

```rust
pub use gio;
```

### Re-export `glib`

```rust
pub use glib;
```

### Re-export `graphene`

```rust
pub use graphene;
```

### Re-export `gsk`

```rust
pub use gsk;
```

### Re-export `gtk4_sys`

```rust
pub use gtk4_sys as ffi;
```

### Re-export `pango`

```rust
pub use pango;
```

### Re-export `AccessibleTextRange`

**Attributes:**

- `Other("#[attr = CfgTrace([NameValue { name: \"feature\", value: Some(\"v4_14\"), span: src/lib.rs:206:7: 206:24 (#0) }])]")`
- `Other("#[attr = CfgAttrTrace]")`
- `Other("#[doc(cfg(feature = \"v4_14\"))]")`

```rust
pub use accessible_text_range::AccessibleTextRange;
```

### Re-export `BitsetIter`

```rust
pub use bitset_iter::BitsetIter;
```

### Re-export `Border`

```rust
pub use border::Border;
```

### Re-export `BuilderCScope`

```rust
pub use builder_cscope::BuilderCScope;
```

### Re-export `BuilderRustScope`

```rust
pub use builder_rust_scope::BuilderRustScope;
```

### Re-export `CssLocation`

```rust
pub use css_location::CssLocation;
```

### Re-export `Align`

```rust
pub use enums::Align;
```

### Re-export `ExpressionWatch`

```rust
pub use expression_watch::ExpressionWatch;
```

### Re-export `KeyvalTrigger`

```rust
pub use keyval_trigger::KeyvalTrigger;
```

### Re-export `MnemonicTrigger`

```rust
pub use mnemonic_trigger::MnemonicTrigger;
```

### Re-export `PadActionEntry`

```rust
pub use pad_action_entry::PadActionEntry;
```

### Re-export `PageRange`

```rust
pub use page_range::PageRange;
```

### Re-export `RecentData`

```rust
pub use recent_data::RecentData;
```

### Re-export `ResponseType`

```rust
pub use response_type::ResponseType;
```

### Re-export `TemplateChild`

```rust
pub use subclass::widget::TemplateChild;
```

### Re-export `SortColumn`

```rust
pub use tree_sortable::SortColumn;
```

### Re-export `TickCallbackId`

```rust
pub use widget::TickCallbackId;
```

### Re-export `auto::*`

```rust
pub use auto::*;
```

### Re-export `rt::*`

```rust
pub use rt::*;
```

### Re-export `functions::*`

```rust
pub use functions::*;
```

