# Super Mario Land 2: 6 Golden Coins — Static Recompilation

Static recompilation of **Super Mario Land 2: 6 Golden Coins** (Nintendo, 1992) from its Game Boy cartridge to native C.
The game's SM83 machine code is translated ahead of time into C and built into
one native `rom.exe`. No emulator runs the game; a small fallback interpreter
only covers jumps the static analyser couldn't resolve.

Built on [gb-recompiled](https://github.com/sp00nznet/gb-recompiled), the same
toolchain as [Link's Awakening](https://github.com/sp00nznet/LinksAwakening),
[Pokémon](https://github.com/sp00nznet/pokemon) and
[Pokémon Gold](https://github.com/sp00nznet/pokemon-gold). Its Game Boy
siblings: [Super Mario Land](https://github.com/sp00nznet/supermarioland-recomp), [Wario Land](https://github.com/sp00nznet/warioland-recomp).

**You need your own copy of the game.** No ROM, and nothing generated from
one, is in this repository.

## Status: **v0.1.0, alpha.** Boots and plays; not yet played through.

| Stage | State |
|---|---|
| Cartridge | MBC1 + RAM + battery, 512 KB, original Game Boy (DMG) |
| Recompile | 66,878 blocks, 4,513 functions; 17 oversized functions sent to the interpreter |
| Boot | DMG power-on state (`GB_MODEL_DMG`), so the game sees an original Game Boy |
| Reached | Title screen, the A/B/C save-file pipes, the opening course: scrolling, enemies, the HUD and timer |
| Not yet checked | A full playthrough, battery saves surviving a restart, audio against the original |

What's next is in [ROADMAP.md](ROADMAP.md); changes are in [CHANGELOG.md](CHANGELOG.md).

## Screenshots

![Super Mario Land 2: 6 Golden Coins, recompiled](docs/hero.gif)

Mario running and jumping through the opening course, every frame drawn by the recompiled game.

## Getting started

You need the ROM, dumped from your own cartridge. Tested with
`Super Mario Land 2 - 6 Golden Coins.gb`, SHA-1 `bba408539ecbf8d322324956d859bc86e2a9977b`.

### Quick start

1. Download this repo: **Code → Download ZIP**, and unzip it anywhere.
2. Windows: double-click **`Setup.cmd`**. Linux and macOS: `./setup.sh`.
3. Answer its questions. It installs the compiler if it's missing (it asks
   first: MSYS2 on Windows, your package manager on Linux and macOS), looks for
   your ROM in `roms/`, Downloads, Desktop and Documents (or asks for the
   path), recompiles it, and builds the game. The first build takes a few
   minutes.
4. Windows: double-click **Super Mario Land 2 (recomp)** in the folder.
   Linux and macOS: `./build/rom`.

If it stops, it prints one line saying what to do, and the details are in
`setup.log`. Rerun it after fixing that; finished steps are skipped. To run it
without questions: `tools\setup.ps1 -Yes -Game <folder or file>` on Windows,
`./setup.sh --yes <file>` elsewhere.

### Step by step (Visual Studio)

With Visual Studio 2022 Build Tools (C++), CMake 3.21 or newer and SDL2 from
[vcpkg](https://github.com/microsoft/vcpkg) (`vcpkg install sdl2:x64-windows`):

```bash
git clone --recurse-submodules https://github.com/sp00nznet/supermarioland2-recomp.git
cd supermarioland2-recomp

# 1) build the recompiler (once)
cmake -S gbrecomp -B gbrecomp/build -DCMAKE_TOOLCHAIN_FILE=C:/vcpkg/scripts/buildsystems/vcpkg.cmake
cmake --build gbrecomp/build --target gbrecomp --config Release

# 2) recompile your ROM into generated/
mkdir generated
cp "roms/Super Mario Land 2 - 6 Golden Coins.gb" generated/rom.gb
gbrecomp/build/bin/Release/gbrecomp.exe generated/rom.gb -o generated
rm generated/CMakeLists.txt generated/rom_main.c   # this repo ships its own

# 3) build and play
cmake -S . -B build -DCMAKE_TOOLCHAIN_FILE=C:/vcpkg/scripts/buildsystems/vcpkg.cmake
cmake --build build --config Release
./build/Release/rom.exe
```

## Usage

Arrows or WASD move, Z or J is A, X or K is B, Enter is Start, Right Shift or
Backspace is Select (all rebindable). A gamepad
works too, and the menu bar has settings, scaling, palettes and save states.

| Flag | Effect |
|---|---|
| `--input "frame:buttons:duration,..."` | Scripted input (buttons `U D L R A B S T`, T is Select) |
| `--dump-frames 100,200,...` | Save those frames as `.ppm` (up to 100) |
| `--screenshot-prefix name` | File prefix for dumped frames (keep it short) |

## Layout

| Path | What it is |
|---|---|
| `Setup.cmd`, `setup.sh` | Quick start for Windows (runs `tools/setup.ps1`) and for Linux and macOS |
| `rom_main.c` | Entry point: DMG boot, the SDL window, the frame loop |
| `CMakeLists.txt` | Builds the runtime from `gbrecomp/` and the game from `generated/` |
| `gbrecomp/` | [gb-recompiled](https://github.com/sp00nznet/gb-recompiled) (submodule): recompiler and runtime |
| `generated/` | Your recompiled ROM (git-ignored, made in step 2) |

## Credits

- Recompiler and runtime: [arcanite24/gb-recompiled](https://github.com/arcanite24/gb-recompiled)
  (MIT), with our changes in [sp00nznet/gb-recompiled](https://github.com/sp00nznet/gb-recompiled).
  It is a git submodule here; none of its code is copied into this repository.
- Setup scripts follow [LinksAwakening](https://github.com/sp00nznet/LinksAwakening)'s.

Contributions are welcome: see [CONTRIBUTING.md](CONTRIBUTING.md).

## License

The code in this repository is MIT-licensed ([LICENSE](LICENSE)).

Super Mario Land 2: 6 Golden Coins is © Nintendo. This project contains no game code or data, is not
affiliated with or endorsed by Nintendo, and grants no rights to the game. You
supply your own ROM, and everything built from it stays on your machine.
