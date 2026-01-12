# Tauri V2 Documentation Index & Context Map

This index serves as a roadmap for the Claude-Tauri-V2-Context project. It categorizes the documentation for Tauri V2 to help locate specific implementation details, security configurations, and API references efficiently.

**Instruction for AI:**

* **Analyze User Request:** Determine if the user is setting up, developing features, debugging, or configuring security.
* **Locate Section:** Use this map to find the relevant subdirectory or file.
* **Retrieve Context:** Read the specific file(s) mentioned in the section before answering.
* **Fallback:** If the specific file doesn't solve the issue, browse the parent directory of that topic.

---

## 1. Getting Started & Configuration

Use this section for project initialization, frontend framework integration, and build issues.

* **Prerequisites & Setup:**
  
  * start/prerequisites.mdx: System dependencies (Rust, Node.js, Android Studio, Xcode).
  * start/create-project.mdx: Using create-tauri-app and manual setup via CLI.
  * start/project-structure.mdx: Explains the directory layout (src-tauri, capabilities, tauri.conf.json).
* **Frontend Integration:**
  
  * start/frontend/index.mdx: Overview of SSG/SPA requirements.
  * Specific Guides: vite.mdx, nextjs.mdx, nuxt.mdx, sveltekit.mdx, qwik.mdx, leptos.mdx, trunk.mdx.
* **Configuration Files:**
  
  * develop/configuration-files.mdx: Deep dive into tauri.conf.json, Cargo.toml, and platform-specific configs.

## 2. Core Development (Rust <-> JS Interop)

Use this section for logic implementation, calling commands, events, and state management.

* **Inter-Process Communication (IPC):**
  
  * concept/inter-process-communication/: Conceptual overview of Events and Commands.
  * develop/calling-rust.mdx: **Critical.** How to create #[tauri::command], pass arguments, handle errors, and call from JS (invoke).
  * develop/calling-frontend.mdx: Sending events from Rust to JS (emit), using Channels.
* **State Management:**
  
  * develop/state-management.mdx: Managing Rust state (app.manage), Mutex, and accessing state in commands.
* **Debugging:**
  
  * develop/Debug/: Guides for VS Code, JetBrains, and identifying console errors.
* **External Binaries:**
  
  * develop/sidecar.mdx: Embedding and running external binaries (Node.js, Python, etc.) as sidecars.
  * learn/sidecar-nodejs.mdx: Specific tutorial for Node.js sidecars.

## 3. Security & Capabilities (ACL)

Use this section for permission errors, capability configuration, and scope definitions. **Tauri V2 Security is significantly different from V1.**

* **Core Concepts:**
  
  * security/capabilities.mdx: **Start Here.** Explains src-tauri/capabilities/, window mapping, and target platforms.
  * security/permissions.mdx: How permissions identify commands (e.g., fs:allow-read-text-file).
  * security/scope.mdx: Restricting access paths (Glob patterns) and URLs.
* **Reference:**
  
  * reference/acl/core-permissions.mdx: List of built-in core permissions (Window, WebView, App, Event, etc.).
* **Tutorials:**
  
  * learn/Security/: Practical guides on using and writing plugin permissions.

## 4. Plugins (Features & Recipes)

Use this section when adding specific functionality. V2 requires installing Rust crates + NPM packages + Permissions.

**Common Plugins:**

* **File System:** plugin/file-system.mdx (Read/Write files, Directories, Scopes).
* **Dialog:** plugin/dialog.mdx (Open/Save file dialogs, Message boxes).
* **Shell/Process:** plugin/shell.mdx (Spawn processes) & plugin/process.mdx (Exit/Relaunch app).
* **Window/App State:** plugin/window-state.mdx (Persist size/pos), plugin/store.mdx (Simple Key-Value store).
* **Networking:** plugin/http-client.mdx (Rust-based HTTP), plugin/websocket.mdx, plugin/upload.mdx.
* **System Integration:**
  
  * plugin/autostart.mdx: Launch on startup.
  * plugin/clipboard.mdx: Copy/Paste text.
  * plugin/notification.mdx: System notifications.
  * plugin/os-info.mdx: System version/platform.
  * plugin/updater.mdx: Self-updating mechanism.
  * plugin/single-instance.mdx: Prevent multiple app instances.
  * plugin/deep-linking.mdx: Handle custom protocols.
* **Database:** plugin/sql.mdx (SQLite, MySQL, PostgreSQL).

Note: For a full list of supported plugins and compatibility, see plugin/index.mdx.

## 5. UI & Windowing

Use this section for visual customizations.

* **Windows & Webviews:**
  
  * learn/window-customization.mdx: Custom titlebars, transparent windows, drag regions.
  * learn/window-menu.mdx: Native application menus.
  * learn/splashscreen.mdx: Creating startup splash screens.
* **System Tray:**
  
  * learn/system-tray.mdx: Creating tray icons and menus.

## 6. Testing & CI/CD

Use this section for automated testing and deployment.

* develop/Tests/mocking.mdx: Unit testing frontend with mocked Tauri APIs.
* develop/Tests/WebDriver/: End-to-end testing with Selenium/WebdriverIO.
* develop/Tests/WebDriver/ci.md: GitHub Actions configuration.

## 7. Reference

Use this section for command lookups.

* reference/\_cli.mdx & reference/\_cli\_ios.mdx: Tauri CLI command reference (dev, build, icon, etc.).
* reference/environment-variables.mdx: CI/CD env vars.

---

## Available Execution Environment

The user has granted permissions to execute the following commands in the J:\\Claude-Tauri-V2-Context directory:

* cargo build, cargo check (Rust)
* npm run build, npm install (Node)
* pnpm add, pnpm run build (Node)

When suggesting solutions, prioritize using these package managers.

---

**Example Query Resolution Flow:**

* **User:** "How do I read a file in V2?"
  
  * **Action:** Refer to plugin/file-system.mdx for code + security/capabilities.mdx for permissions configuration.
* **User:** "My frontend can't call my Rust command."
  
  * **Action:** Check develop/calling-rust.mdx for code syntax and reference/acl/core-permissions.mdx to ensure IPC is allowed.
* **User:** "How do I make the window transparent?"
  
  * **Action:** Refer to learn/window-customization.mdx.




# Upgrade from Tauri 1.0

This guide walks you through upgrading your Tauri 1.0 application to Tauri 2.0.

## [Preparing for Mobile](https://v2.tauri.app/start/migrate/from-tauri-1/#preparing-for-mobile)

The mobile interface of Tauri requires your project to output a shared library. If you are targeting mobile for your existing application, you must change your crate to produce that kind of artifact along with the desktop executable.

1. Change the Cargo manifest to produce the library. Append the following block:

src-tauri/Cargo.toml

```toml
[lib]name = "app_lib"crate-type = ["staticlib", "cdylib", "rlib"]
```

2. Rename `src-tauri/src/main.rs` to `src-tauri/src/lib.rs`. This file will be shared by both desktop and mobile targets.
3. Rename the `main` function header in `lib.rs` to the following:

src-tauri/src/lib.rs

```rust
#[cfg_attr(mobile, tauri::mobile_entry_point)]pub fn run() {    // your code here}
```

The `tauri::mobile_entry_point` macro prepares your function to be executed on mobile.

4. Recreate the `main.rs` file calling the shared run function:

src-tauri/src/main.rs

```rust
#![cfg_attr(not(debug_assertions), windows_subsystem = "windows")]
fn main() {  app_lib::run();}
```

## [Automated Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#automated-migration)

Danger

This command is not a substitude for this guide! Please read the *whole* page regardless of whether you chose to use the command.

The Tauri v2 CLI includes a `migrate` command that automates most of the process and helps you finish the migration:

* [npm](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2793)
* [yarn](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2794)
* [pnpm](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2795)
* [cargo](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2796)

```sh
npm install @tauri-apps/cli@latestnpm run tauri migrate
```

Learn more about the `migrate` command in the [Command Line Interface reference](https://v2.tauri.app/reference/cli/#migrate)

## [Summary of Changes](https://v2.tauri.app/start/migrate/from-tauri-1/#summary-of-changes)

Below is a summary of the changes from Tauri 1.0 to Tauri 2.0:

### [Tauri Configuration](https://v2.tauri.app/start/migrate/from-tauri-1/#tauri-configuration)

* `package > productName` and `package > version` moved to top-level object.
* the binary name is no longer renamed to match `productName` automatically, so you must add a `mainBinaryName` string to the top-level object matching `productName`.
* `package` removed.
* `tauri` key renamed to `app`.
* `tauri > allowlist` removed. Refer to [Migrate Permissions](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-permissions).
* `tauri > allowlist > protocol > assetScope` moved to `app > security > assetProtocol > scope`.
* `tauri > cli` moved to `plugins > cli`.
* `tauri > windows > fileDropEnabled` renamed to `app > windows > dragDropEnabled`.
* `tauri > updater > active` removed.
* `tauri > updater > dialog` removed.
* `tauri > updater` moved to `plugins > updater`.
* `bundle > createUpdaterArtifacts` added, must be set when using the app updater.
  * set it to `v1Compatible` when upgrading from v1 apps that were already distributed. See the [updater guide](https://v2.tauri.app/plugin/updater/) for more information.
* `tauri > systemTray` renamed to `app > trayIcon`.
* `tauri > pattern` moved to `app > security > pattern`.
* `tauri > bundle` moved top-level.
* `tauri > bundle > identifier` moved to top-level object.
* `tauri > bundle > dmg` moved to `bundle > macOS > dmg`
* `tauri > bundle > deb` moved to `bundle > linux > deb`
* `tauri > bundle > appimage` moved to `bundle > linux > appimage`
* `tauri > bundle > macOS > license` removed, use `bundle > licenseFile` instead.
* `tauri > bundle > windows > wix > license` removed, use `bundle > licenseFile` instead.
* `tauri > bundle > windows > nsis > license` removed, use `bundle > licenseFile` instead.
* `tauri > bundle > windows > webviewFixedRuntimePath` removed, use `bundle > windows > webviewInstallMode` instead.
* `build > withGlobalTauri` moved to `app > withGlobalTauri`.
* `build > distDir` renamed to `frontendDist`.
* `build > devPath` renamed to `devUrl`.

[Tauri 2.0 Configuration API reference](https://v2.tauri.app/reference/config/)

### [New Cargo Features](https://v2.tauri.app/start/migrate/from-tauri-1/#new-cargo-features)

* linux-protocol-body: Enables custom protocol request body parsing, allowing the IPC to use it. Requires webkit2gtk 2.40.

### [Removed Cargo Features](https://v2.tauri.app/start/migrate/from-tauri-1/#removed-cargo-features)

* reqwest-client: reqwest is now the only supported client.
* reqwest-native-tls-vendored: use `native-tls-vendored` instead.
* process-command-api: use the `shell` plugin instead (see instructions in the following section).
* shell-open-api: use the `shell` plugin instead (see instructions in the following section).
* windows7-compat: moved to the `notification` plugin.
* updater: Updater is now a plugin.
* linux-protocol-headers: Now enabled by default since we upgraded our minimum webkit2gtk version.
* system-tray: renamed to `tray-icon`.

### [Rust Crate Changes](https://v2.tauri.app/start/migrate/from-tauri-1/#rust-crate-changes)

* `api` module removed. Each API module can be found in a Tauri plugin.
* `api::dialog` module removed. Use `tauri-plugin-dialog` instead. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-dialog-plugin)
* `api::file` module removed. Use Rust’s [`std::fs`](https://doc.rust-lang.org/std/fs/) instead.
* `api::http` module removed. Use `tauri-plugin-http` instead. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-http-plugin)
* `api::ip` module rewritten and moved to `tauri::ipc`. Check out the new APIs, specially `tauri::ipc::Channel`.
* `api::path` module functions and `tauri::PathResolved` moved to `tauri::Manager::path`. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-path-to-tauri-manager)
* `api::process::Command`, `tauri::api::shell` and `tauri::Manager::shell_scope` APIs removed. Use `tauri-plugin-shell` instead. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-shell-plugin)
* `api::process::current_binary` and `tauri::api::process::restart` moved to `tauri::process`.
* `api::version` module has been removed. Use the [semver crate](https://docs.rs/semver/latest/semver/) instead.
* `App::clipboard_manager` and `AppHandle::clipboard_manager` removed. Use `tauri-plugin-clipboard` instead. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-clipboard-plugin)
* `App::get_cli_matches` removed. Use `tauri-plugin-cli` instead. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-cli-plugin)
* `App::global_shortcut_manager` and `AppHandle::global_shortcut_manager` removed. Use `tauri-plugin-global-shortcut` instead. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-global-shortcut-plugin)
* `Manager::fs_scope` removed. The file system scope can be accessed via `tauri_plugin_fs::FsExt`.
* `Plugin::PluginApi` now receives a plugin configuration as a second argument.
* `Plugin::setup_with_config` removed. Use the updated `tauri::Plugin::PluginApi` instead.
* `scope::ipc::RemoteDomainAccessScope::enable_tauri_api` and `scope::ipc::RemoteDomainAccessScope::enables_tauri_api` removed. Enable each core plugin individually via `scope::ipc::RemoteDomainAccessScope::add_plugin` instead.
* `scope::IpcScope` removed, use `scope::ipc::Scope` instead.
* `scope::FsScope`, `scope::GlobPattern` and `scope::FsScopeEvent` removed, use `scope::fs::Scope`, `scope::fs::Pattern` and `scope::fs::Event` respectively.
* `updater` module removed. Use `tauri-plugin-updater` instead. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-updater-plugin)
* `Env.args` field has been removed, use `Env.args_os` field instead.
* `Menu`, `MenuEvent`, `CustomMenuItem`, `Submenu`, `WindowMenuEvent`, `MenuItem` and `Builder::on_menu_event` APIs removed. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-menu)
* `SystemTray`, `SystemTrayHandle`, `SystemTrayMenu`, `SystemTrayMenuItemHandle`, `SystemTraySubmenu`, `MenuEntry` and `SystemTrayMenuItem` APIs removed. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-tray-icon-module)

### [JavaScript API Changes](https://v2.tauri.app/start/migrate/from-tauri-1/#javascript-api-changes)

The `@tauri-apps/api` package no longer provides non-core modules. Only the previous `tauri` (now `core`), `path`, `event` and `window` modules are exported. All others have been moved to plugins.

* `@tauri-apps/api/tauri` module renamed to `@tauri-apps/api/core`. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-core-module)
* `@tauri-apps/api/cli` module removed. Use `@tauri-apps/plugin-cli` instead. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-cli-plugin)
* `@tauri-apps/api/clipboard` module removed. Use `@tauri-apps/plugin-clipboard` instead. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-clipboard-plugin)
* `@tauri-apps/api/dialog` module removed. Use `@tauri-apps/plugin-dialog` instead. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-dialog-plugin)
* `@tauri-apps/api/fs` module removed. Use `@tauri-apps/plugin-fs` instead. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-file-system-plugin)
* `@tauri-apps/api/global-shortcut` module removed. Use `@tauri-apps/plugin-global-shortcut` instead. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-global-shortcut-plugin)
* `@tauri-apps/api/http` module removed. Use `@tauri-apps/plugin-http` instead. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-http-plugin)
* `@tauri-apps/api/os` module removed. Use `@tauri-apps/plugin-os` instead. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-os-plugin)
* `@tauri-apps/api/notification` module removed. Use `@tauri-apps/plugin-notification` instead. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-notification-plugin)
* `@tauri-apps/api/process` module removed. Use `@tauri-apps/plugin-process` instead. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-process-plugin)
* `@tauri-apps/api/shell` module removed. Use `@tauri-apps/plugin-shell` instead. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-shell-plugin)
* `@tauri-apps/api/updater` module removed. Use `@tauri-apps/plugin-updater` instead [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-updater-plugin)
* `@tauri-apps/api/window` module renamed to `@tauri-apps/api/webviewWindow`. [Migration](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-new-window-api)

The v1 plugins are now published as `@tauri-apps/plugin-<plugin-name>`. Previously they were available from git as `tauri-plugin-<plugin-name>-api`.

### [Environment Variables Changes](https://v2.tauri.app/start/migrate/from-tauri-1/#environment-variables-changes)

Most of the environment variables read and written by the Tauri CLI were renamed for consistency and prevention of mistakes:

* `TAURI_PRIVATE_KEY` -> `TAURI_SIGNING_PRIVATE_KEY`
* `TAURI_KEY_PASSWORD` -> `TAURI_SIGNING_PRIVATE_KEY_PASSWORD`
* `TAURI_SKIP_DEVSERVER_CHECK` -> `TAURI_CLI_NO_DEV_SERVER_WAIT`
* `TAURI_DEV_SERVER_PORT` -> `TAURI_CLI_PORT`
* `TAURI_PATH_DEPTH` -> `TAURI_CLI_CONFIG_DEPTH`
* `TAURI_FIPS_COMPLIANT` -> `TAURI_BUNDLER_WIX_FIPS_COMPLIANT`
* `TAURI_DEV_WATCHER_IGNORE_FILE` -> `TAURI_CLI_WATCHER_IGNORE_FILENAME`
* `TAURI_TRAY` -> `TAURI_LINUX_AYATANA_APPINDICATOR`
* `TAURI_APPLE_DEVELOPMENT_TEAM` -> `APPLE_DEVELOPMENT_TEAM`
* `TAURI_PLATFORM` -> `TAURI_ENV_PLATFORM`
* `TAURI_ARCH` -> `TAURI_ENV_ARCH`
* `TAURI_FAMILY` -> `TAURI_ENV_FAMILY`
* `TAURI_PLATFORM_VERSION` -> `TAURI_ENV_PLATFORM_VERSION`
* `TAURI_PLATFORM_TYPE` -> `TAURI_ENV_PLATFORM_TYPE`
* `TAURI_DEBUG` -> `TAURI_ENV_DEBUG`

### [Event System](https://v2.tauri.app/start/migrate/from-tauri-1/#event-system)

The event system was redesigned to be easier to use. Instead of relying on the source of the event, it now has a simpler implementation that relies on event targets.

* The `emit` function now emits the event to all event listeners.
* Added a new `emit_to`/`emitTo` function to trigger an event to a specific target.
* `emit_filter` now filters based on [`EventTarget`](https://docs.rs/tauri/2.0.0/tauri/event/enum.EventTarget.html) instead of a window.
* Renamed `listen_global` to `listen_any`. It now listens to all events regardless of their filters and targets.
* JavaScript: `event.listen()` behaves similar to `listen_any`. It now listens to all events regardless of their filters and targets, unless a target is set in the `Options`.
* JavaScript: `WebviewWindow.listen` etc. only listen to events emitted to the respective `EventTarget`.

### [Multiwebview support](https://v2.tauri.app/start/migrate/from-tauri-1/#multiwebview-support)

Tauri v2 introduces multiwebview support currently behind an `unstable` feature flag. In order to support it, we renamed the Rust `Window` type to `WebviewWindow` and the Manager `get_window` function to `get_webview_window`.

The `WebviewWindow` JS API type is now re-exported from `@tauri-apps/api/webviewWindow` instead of `@tauri-apps/api/window`.

### [New origin URL on Windows](https://v2.tauri.app/start/migrate/from-tauri-1/#new-origin-url-on-windows)

On Windows the frontend files in production apps are now hosted on `http://tauri.localhost` instead of `https://tauri.localhost`. Because of this IndexedDB, LocalStorage and Cookies will be reset unless `dangerousUseHttpScheme` was used in v1. To prevent this you can set `app > windows > useHttpsScheme` to `true` or use `WebviewWindowBuilder::use_https_scheme` to keep using the `https` scheme.

## [Detailed Migration Steps](https://v2.tauri.app/start/migrate/from-tauri-1/#detailed-migration-steps)

Common scenarios you may encounter when migrating your Tauri 1.0 app to Tauri 2.0.

### [Migrate to Core Module](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-core-module)

The `@tauri-apps/api/tauri` module was renamed to `@tauri-apps/api/core`. Simply rename the module import:

```diff
import { invoke } from "@tauri-apps/api/tauri"import { invoke } from "@tauri-apps/api/core"
```

### [Migrate to CLI Plugin](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-cli-plugin)

The Rust `App::get_cli_matches` JavaScript `@tauri-apps/api/cli` APIs have been removed. Use the `@tauri-apps/plugin-cli` plugin instead:

1. Add to cargo dependencies:

Cargo.toml

```toml
[dependencies]tauri-plugin-cli = "2"
```

2. Use in JavaScript or Rust project:

* [JavaScript](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2771)
* [Rust](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2772)

```rust
fn main() {    tauri::Builder::default()        .plugin(tauri_plugin_cli::init())}
```

package.json

```json
{  "dependencies": {    "@tauri-apps/plugin-cli": "^2.0.0"  }}
```

```javascript
import { getMatches } from '@tauri-apps/plugin-cli';const matches = await getMatches();
```

### [Migrate to Clipboard Plugin](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-clipboard-plugin)

The Rust `App::clipboard_manager` and `AppHandle::clipboard_manager` and JavaScript `@tauri-apps/api/clipboard` APIs have been removed. Use the `@tauri-apps/plugin-clipboard-manager` plugin instead:

```toml
[dependencies]tauri-plugin-clipboard-manager = "2"
```

* [JavaScript](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2773)
* [Rust](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2774)

```rust
fn main() {    tauri::Builder::default()        .plugin(tauri_plugin_clipboard_manager::init())}
```

package.json

```json
{  "dependencies": {    "@tauri-apps/plugin-clipboard-manager": "^2.0.0"  }}
```

```javascript
import { writeText, readText } from '@tauri-apps/plugin-clipboard-manager';await writeText('Tauri is awesome!');assert(await readText(), 'Tauri is awesome!');
```

### [Migrate to Dialog Plugin](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-dialog-plugin)

The Rust `tauri::api::dialog` JavaScript `@tauri-apps/api/dialog` APIs have been removed. Use the `@tauri-apps/plugin-dialog` plugin instead:

1. Add to cargo dependencies:

Cargo.toml

```toml
[dependencies]tauri-plugin-dialog = "2"
```

2. Use in JavaScript or Rust project:

* [JavaScript](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2775)
* [Rust](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2776)

```rust
fn main() {    tauri::Builder::default()        .plugin(tauri_plugin_dialog::init())}
```

package.json

```json
{  "dependencies": {    "@tauri-apps/plugin-dialog": "^2.0.0"  }}
```

```javascript
import { save } from '@tauri-apps/plugin-dialog';const filePath = await save({  filters: [    {      name: 'Image',      extensions: ['png', 'jpeg'],    },  ],});
```

### [Migrate to File System Plugin](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-file-system-plugin)

The Rust `App::get_cli_matches` JavaScript `@tauri-apps/api/fs` APIs have been removed. Use the [`std::fs`](https://doc.rust-lang.org/std/fs/) for Rust and `@tauri-apps/plugin-fs` plugin for JavaScript instead:

1. Add to cargo dependencies:

Cargo.toml

```toml
[dependencies]tauri-plugin-fs = "2"
```

2. Use in JavaScript or Rust project:

* [JavaScript](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2777)
* [Rust](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2778)

```rust
fn main() {    tauri::Builder::default()        .plugin(tauri_plugin_fs::init())}
```

package.json

```json
{  "dependencies": {    "@tauri-apps/plugin-fs": "^2.0.0"  }}
```

```javascript
import { mkdir, BaseDirectory } from '@tauri-apps/plugin-fs';await mkdir('db', { baseDir: BaseDirectory.AppLocalData });
```

Some functions and types have been renamed or removed:

* `Dir` enum alias removed, use `BaseDirectory`.
* `FileEntry`, `FsBinaryFileOption`, `FsDirOptions`, `FsOptions`, `FsTextFileOption` and `BinaryFileContents` interfaces and type aliases have been removed and replaced with new interfaces suited for each function.
* `createDir` renamed to `mkdir`.
* `readBinaryFile` renamed to `readFile`.
* `removeDir` removed and replaced with `remove`.
* `removeFile` removed and replaced with `remove`.
* `renameFile` removed and replaced with `rename`.
* `writeBinaryFile` renamed to `writeFile`.

### [Migrate to Global Shortcut Plugin](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-global-shortcut-plugin)

The Rust `App::global_shortcut_manager` and `AppHandle::global_shortcut_manager` and JavaScript `@tauri-apps/api/global-shortcut` APIs have been removed. Use the `@tauri-apps/plugin-global-shortcut` plugin instead:

1. Add to cargo dependencies:

Cargo.toml

```toml
[dependencies][target."cfg(not(any(target_os = \"android\", target_os = \"ios\")))".dependencies]tauri-plugin-global-shortcut = "2"
```

2. Use in JavaScript or Rust project:

* [JavaScript](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2779)
* [Rust](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2780)

```rust
fn main() {    tauri::Builder::default()        .plugin(tauri_plugin_global_shortcut::Builder::default().build())}
```

package.json

```json
{  "dependencies": {    "@tauri-apps/plugin-global-shortcut": "^2.0.0"  }}
```

```javascript
import { register } from '@tauri-apps/plugin-global-shortcut';await register('CommandOrControl+Shift+C', () => {  console.log('Shortcut triggered');});
```

### [Migrate to HTTP Plugin](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-http-plugin)

The Rust `tauri::api::http` JavaScript `@tauri-apps/api/http` APIs have been removed. Use the `@tauri-apps/plugin-http` plugin instead:

1. Add to cargo dependencies:

Cargo.toml

```toml
[dependencies]tauri-plugin-http = "2"
```

2. Use in JavaScript or Rust project:

* [JavaScript](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2781)
* [Rust](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2782)

```rust
fn main() {    tauri::Builder::default()        .plugin(tauri_plugin_http::init())}
```

package.json

```json
{  "dependencies": {    "@tauri-apps/plugin-http": "^2.0.0"  }}
```

```javascript
import { fetch } from '@tauri-apps/plugin-http';const response = await fetch(  'https://raw.githubusercontent.com/tauri-apps/tauri/dev/package.json');
```

### [Migrate to Notification Plugin](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-notification-plugin)

The Rust `tauri::api::notification` JavaScript `@tauri-apps/api/notification` APIs have been removed. Use the `@tauri-apps/plugin-notification` plugin instead:

1. Add to cargo dependencies:

Cargo.toml

```toml
[dependencies]tauri-plugin-notification = "2"
```

2. Use in JavaScript or Rust project:

* [JavaScript](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2783)
* [Rust](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2784)

```rust
fn main() {    tauri::Builder::default()        .plugin(tauri_plugin_notification::init())}
```

package.json

```json
{  "dependencies": {    "@tauri-apps/plugin-notification": "^2.0.0"  }}
```

```javascript
import { sendNotification } from '@tauri-apps/plugin-notification';sendNotification('Tauri is awesome!');
```

### [Migrate to Menu Module](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-menu-module)

The Rust `Menu` APIs were moved to the `tauri::menu` module and refactored to use the [muda crate](https://github.com/tauri-apps/muda).

#### [Use `tauri::menu::MenuBuilder`](https://v2.tauri.app/start/migrate/from-tauri-1/#use-taurimenumenubuilder)

Use `tauri::menu::MenuBuilder` instead of `tauri::Menu`. Note that its constructor takes a Manager instance (one of `App`, `AppHandle` or `WebviewWindow`) as an argument:

```rust
use tauri::menu::MenuBuilder;
tauri::Builder::default()    .setup(|app| {        let menu = MenuBuilder::new(app)            .copy()            .paste()            .separator()            .undo()            .redo()            .text("open-url", "Open URL")            .check("toggle", "Toggle")            .icon("show-app", "Show App", app.default_window_icon().cloned().unwrap())            .build()?;        app.set_menu(menu);        Ok(())    })
```

#### [Use `tauri::menu::PredefinedMenuItem`](https://v2.tauri.app/start/migrate/from-tauri-1/#use-taurimenupredefinedmenuitem)

Use `tauri::menu::PredefinedMenuItem` instead of `tauri::MenuItem`:

```rust
use tauri::menu::{MenuBuilder, PredefinedMenuItem};
tauri::Builder::default()    .setup(|app| {        let menu = MenuBuilder::new(app).item(&PredefinedMenuItem::copy(app)?).build()?;        Ok(())    })
```

Tip

The menu builder has dedicated methods to add each predefined menu item so you can call `.copy()` instead of `.item(&PredefinedMenuItem::copy(app, None)?)`.

#### [Use `tauri::menu::MenuItemBuilder`](https://v2.tauri.app/start/migrate/from-tauri-1/#use-taurimenumenuitembuilder)

Use `tauri::menu::MenuItemBuilder` instead of `tauri::CustomMenuItem`:

```rust
use tauri::menu::MenuItemBuilder;
tauri::Builder::default()    .setup(|app| {        let toggle = MenuItemBuilder::new("Toggle").accelerator("Ctrl+Shift+T").build(app)?;        Ok(())    })
```

#### [Use `tauri::menu::SubmenuBuilder`](https://v2.tauri.app/start/migrate/from-tauri-1/#use-taurimenusubmenubuilder)

Use `tauri::menu::SubmenuBuilder` instead of `tauri::Submenu`:

```rust
use tauri::menu::{MenuBuilder, SubmenuBuilder};
tauri::Builder::default()    .setup(|app| {        let submenu = SubmenuBuilder::new(app, "Sub")            .text("Tauri")            .separator()            .check("Is Awesome")            .build()?;        let menu = MenuBuilder::new(app).item(&submenu).build()?;        Ok(())    })
```

`tauri::Builder::menu` now takes a closure because the menu needs a Manager instance to be built. See [the documentation](https://docs.rs/tauri/2.0.0/tauri/struct.Builder.html#method.menu) for more information.

#### [Menu Events](https://v2.tauri.app/start/migrate/from-tauri-1/#menu-events)

The Rust `tauri::Builder::on_menu_event` API was removed. Use `tauri::App::on_menu_event` or `tauri::AppHandle::on_menu_event` instead:

```rust
use tauri::menu::{CheckMenuItemBuilder, MenuBuilder, MenuItemBuilder};
tauri::Builder::default()    .setup(|app| {        let toggle = MenuItemBuilder::with_id("toggle", "Toggle").build(app)?;        let check = CheckMenuItemBuilder::new("Mark").build(app)?;        let menu = MenuBuilder::new(app).items(&[&toggle, &check]).build()?;
        app.set_menu(menu)?;
        app.on_menu_event(move |app, event| {            if event.id() == check.id() {                println!("`check` triggered, do something! is checked? {}", check.is_checked().unwrap());            } else if event.id() == "toggle" {                println!("toggle triggered!");            }        });        Ok(())    })
```

Note that there are two ways to check which menu item was selected: move the item to the event handler closure and compare IDs, or define a custom ID for the item through the `with_id` constructor and use that ID string to compare.

Tip

Menu items can be shared across menus, and the menu event is bound to a menu item instead of a menu or window. If you don’t want all listeners to be triggered when a menu item is selected, do not share menu items and use dedicated instances instead, that you could move into `tauri::WebviewWindow/WebviewWindowBuilder::on_menu_event` closure.

### [Migrate to OS Plugin](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-os-plugin)

The Rust `tauri::api::os` JavaScript `@tauri-apps/api/os` APIs have been removed. Use the `@tauri-apps/plugin-os` plugin instead:

1. Add to cargo dependencies:

Cargo.toml

```toml
[dependencies]tauri-plugin-os = "2"
```

2. Use in JavaScript or Rust project:

* [JavaScript](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2785)
* [Rust](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2786)

```rust
fn main() {    tauri::Builder::default()        .plugin(tauri_plugin_os::init())}
```

package.json

```json
{  "dependencies": {    "@tauri-apps/plugin-os": "^2.0.0"  }}
```

```javascript
import { arch } from '@tauri-apps/plugin-os';const architecture = await arch();
```

### [Migrate to Process Plugin](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-process-plugin)

The Rust `tauri::api::process` JavaScript `@tauri-apps/api/process` APIs have been removed. Use the `@tauri-apps/plugin-process` plugin instead:

1. Add to cargo dependencies:

Cargo.toml

```toml
[dependencies]tauri-plugin-process = "2"
```

2. Use in JavaScript or Rust project:

* [JavaScript](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2787)
* [Rust](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2788)

```rust
fn main() {    tauri::Builder::default()        .plugin(tauri_plugin_process::init())}
```

package.json

```json
{  "dependencies": {    "@tauri-apps/plugin-process": "^2.0.0"  }}
```

```javascript
import { exit, relaunch } from '@tauri-apps/plugin-process';await exit(0);await relaunch();
```

### [Migrate to Shell Plugin](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-shell-plugin)

The Rust `tauri::api::shell` JavaScript `@tauri-apps/api/shell` APIs have been removed. Use the `@tauri-apps/plugin-shell` plugin instead:

1. Add to cargo dependencies:

Cargo.toml

```toml
[dependencies]tauri-plugin-shell = "2"
```

2. Use in JavaScript or Rust project:

* [JavaScript](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2789)
* [Rust](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2790)

```rust
fn main() {    tauri::Builder::default()        .plugin(tauri_plugin_shell::init())}
```

package.json

```json
{  "dependencies": {    "@tauri-apps/plugin-shell": "^2.0.0"  }}
```

```javascript
import { Command, open } from '@tauri-apps/plugin-shell';const output = await Command.create('echo', 'message').execute();
await open('https://github.com/tauri-apps/tauri');
```

### [Migrate to Tray Icon Module](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-tray-icon-module)

The Rust `SystemTray` APIs were renamed to `TrayIcon` for consistency. The new APIs can be found in the Rust `tray` module.

#### [Use `tauri::tray::TrayIconBuilder`](https://v2.tauri.app/start/migrate/from-tauri-1/#use-tauritraytrayiconbuilder)

Use `tauri::tray::TrayIconBuilder` instead of `tauri::SystemTray`:

```rust
let tray = tauri::tray::TrayIconBuilder::with_id("my-tray").build(app)?;
```

See [TrayIconBuilder](https://docs.rs/tauri/2.0.0/tauri/tray/struct.TrayIconBuilder.html) for more information.

#### [Migrate to Menu](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-menu)

Use `tauri::menu::Menu` instead of `tauri::SystemTrayMenu`, `tauri::menu::Submenu` instead of `tauri::SystemTraySubmenu` and `tauri::menu::PredefinedMenuItem` instead of `tauri::SystemTrayMenuItem`.

#### [Tray Events](https://v2.tauri.app/start/migrate/from-tauri-1/#tray-events)

`tauri::SystemTray::on_event` have been split into `tauri::tray::TrayIconBuilder::on_menu_event` and `tauri::tray::TrayIconBuilder::on_tray_icon_event`:

```rust
use tauri::{    menu::{MenuBuilder, MenuItemBuilder},    tray::{MouseButton, MouseButtonState, TrayIconBuilder, TrayIconEvent},};
tauri::Builder::default()    .setup(|app| {        let toggle = MenuItemBuilder::with_id("toggle", "Toggle").build(app)?;        let menu = MenuBuilder::new(app).items(&[&toggle]).build()?;        let tray = TrayIconBuilder::new()            .menu(&menu)            .on_menu_event(move |app, event| match event.id().as_ref() {                "toggle" => {                    println!("toggle clicked");                }                _ => (),            })            .on_tray_icon_event(|tray, event| {                if let TrayIconEvent::Click {                        button: MouseButton::Left,                        button_state: MouseButtonState::Up,                        ..                } = event                {                    let app = tray.app_handle();                    if let Some(webview_window) = app.get_webview_window("main") {                       let _ = webview_window.unminimize();                       let _ = webview_window.show();                       let _ = webview_window.set_focus();                    }                }            })            .build(app)?;
        Ok(())    })
```

### [Migrate to Updater Plugin](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-updater-plugin)

Change of default behavior

The built-in dialog with an automatic update check was removed, use the Rust and JS APIs to check for and install updates instead. Failing to do so will prevent your users from getting further updates!

The Rust `tauri::updater` and JavaScript `@tauri-apps/api-updater` APIs have been removed. To set a custom updater target with the `@tauri-apps/plugin-updater`:

1. Add to cargo dependencies:

```toml
[dependencies]tauri-plugin-updater = "2"
```

2. Use in JavaScript or Rust project:

* [JavaScript](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2791)
* [Rust](https://v2.tauri.app/start/migrate/from-tauri-1/#tab-panel-2792)

```rust
fn main() {    tauri::Builder::default()        .plugin(tauri_plugin_updater::Builder::new().build())}
```

package.json

```json
{  "dependencies": {    "@tauri-apps/plugin-updater": "^2.0.0"  }}
```

```javascript
import { check } from '@tauri-apps/plugin-updater';import { relaunch } from '@tauri-apps/plugin-process';
const update = await check();if (update?.available) {  console.log(`Update to ${update.version} available! Date: ${update.date}`);  console.log(`Release notes: ${update.body}`);  await update.downloadAndInstall();  // requires the `process` plugin  await relaunch();}
```

### [Migrate Path to Tauri Manager](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-path-to-tauri-manager)

The Rust `tauri::api::path` module functions and `tauri::PathResolver` have been moved to `tauri::Manager::path`:

```rust
use tauri::{path::BaseDirectory, Manager};
tauri::Builder::default()    .setup(|app| {        let home_dir_path = app.path().home_dir().expect("failed to get home dir");
        let path = app.path().resolve("path/to/something", BaseDirectory::Config)?;
        Ok(())  })
```

### [Migrate to new Window API](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-new-window-api)

On the Rust side, `Window` was renamed to `WebviewWindow`, its builder `WindowBuilder` is now named `WebviewWindowBuilder` and `WindowUrl` is now named `WebviewUrl`.

Additionally, the `Manager::get_window` function was renamed to `get_webview_window` and the window’s `parent_window` API was renamed to `parent_raw` to support a high level window parent API.

On the JavaScript side, the `WebviewWindow` class is now exported in the `@tauri-apps/api/webviewWindow` path.

The `onMenuClicked` function was removed, you can intercept menu events when creating a menu in JavaScript instead.

### [Migrate Embedded Additional Files (Resources)](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-embedded-additional-files-resources)

On the JavaScript side, make sure you [Migrate to File System Plugin](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-file-system-plugin). Additionally, note the changes made to the v1 allowlist in [Migrate Permissions](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-permissions).

On the Rust side, make sure you [Migrate Path to Tauri Manager](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-path-to-tauri-manager).

### [Migrate Embedded External Binaries (Sidecar)](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-embedded-external-binaries-sidecar)

In Tauri v1, the external binaries and their arguments were defined in the allowlist. In v2, use the new permissions system. Read [Migrate Permissions](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-permissions) for more information.

On the JavaScript side, make sure you [Migrate to Shell Plugin](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-to-shell-plugin).

On the Rust side, `tauri::api::process` API has been removed. Use `tauri_plugin_shell::ShellExt` and `tauri_plugin_shell::process::CommandEvent` APIs instead. Read the [Embedding External Binaries](https://v2.tauri.app/develop/sidecar/#running-it-from-rust) guide to see how.

The “process-command-api” features flag has been removed in v2. So running the external binaries does not require this feature to be defined in the Tauri config anymore.

### [Migrate Permissions](https://v2.tauri.app/start/migrate/from-tauri-1/#migrate-permissions)

The v1 allowlist have been rewritten to a completely new system for permissions that works for individual plugins and is much more configurable for multiwindow and remote URL support. This new system works like an access control list (ACL) where you can allow or deny commands, allocate permissions to a specific set of windows and domains, and define access scopes.

To enable permissions for your app, you must create capability files inside the `src-tauri/capabilities` folder, and Tauri will automatically configure everything else for you.

The `migrate` CLI command automatically parses your v1 allowlist and generates the associated capability file.

To learn more about permissions and capabilities, see [the security documentation](https://v2.tauri.app/security/).
