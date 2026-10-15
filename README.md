# protonhax

Tool to help run other programs (for example, Cheat Engine) inside a game's Proton environment.

## Usage with Steam

Set the Steam game's launch options to:

```sh
protonhax init %COMMAND%
```

In Steam: right-click the game > **Properties** > **General** > **Launch Options**.

## Usage with other launchers (experimental)

Non-Steam launchers do not necessarily pass Steam's `%COMMAND%` or expose the same environment. The script now has an explicit mode for launchers/wrappers that pass the Windows game executable and its arguments to the wrapper.

```sh
protonhax init --id <unique-id> --proton <path-to-proton-script> --prefix <path-to-prefix> -- <game.exe> [game args...]
```

- `--id`: a stable, unique identifier for this running game (letters, numbers, `_`, `.`, and `-` are supported).
- `--proton`: the executable Proton script, commonly `<Proton directory>/proton`.
- `--prefix`: the actual Wine prefix directory. For Heroic, `STEAM_COMPAT_DATA_PATH` points to the compatibility-data directory, so the prefix is usually its `pfx` subdirectory.
- Everything after `--` is the Windows executable and its arguments. The explicit mode starts it using `<path-to-proton> run ...`.

Example (replace every path and executable with values from your setup):

```sh
protonhax init --id heroic-example --proton "$HOME/.steam/steam/steamapps/common/Proton - Experimental/proton" --prefix "$HOME/Games/Heroic/Prefixes/default/GameName/pfx" -- "C:\\Games\\Example\\game.exe"
```

**Important:** this mode is experimental and is not yet verified against every Heroic version/configuration. Heroic's wrapper integration and its Proton launch path can vary by runner/version. Configure `protonhax` only in a wrapper field that actually wraps the game's launch command and passes that command as arguments. Do not put `%command%` in Heroic; it is a Steam-specific token. If Heroic is already passing a complete Proton command to the wrapper, use the ordinary `protonhax init <command> [args...]` mode instead. If the game fails to launch, first verify the wrapper's received arguments and the selected Proton/prefix paths.

The ordinary `init <command> [args...]` mode works when the launcher supplies a complete command whose arguments include an executable Proton script, and sets either `STEAM_COMPAT_DATA_PATH` or `WINEPREFIX`. Steam's existing launch-options setup remains supported.

## Commands

- List running games: `protonhax ls`
- Run a program through the game's Proton: `protonhax run <id> <path/to/program> [args...]`
- Run Windows command prompt: `protonhax cmd <id>`
- Run a native program with the game's launch environment: `protonhax exec <id> <path/to/program> [args...]`

The executable path should be supplied explicitly; these commands do not resolve programs through `PATH`.

## Installation

`protonhax` is a single Bash script. Copy it to a directory on your `PATH` and make it executable:

```sh
chmod 755 protonhax
```

Example locations include `$HOME/.local/bin/protonhax` and `/usr/local/bin/protonhax`.

### Arch Linux

- https://aur.archlinux.org/packages/protonhax
- https://aur.archlinux.org/packages/protonhax-git/

## Debugging and troubleshooting

### Enable logging

Temporarily add these lines near the top of the script:

```sh
set -x
exec >/tmp/protonhax.$$.log 2>&1
```

### PATH and sandboxing

Make sure the launcher can access the `protonhax` script and all referenced paths. Flatpak installations may have restricted access to host paths.

### Check the running game

Use `protonhax ls` to list active IDs, then try `protonhax cmd <id>` to check whether the prefix and Proton runner were detected correctly.

## Running a program before the game

If you need to replace the game's executable in Steam's launch command, see the existing discussion: https://github.com/jcnils/protonhax/issues/5#issuecomment-2053773221

## Contributing

Contributions are welcome, especially packaging and compatibility improvements.

## TODO

- [ ] Flatpak version for Steam Flatpak
- [ ] Nix packages
- [ ] Packages for other Linux distributions
- [ ] GUI
