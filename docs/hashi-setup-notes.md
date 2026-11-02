# Hashi: Tauri setup notes

Saved 2 Nov 2026.

## Targets

Desktop only: macOS, Linux and Windows. No iOS or Android.
Main development machine: Intel MacBook (x86_64).

## What was done

Created the project with `pnpm create tauri-app@latest` and these choices:

- Project name: Hashi
- Package name: hashi
- Identifier: com.hashi.app
- Frontend language: TypeScript / JavaScript
- Package manager: pnpm
- UI template: Vanilla
- UI flavor: TypeScript

The template was created. The only missing dependency was Rust.

## Original terminal output (mobile lines removed)

```
➜  Hashi pnpm create tauri-app@latest
Packages are cloned from the content-addressable store to the virtual store.
  Content-addressable store is at: /Users/abdelrhman/Library/pnpm/store/v11
  Virtual store is at:             ../../../../../Library/Caches/pnpm/dlx/a87dd746bd97e93ea4d35b0f32bfe4c7/mvzosuk1-4s4/node_modules/.pnpm
.../mvzosuk1-4s4                         | Progress: resolved 2, reused 0, downloaded 2, added 2, done

dependencies:
+ create-tauri-app 4.7.5
✔ Project name · Hashi
✔ Package name · hashi
✔ Identifier · com.hashi.app
✔ Choose which language to use for your frontend · TypeScript / JavaScript - (pnpm, yarn, npm, deno, bun)
✔ Choose your package manager · pnpm
✔ Choose your UI template · Vanilla
✔ Choose your UI flavor · TypeScript

Template created!

Your system is missing dependencies (or they do not exist in $PATH):
╭──────┬───────────────────────────────────────────────────────────────────╮
│ Rust │ Visit https://www.rust-lang.org/learn/get-started#installing-rust │
╰──────┴───────────────────────────────────────────────────────────────────╯

Make sure you have installed the prerequisites for your OS: https://tauri.app/start/prerequisites/, then run:
  cd Hashi
  pnpm install

For Desktop development, run:
  pnpm tauri dev

Done in 27m 30.8s using pnpm v12.11.2
➜  Hashi
```

## Next steps (Intel Mac)

1. Install Xcode Command Line Tools (skip if already installed). Full Xcode is not needed for desktop builds:

   ```
   xcode-select --install
   ```

2. Install Rust, choose the default option, then open a new terminal:

   ```
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   rustc --version
   ```

   On an Intel Mac the default target is `x86_64-apple-darwin`, which is what you want.

3. Install dependencies and run the desktop app (the first run compiles a lot of Rust and takes several minutes):

   ```
   cd Hashi
   pnpm install
   pnpm tauri dev
   ```

## Develop without Rust

Stages 1 to 4 of the build plan (parser, renderer, split view) are plain TypeScript. Iterate in the browser with:

```
pnpm dev
```

Only start `pnpm tauri dev` at Stage 5, when you add opening and saving real files.

## Building for Linux and Windows

Tauri builds for the platform it runs on, so you can't easily produce a Windows or Linux build from your Mac. The usual solution is CI: a GitHub Actions matrix (macOS, Ubuntu, Windows) using the official `tauri-action`, which builds all three on every release. Set this up in Stage 8.

Things to remember while developing on macOS:

- Test file paths and line endings (CRLF on Windows) in Stage 5.
- Keybindings: `^` in the design means Ctrl, but on Mac people expect Cmd. Plan a mapping.
- Linux builds need system libraries (WebKitGTK and others). The Tauri prerequisites page lists them, and the CI runner installs them.

## Decisions

- Vanilla TypeScript instead of React: better learning, lowest barrier for contributors, fits the keep-it-small rule.
- Keep the engine in `src/engine/` with zero imports from the UI, with one test per rule.
- Start with a plain `<textarea>`. Swap in CodeMirror 6 later for syntax highlighting and line numbers.
- Structure the UI as small modules (`editor.ts`, `preview.ts`, `statusbar.ts`, `keys.ts`) with one shared state object.
