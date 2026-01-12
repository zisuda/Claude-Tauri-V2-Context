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
