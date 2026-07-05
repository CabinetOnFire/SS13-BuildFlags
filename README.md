# SS13 Build Flags

A VS Code extension to add a checkbox picker for build/debug flags. The flag list and presets are read from the game repo's flags JSON file (`tgBuildFlags.configPath`, default `tools/build/build_flags.json`), so contributors edit flags there and this extension picks them up.

## What it does

- **A view inside the Run and Debug side panel** (same container as the launch
  config dropdown/breakpoints), not a separate tab: checkboxes grouped by
  category, each showing its define and description.
- **Preset dropdown** at the top of the view: picking a preset instantly
  checks exactly that preset's boxes. Toggling boxes by hand flips the
  dropdown back to "Custom".
- **Dependencies**: checking a flag auto-checks its `requires`;
  unchecking a requirement drops dependents; `conflictsWith` shows a warning.
- **Injection on F5**: when the resolved launch config's `preLaunchTask`
  matches `tgBuildFlags.baseTask`, the extension injects the selected flags
  and runs that task itself before launching the debugger, via one of two
  modes (`tgBuildFlags.injectionMode`):
  - `cli-args` (default): clones the base task with `-D${define}` appended to
    its command/args — matches DreamMaker's own CLI define syntax.
  - `write-file`: writes the selected `#define`s into
    `tgBuildFlags.localDefinesPath`, then runs the base task unmodified. Use
    this when the target build system has no CLI define flag at all (e.g. raw
      `dm.exe`, which only takes a `.dme` path). Only a marked block inside that
      file is ever touched, so any other hand-written content in it survives.
    With 0 flags selected, or if `tgBuildFlags.baseTask` isn't set/doesn't
    match, the extension gets out of the way entirely (extension-free parity).

## Settings

- `tgBuildFlags.configPath` — workspace-relative path to the flags JSON.
- `tgBuildFlags.baseTask` — exact task name/label to inject flags into.
- `tgBuildFlags.injectionMode` — `"cli-args"` or `"write-file"`.
- `tgBuildFlags.localDefinesPath` — workspace-relative path for `write-file` mode.

## Build / run locally

```sh
npm install
npm run compile
```

Then press **F5** in this folder to launch an Extension Development Host, open
the target game repo inside it, and open the **Run and Debug** panel. There
should be a section for the flags.