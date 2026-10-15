# protonhax

Run helper programs (for example, Cheat Engine) in a game's Proton environment.

## Steam setup

Set the game's Launch Options to:

```sh
protonhax init %COMMAND%
```

## Heroic Games Launcher setup (experimental)

Heroic can pass the game's launch command to a wrapper, so you should not need to enter a Proton path, prefix path, or game ID manually.

1. Make sure `protonhax` is executable and on a path Heroic can access.
2. Open the game's settings in Heroic and find its **Wrapper command** settings.
3. Set the wrapper command/executable to the full path to `protonhax` (for example, `/home/your-user/.local/bin/protonhax`).
4. Set the wrapper arguments to `init`.
5. Save and launch the game.

Do **not** add Steam's `%COMMAND%` token in Heroic. It is specific to Steam.

When the wrapper starts, protonhax reads the launch command Heroic passes to it, detects the Proton executable, and uses `STEAM_COMPAT_DATA_PATH` to find the game's prefix. It chooses an ID from launcher-provided information or derives one from the prefix. Once the game is running, use `protonhax ls` to see its ID.

**Compatibility note:** Heroic's wrapper implementation and launch command can differ between versions and runner configurations. Automatic Proton detection is designed to handle common command layouts, but has not been verified against every Heroic setup. If startup fails, temporarily remove the wrapper to confirm the game still launches normally, then report the Heroic version and runner type.

## Commands

- List running games: `protonhax ls`
- Run a Windows program through the game's Proton: `protonhax run <id> <path/to/program.exe> [args...]`
- Open Windows command prompt: `protonhax cmd <id>`
- Run a native Linux program with the game's launch environment: `protonhax exec <id> <path/to/program> [args...]`

Use full executable paths; these commands do not resolve programs through `PATH`.

## Installation

`protonhax` is a single Bash script. Copy it to a directory on your `PATH` and make it executable:

```sh
chmod 755 protonhax
```

For example, install it at `$HOME/.local/bin/protonhax`. Make sure the launcher can access that location. Flatpak installations may need additional filesystem permissions.

## Troubleshooting

- Run `protonhax ls` while the game is running to see whether initialization succeeded.
- If the game does not launch with the wrapper enabled, remove the wrapper and confirm the game works without it.
- If `protonhax` reports that it cannot locate Proton, the launch command exposed by that launcher/runner may not include a directly detectable Proton path.

## Contributing

Contributions are welcome, especially compatibility reports and packaging for additional distributions.
