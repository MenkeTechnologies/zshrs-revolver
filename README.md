# zshrs-revolver

[revolver](https://github.com/molovo/revolver) — a progress spinner for the
shell — ported to a **native [zshrs](https://github.com/MenkeTechnologies/zshrs)
plugin**. Instead of a shell script that forks a background process and
coordinates through a statefile, the spinner is a compiled Rust builtin in a
`cdylib` loaded with `zmodload -R`.

## Usage

```sh
revolver start 'Working…'
# … do something …
revolver update 'Almost there…'
# … do something else …
revolver stop
```

| command            | what it does                     |
| ------------------ | -------------------------------- |
| `start <message>`  | start the spinner                |
| `update <message>` | change the message               |
| `stop`             | stop the spinner, clear the line |
| `demo`             | animate each style for 2s        |

Options: `-h`/`--help`, `-v`/`--version`, `-s`/`--style <name>` (55 styles —
`dots`, `line`, `arc`, `bouncingBall`, `pong`, `shark`, …). Run
`revolver demo` to preview them.

## Install

```sh
zpm load MenkeTechnologies/zshrs-revolver
```

Put that one line in your `.zshrc`.
[zpm](https://github.com/MenkeTechnologies/zshrs/blob/main/docs/ZPM.md),
zshrs's package manager, installs the plugin on the first shell start — clones
it, runs `cargo build --release`, and `zmodload -R`s the resulting
`librevolver` — then loads it from the store, zero-network, on every start
after. No separate install step.

### Manual build

```sh
cargo build --release
zmodload -R ./target/release/librevolver.dylib   # .so on Linux
revolver start 'Working…'
```

## How the port differs

Upstream forks a disowned background process (`_revolver_process $PPID &!`)
and coordinates through a `$REVOLVER_DIR/$PPID` statefile — the only way
separate `revolver` processes can find each other. zshrs is non-forking, and
does not need to fork: `start`, `update`, and `stop` are calls into one
resident process, so they share memory directly. The port keeps the animator
as an in-process **thread** and the running spinner in a `static` slot — no
fork, no statefile, no PID handshake. Observable behavior (the CLI, all 55
styles, frames, intervals, the dim-grey message, clearing the line on stop)
matches upstream.

## License

MIT. Ported from [molovo/revolver](https://github.com/molovo/revolver) (MIT).
See [LICENSE](LICENSE).
