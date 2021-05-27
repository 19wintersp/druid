# Get started with Druid
*this is outdated, and should be replaced with a walkthrough of getting a simple
app built and running*.

This chapter will walk you through setting up a simple Druid application from start to finish.

## Set up a Druid project
Setting up a project is a simple as creating a new Rust project;
```bash
> cargo new druid-example
```

And then adding Druid as a dependency to Cargo.toml
```toml
[dependencies]
druid = { git = "https://github.com/linebender/druid.git" }
```

To show a minimal window with a label replace `main.rs` with this;
```rust, noplaypen
use druid::{AppLauncher, WindowDesc, Widget, PlatformError};
use druid::widget::Label;

fn build_ui() -> impl Widget<()> {
    Label::new("Hello world")
}

fn main() -> Result<(), PlatformError> {
    AppLauncher::with_window(WindowDesc::new(build_ui())).launch(())?;
    Ok(())
}
```
In our main function we create an `AppLauncher`, pass it a `WindowDesc`, and launch it. We use `build_ui` to create a tree of widgets to pass to our `WindowDesc`. For now this tree consists of one simple label widget.

This is a very simple example application and it's missing some important pieces. We will add these in the coming few paragraphs.

## Draw more widgets
The first thing we could do to make our example application more interesting is to draw more than one widget. Unfortunately `WindowDesc::new` expects a function that returns only one Widget. We also need a way to tell Druid how to lay-out our widgets.
We solve both these problems by passing in a widget-tree with one single widget at the top. Widgets can have children and widgets higher up in the tree know how to lay-out their children. That way we describe a window as a widget-tree with layout containers as the branches and widgets as the leaves. Our `build_ui` function is then responsible for building this widget tree.

To see how this works we will divide our window in four. We'll have two rows and two columns with a single label in each of the quadrants. We can lay-out our labels using the `Flex` widget.

```rust, noplaypen
fn build_ui() -> impl Widget<()> {
    Flex::row()
        .with_flex_child(
            Flex::column()
                .with_flex_child(Label::new("top left"), 1.0)
                .with_flex_child(Label::new("bottom left"), 1.0),
            1.0)
        .with_flex_child(
            Flex::column()
                .with_flex_child(Label::new("top right"), 1.0)
                .with_flex_child(Label::new("bottom right"), 1.0),
            1.0)
}
```

This looks nice but the labels on the left are drawn right against the window edge, so we needs some padding. Lets say we also want to center the two bottom labels. Unlike many other UI frameworks, widgets in Druid don't have padding or alignment properties themselves. Widgets are kept as simple as possible.

Features like padding or alignment are implemented in separate widgets. To add padding you simply wrap the labels in a `Padding` widget. Centering widgets is done using the `Align` widget set to `centered`.

```rust, noplaypen
fn build_ui() -> impl Widget<()> {
    Padding::new(
        10.0,
        Flex::row()
            .with_flex_child(
                Flex::column()
                    .with_flex_child(Label::new("top left"), 1.0)
                    .with_flex_child(Align::centered(Label::new("bottom left")), 1.0),
                1.0)
            .with_flex_child(
                Flex::column()
                    .with_flex_child(Label::new("top right"), 1.0)
                    .with_flex_child(Align::centered(Label::new("bottom right")), 1.0),
                1.0))
}
```

Do not forget to import the new widgets;
```rust, noplaypen
use druid::widget::{Label, Flex, Padding, Align};
```

## Application state
Currently, we know how to display a window and draw and position widgets in it. Now we're going to create a bigger app: a basic to-do list. To create anything in the app, we first need to define the application state.

We'll start by defining a to-do, which contains its title and whether it's completed or not. Our state should implement [the `Data` trait](data), as well as the `Clone` trait.

```rust, noplaypen
#[derive(Data, Clone)]
struct TodoItem {
    title: String,
    is_done: bool,
}
```

The main application state is simply a list of to-do items, stored in a `Vec`. However, so that comparisons are fast (see [`Data`]), we will use an `Arc<Vec>` instead. Here's the main state, deriving from `Data` and `Clone` as before:

```rust, noplaypen
#[derive(Data, Clone)]
struct TodoApp {
    items: Arc<Vec<TodoItem>>,
}
```

## Handle user input

...

## Putting it all together

...
