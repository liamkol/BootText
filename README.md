# BootText

BootText is a small Beat Saber Quest mod prototype that displays a brief, randomized startup message when the main menu opens. The message list can be customized in the source code.

> **Status: source/build kit only.** BootText has not yet been successfully compiled or tested in-headset. A successful package build would confirm the archive structure, not that the mod works at runtime.

## Target

- **Game:** Beat Saber `1.40.8_7379`
- **Platform:** Standalone Meta Quest / Android ARM64 (`arm64-v8a`)
- **Mod loader:** Scotland2
- **Dependencies:** BSML and custom-types, as declared in `qpm.json` and the QMOD metadata

## Features

- Picks a random message from a customizable list.
- Shows the message briefly when the main menu first opens after launch.
- Removes its temporary UI container after five seconds.
- Targets Quest standalone rather than the PCVR build.

## Customize the messages

Edit the `kGreetings` array in `src/main.cpp`. Keep the messages short enough to fit comfortably in the game's menu. Rebuild the project to include changes.

## Build a QMOD with GitHub Actions

1. Create a GitHub repository for this project.
2. Upload the **contents** of this folder to the repository root, including the `.github` folder. Do not upload only the ZIP file or add an extra nested project folder.
3. Open the repository's **Actions** tab and select **Build BootText QMOD**.
4. Select **Run workflow**. The workflow checks out the Quest mod template, installs the required build tools, prepares the project, builds the native library, and runs the QMOD packer.
5. When the run finishes successfully, open that run's **Artifacts** section and download `BootText-QMOD-Beat-Saber-1.40.8`. The artifact should contain `BootText.qmod`.

The workflow also runs on pushes to the `main` or `master` branch when relevant project files change. GitHub Actions must be enabled for the repository. If the run fails, open its logs and inspect the first failing step; a workflow file does not guarantee that external dependencies or APIs remain unchanged.

## Build locally

Use the [official Quest mod template](https://github.com/Lauriethefish/quest-mod-template), the [Quest modding guide](https://bsmg.wiki/modding/quest/intro.html), QPM CLI, Android NDK 26, CMake, and Ninja. Prepare a generated template project with:

```bash
scripts/apply-to-template.sh /path/to/generated/project
```

Then build from the generated project using its documented QPM/template workflow. The project helper and packer are provided in `scripts/`; run `python3 scripts/pack_qmod.py --help` to see the packer's arguments.

## Validation and limitations

The packer checks that the input library is a 64-bit little-endian AArch64 ELF, creates a QMOD ZIP with a root-level `mod.json`, and checks ZIP integrity and target game metadata. These checks do **not** prove that the code loads or behaves correctly in Beat Saber.

Before treating the mod as ready to install, confirm that:

- the native library compiles successfully for Quest ARM64;
- the QMOD package is structurally valid and its dependencies are available;
- the mod loads on the exact target version;
- the message appears in the intended position and is removed cleanly;
- repeated menu activation and normal gameplay do not cause errors or crashes.

Back up your game data and test on a modded installation of the matching game version. BootText is not an APK and does not itself install mods on the headset.

## Project files

- `src/main.cpp` — startup-message logic and customizable message list
- `include/main.hpp` — shared mod declarations
- `qpm.json` — QPM project and dependency metadata
- `mod.template.json` — QMOD manifest template
- `scripts/pack_qmod.py` — packages and structurally validates a compiled library
- `scripts/prepare_ci_project.py` — prepares the source inside the Quest mod template for CI
- `.github/workflows/build-qmod.yml` — GitHub Actions build workflow

