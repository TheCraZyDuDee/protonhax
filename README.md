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

For Proton games, Heroic exports `PROTONPATH` and prefix-related environment variables. protonhax now checks `PROTONPATH` first, then falls back to detecting Proton in the launch arguments. It accepts either a Steam-style compatdata directory containing `pfx/` or a prefix path supplied directly by Heroic. It chooses an ID from launcher-provided information or derives one from the prefix. Once the game is running, use `protonhax ls` to see its ID.

**Compatibility note:** Heroic's wrapper implementation and launch command can differ between versions and runner configurations. This integration remains experimental and has not been verified against every Heroic setup. If startup fails, temporarily remove the wrapper to confirm the game still launches normally, then report the Heroic version and runner type.

## Commands

- List running games: `protonhax ls`
- Run a Windows program through the game's Proton: `protonhax run <id> <path/to/program.exe> [args...]`
- Open Windows command prompt: `protonhax cmd <id>`
- Run a native Linux program with the game's launch environment: `protonhax exec <id> <path/to/program> [args...]`

Use full executable paths; these commands do not resolve programs through `PATH`.

## Logging and troubleshooting

To capture stdout and stderr from a game launched through `init`, or from helper programs launched with `run`, `cmd`, or `exec`, pass `--log <file>` before the command:

```sh
protonhax --log "$HOME/protonhax.log" run <id> "C:/path/to/program.exe"
```

Output is also shown in the terminal. The log is appended to, includes a timestamp and command type, and the launched program's exit status is preserved. Use a writable path with enough disk space; logs can contain sensitive paths, application output, or diagnostic details.

For game launch logging, add the option before `init` in Steam Launch Options, keeping Steam's command token at the end:

```sh
protonhax --log "$HOME/protonhax.log" init %COMMAND%
```

Alternatively, set `PROTONHAX_LOG` to a log-file path in the launch environment to enable logging without repeating the option. This is useful for launchers where the wrapper command is configured separately.

### Enable Proton/Wine diagnostics

Capturing stdout/stderr alone does not enable Wine's debug output. Add `--diagnostics` before the command to enable Proton's own log and selected Wine exception/unwind diagnostics:

```sh
protonhax --log "$HOME/protonhax.log" --diagnostics init %COMMAND%
```

For a helper program launched while the game is running:

```sh
protonhax --log "$HOME/protonhax-helper.log" --diagnostics run <id> "C:/path/to/program.exe"
```

This sets `PROTON_LOG=1` and defaults `WINEDEBUG` to `+timestamp,+pid,+tid,+seh,+unwind`. Proton typically writes an additional `steam-<appid>.log` file in the home directory; this is separate from the protonhax log. Non-Steam IDs and different Proton builds may affect the filename or behavior, so check the Proton log location after a test launch.

To request more Wine detail, set `PROTONHAX_WINEDEBUG` before running protonhax, for example `PROTONHAX_WINEDEBUG=+all`. **Use `+all` only for short troubleshooting sessions:** it can create very large logs and substantially slow the program. Diagnostics are opt-in because they can affect performance and produce large files.

The protonhax log captures output emitted by the launched command and Proton wrapper. It cannot automatically combine separate Proton log files or capture errors written only to separate files by the game, launcher, graphics driver, or anti-cheat system; inspect those separately when needed. Logs may contain usernames, paths, command arguments, and other sensitive details.

## Installation

`protonhax` is a single Bash script. Copy it to a directory on your `PATH` and make it executable:

```sh
chmod 755 protonhax
```

For example, install it at `$HOME/.local/bin/protonhax`. Make sure the launcher can access that location. Flatpak installations may need additional filesystem permissions.

## Troubleshooting

- Run `protonhax ls` while the game is running to see whether initialization succeeded.
- If the game does not launch with the wrapper enabled, remove the wrapper and confirm the game works without it.
- If Proton detection fails, confirm the selected Heroic runner is Proton rather than Wine.

## Contributing

Contributions are welcome, especially compatibility reports and packaging for additional distributions.
