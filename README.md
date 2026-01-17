# paint

A small, fast Bash CLI for printing **styled, colored text** — or acting as a **stdin filter** — with a simple token-based syntax.

`paint` is designed to be:

- **Composable** in pipelines (`date | paint blue`)
- **Safe** for command substitution (`var="$(paint green test)"`)
- **Portable** (Bash; works in Termux, WSL, Linux, macOS)
- **Predictable** (clean resets, no color bleeding, correct exit codes)

---

## Features

- **Foreground & background colors** with names and numeric aliases  
  `black|0 red|1 green|2 yellow|3 blue|4 magenta|5 cyan|6 white|7`
- **Bright colors**
  - `bright <color>`
  - numeric codes `8..15`
- **Text styles**
  - `bold dim italic underline blink reverse invis`
- **Targets**
  - `-fg|--foreground` (default)
  - `-bg|--background`
- **Color backend**
  - `--ansi` (default)
  - `--tput`
- **Color policy**
  - `--color always` (default)
  - `--color auto`
  - `--color never`
  - `--force-color`, `--no-color`
- **Pipeline-friendly**
  - Reads stdin automatically
  - Explicit stdin token `-`
- **Join stdin + suffix**
  - `--join newline|space|none`
  - Smart newline behavior (no double newlines)
- **Escape handling**
  - TEXT args support `printf %b` escapes (`\n`, `\t`, etc.)
  - `--escapes-stdin` for stdin
- **Color table**
  - `-t | --table`
- **Zsh completion**
  - `paint --completion zsh`

---

## Installation

### Manual (recommended)

```bash
# put paint somewhere on PATH
install -m 755 paint ~/.local/bin/paint

# or:
chmod +x paint
mv paint ~/.local/bin/
```

Ensure `~/.local/bin` is on your PATH:

```bash
export PATH="$HOME/.local/bin:$PATH"
```

### Termux

```bash
chmod +x paint
cp paint $PREFIX/bin/paint
```

---

## Usage

### Basic colors

```bash
paint red "hello"
paint blue "hello"
paint 4 "blue via numeric"
```

### Bright colors

```bash
paint bright red "bright red"
paint 9 "bright red via code"
paint bright blue "bright blue"
```

### Styles

```bash
paint bold "bold text"
paint underline green "underlined green"
paint italic dim "muted italic"
```

Order is flexible:

```bash
paint underline green bold "text"
paint green bold underline "text"
```

### Background colors

```bash
paint -bg blue white "white on blue"
paint red -bg green "red with green background"
```

Foreground is default:

```bash
paint blue "text"
```

---

## Color backend

```bash
paint --ansi green "ANSI mode (default)"
paint --tput green "tput / terminfo mode"
```

---

## Color output control

Default is `--color always` (still requires a color-capable terminal).

```bash
paint --color auto green "only colorize when stdout is a TTY"
paint --color never green "never emit color"
paint --force-color green
paint --no-color green
```

---

## Stdin & pipelines

### Filter stdin

```bash
date | paint cyan
echo "hello" | paint green
```

### Explicit stdin token (`-`)

```bash
echo "hello" | paint yellow -
```

### Join stdin + suffix

```bash
echo "hello" | paint blue --join space - "world"
# hello world

echo "hello" | paint blue --join newline - "world"
# hello
# world
```

**Smart newline behavior:**  
When using `--join newline`, if stdin already ends with `\n`, `paint` won’t add another.

---

## Escapes

### Escapes in TEXT arguments

TEXT args are printed using `printf %b`:

```bash
paint green "line1\nline2\n"
```

### Escapes from stdin

By default, stdin is raw. Enable escape interpretation:

```bash
printf 'a\\nb\\n' | paint --escapes-stdin cyan
```

---

## Color table

```bash
paint --table
# or
paint -t
```

Displays all color names, codes, and FG/BG examples  
(colors only shown if the terminal supports them).

---

## Zsh completion

Generate and install completion:

```zsh
mkdir -p ~/.zsh/completions
paint --completion zsh > ~/.zsh/completions/_paint
fpath=(~/.zsh/completions $fpath)
autoload -Uz compinit && compinit
```

Completion features:

- Stops suggesting tokens after `--`
- After `bright`, only suggests colors
- Includes all flags and modes

---

## Exit codes

- `0` — success
- `1` — error (invalid args, stdin requested but not piped, etc.)

---

## Philosophy

`paint` favors:

- explicit tokens over flags
- streaming over buffering
- clean resets (no ANSI bleeding)
- predictable behavior in scripts

---

## License

MIT

