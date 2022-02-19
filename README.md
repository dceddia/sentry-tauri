# `sentry-tauri`

A Tauri Plugin for Sentry.

Captures and reports to Sentry:

- JavaScript errors and breadcrumbs in Tauri web-views using `@sentry/browser`
- Panics and breadcrumbs in Rust via the Sentry Rust SDK
- Native crashes as minidumps
- Merges breadcrumbs from all Rust + JavaScript

## Installation

Add the plugin as a dependency in `Cargo.toml`:

```toml
[dependencies]
sentry-tauri = {git = "https://github.com/timfish/sentry-tauri"}
```

Initialise the plugin and pass it to Tauri:

```rust
fn main() {
    let (_guard, sentry) = sentry_tauri::init(
        "App Name",
        "__YOUR_DSN__",
        Some(env!("CARGO_PKG_VERSION")),
    )
    .expect("Could not start Sentry");

    tauri::Builder::default()
        .plugin(sentry)
        .run(tauri::generate_context!())
        .expect("error while running tauri application");
}
```

## What is going on here? 🤔

- Injects and initialises `@sentry/browser` in every web-view
- Includes a `TauriIntegration` that intercepts events and breadcrumbs and passes
  them to Rust via Tauri `invoke` API
- Tauri + `serde` + existing Sentry Rust types = Deserialisation mostly Just Works™️
- [`sentry_contrib_breakpad`](https://github.com/embarkstudios/sentry-contrib-rust)
  captures native crashes via breakpad and sends them via the Sentry Rust SDK

## Example App

Install dependencies:

```shell
> cd examples/basic-app
> yarn install
```

In `examples/basic-app/src-tauri/src/main.rs` replace `__YOUR_DSN__` with your DSN

Run in development mode:

```shell
> yarn tauri dev
```
