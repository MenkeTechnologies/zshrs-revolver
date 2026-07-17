```
██████╗ ███████╗██╗   ██╗ ██████╗ ██╗    ██╗   ██╗███████╗██████╗ 
██╔══██╗██╔════╝██║   ██║██╔═══██╗██║    ██║   ██║██╔════╝██╔══██╗
██████╔╝█████╗  ██║   ██║██║   ██║██║    ██║   ██║█████╗  ██████╔╝
██╔══██╗██╔══╝  ╚██╗ ██╔╝██║   ██║██║    ╚██╗ ██╔╝██╔══╝  ██╔══██╗
██║  ██║███████╗ ╚████╔╝ ╚██████╔╝███████╗╚████╔╝ ███████╗██║  ██║
╚═╝  ╚═╝╚══════╝  ╚═══╝   ╚═════╝ ╚══════╝ ╚═══╝  ╚══════╝╚═╝  ╚═╝
                                                                  
```

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![zshrs plugin](https://img.shields.io/badge/zshrs-native%20plugin-blue.svg)](https://github.com/MenkeTechnologies/zshrs)

### `[PROGRESS SPINNER — FORK-FREE]`

> *"No fork, no statefile — just a thread."*

## `[NATIVE ZSHRS PLUGIN]`

[revolver](https://github.com/molovo/revolver) — a progress spinner for the shell — ported to a **native [zshrs](https://github.com/MenkeTechnologies/zshrs) plugin**. Instead of a shell script that forks a background process and coordinates through a statefile, the spinner is a compiled Rust builtin whose animator runs on an in-process thread.

### [`zshrs`](https://github.com/MenkeTechnologies/zshrs) &middot; [`znative`](https://github.com/MenkeTechnologies/zshrs/blob/main/docs/ZNATIVE.md) &middot; [`upstream`](https://github.com/molovo/revolver)

---

## Table of Contents

- [\[0x00\] Overview](#0x00-overview)
- [\[0x01\] Install](#0x01-install)
- [\[0x02\] Usage](#0x02-usage)
- [\[0x03\] How the port differs](#0x03-how-the-port-differs)
- [\[0xFF\] License](#0xff-license)

---

## [0x00] OVERVIEW

Start a spinner, update its message while work runs, stop it. 55 styles (`dots`, `line`, `arc`, `bouncingBall`, `pong`, `shark`, …); `revolver demo` previews them. Options: `-h`/`--help`, `-v`/`--version`, `-s`/`--style <name>`.

---

## [0x01] INSTALL

```sh
znative load MenkeTechnologies/zshrs-revolver
```

Put that one line in your `.zshrc`. [znative](https://github.com/MenkeTechnologies/zshrs/blob/main/docs/ZNATIVE.md), zshrs's package manager, installs the plugin on the first shell start — clones it, runs `cargo build --release`, and `zmodload -R`s the resulting `librevolver` — then loads it from the store, zero-network, on every start after. No separate install step.

### Manual build

```sh
cargo build --release
zmodload -R ./target/release/librevolver.dylib   # .so on Linux
revolver start 'Working…'
```

---

## [0x02] USAGE

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

---

## [0x03] HOW THE PORT DIFFERS

Upstream forks a disowned background process (`_revolver_process $PPID &!`) and coordinates through a `$REVOLVER_DIR/$PPID` statefile — the only way separate `revolver` processes can find each other. zshrs is non-forking, and does not need to fork: `start`, `update`, and `stop` are calls into one resident process, so they share memory directly. The port keeps the animator as an in-process **thread** and the running spinner in a `static` slot — no fork, no statefile, no PID handshake. Observable behavior (the CLI, all 55 styles, frames, intervals, the dim-grey message, clearing the line on stop) matches upstream.

---

## [0xFF] LICENSE

MIT. Ported from [molovo/revolver](https://github.com/molovo/revolver) (MIT). See [LICENSE](LICENSE).
