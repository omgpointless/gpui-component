# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

GPUI Component is a UI component library for building desktop applications using [GPUI](https://gpui.rs), the graphics framework powering the Zed editor. The library provides 60+ cross-platform components with a modern design inspired by macOS/Windows controls and shadcn/ui.

## Build and Development Commands

```bash
# Run the main gallery application (shows all component stories)
cargo run

# Run a specific example
cargo run --example <example_name>

# Build the project
cargo build

# Run tests
cargo test

# Run tests for a specific crate
cargo test -p gpui-component

# Run a single test
cargo test <test_name>

# Check code without building
cargo check
```

### Setup Dependencies

```bash
# Linux
./script/install-linux.sh

# Windows (PowerShell)
.\script\install-window.ps1

# macOS
./script/bootstrap
```

### Performance Profiling (macOS only)

```bash
# Enable Metal HUD to see FPS
MTL_HUD_ENABLED=1 cargo run

# Use Samply for detailed profiling
samply record cargo run
```

## Architecture

### Crate Structure

- **crates/ui** (`gpui-component`): Core UI component library - the main crate users depend on
- **crates/story**: Gallery application with visual tests for all components (run with `cargo run`)
- **crates/macros** (`gpui-component-macros`): Procedural macros for the library
- **crates/assets** (`gpui-component-assets`): Asset loading utilities
- **crates/webview**: WebView component integration

### Core Concepts

**Component Pattern**: Components are stateless `RenderOnce` structs that implement GPUI's `IntoElement` trait. They use a builder pattern for configuration:

```rust
Button::new("id")
    .primary()              // Variant
    .label("Click me")
    .on_click(|_, _, _| {}) // Event handler
```

**Root Component**: Every window must have a `Root` as the top-level view. It manages Sheet, Dialog, and Notification layers:

```rust
cx.new(|cx| Root::new(your_view, window, cx))
```

**Initialization**: `gpui_component::init(cx)` must be called before using any components.

**Theme System**: Access theme via `cx.theme()` trait method (`ActiveTheme`). Theme provides colors, sizing, and styling configuration. Use `ThemeMode::Light/Dark` for mode switching.

**Size System**: Components support `Size::XSmall`, `Size::Small`, `Size::Medium` (default), `Size::Large` via the `Sizable` trait methods: `.xsmall()`, `.small()`, `.large()`.

**Styled Extensions**: Use `h_flex()` and `v_flex()` helpers for horizontal/vertical flex layouts. The `StyledExt` trait adds convenience methods to GPUI's `Styled` trait.

### Key Component Modules

- **dock**: Complex panel layout system (DockArea, DockItem, TabPanel, StackPanel, Tiles)
- **input**: Text input with LSP support, code editor capabilities, syntax highlighting
- **table**: Virtualized table with column resizing
- **list**: Virtualized list component
- **menu**: Context menus, popup menus, dropdown menus
- **chart**: Line, bar, area, pie, candlestick charts

### Story System

Stories in `crates/story/src/stories/` demonstrate component usage. Each story implements the `Story` trait:

```rust
impl Story for MyStory {
    fn title() -> &'static str { "My Component" }
    fn new_view(window: &mut Window, cx: &mut App) -> Entity<impl Render>;
}
```

## Code Style Guidelines

- Use `default` mouse cursor for buttons (not `pointer`) - this is a desktop app, not web
- Use `md` (medium) size as the default for components
- Follow existing API naming conventions inspired by macOS/Windows controls
- Reference these design systems: Apple HIG, Microsoft Fluent Design, shadcn/ui

## Feature Flags

- `tree-sitter-languages`: Enable syntax highlighting for various languages
- `inspector`: Enable GPUI inspector for debugging
- `decimal`: Enable rust_decimal support for charts
