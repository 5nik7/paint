# paint

A small, dependency-light **Bash CLI** to print **styled** and **colored** text with readable tokens.

- ANSI escape codes by default (optionally `tput`)
- Token-based flags for styles + colors
- Works with pipes (stdin in/out)
- Optional **boxed** output (`--box`) with configurable border color (`--border`)
- Built-in **zsh completion** generator (`paint --completion zsh`)
- Built-in **tables** for tokens/colors (`paint -t`)

---

## Install

~~~bash
# put paint somewhere on PATH
install -m 755 paint ~/.local/bin/paint

# or:
chmod +x paint
mv paint ~/.local/bin/
~~~

---

## Usage

~~~bash
paint [options|tokens...] [--] [TEXT...]
paint [options|tokens...] -        # explicit: read stdin as text
<cmd> | paint [options|tokens...]  # filter stdin
~~~

### Common examples

~~~bash
paint red "hello"
paint underline green bold "hello"
paint red -bg green "hello"
paint bright yellow "warning!"
~~~

### Pipe / stdin examples

Colorize the output of another command:

~~~bash
date | paint blue
ls -la | paint bright cyan
~~~

Explicitly read stdin with `-` and join with a suffix:

~~~bash
echo "hello" | paint blue --join space - "world"
echo "hello" | paint blue --join newline - "world"
echo "hello" | paint blue --join none - "world"
~~~

Interpret escapes from stdin:

~~~bash
printf 'a\\nb\\n' | paint --escapes-stdin green
~~~

Interpret escapes from arguments (printf `%b` behavior):

~~~bash
paint blue "line1\nline2\n"
~~~

---

## Tokens

### Styles

These enable the matching text attribute:

- `bold`
- `dim`
- `italic`
- `underline`
- `blink`
- `reverse`
- `invis`

Order doesn’t matter:

~~~bash
paint underline green bold "text"
paint green bold underline "text"
~~~

### Foreground / background target

- `-fg` / `--foreground` (default target)
- `-bg` / `--background`

Examples:

~~~bash
paint blue "fg blue"
paint red -bg green "red fg + green bg"
paint -bg blue white "white on blue"
~~~

### Colors

Named colors and numeric aliases:

| Name / Token | Code |
|---|---:|
| `black` | `0` |
| `red` | `1` |
| `green` | `2` |
| `yellow` | `3` |
| `blue` | `4` |
| `magenta` | `5` |
| `cyan` | `6` |
| `white` | `7` |

Numeric also works:

~~~bash
paint 2 "green text"
paint -bg 4 7 "white on blue"
~~~

### Bright colors

Use `bright <color>` or direct codes `8..15`:

~~~bash
paint bright red "bright red"
paint 9 "bright red"
paint -bg bright blue white "white on bright blue"
~~~

---

## Options

### Color backend

- `--ansi` (default)
- `--tput`

### Color mode

Controls whether color escapes are emitted:

- `--color always` (default)
- `--color auto` (only when stdout is a TTY)
- `--color never`

Convenience aliases:

- `--force-color` → `--color always`
- `--no-color` → `--color never`

---

## Tables

Print formatted tables for style tokens + colors:

~~~bash
paint -t
paint --table
~~~

The tables use unicode box drawing characters when the locale is UTF-8.

---

## Box Mode

Draw a box around the rendered output.

Border color can be provided either:

1) as the token immediately after `--box`, or  
2) via `--border <color>`

Examples:

~~~bash
# border is red; text is blue on black
paint --box red -fg blue -bg black "text"

# same, using --border
paint --box --border red -fg blue -bg black "text"
~~~

Multi-line works:

~~~bash
paint --box --border bright cyan -fg white "line1\nline2\nline3"
~~~

> Note: Box borders are colored only when color output is enabled (`--color` mode allows it and the terminal supports color).

---

## Completion (zsh)

Generate the completion script:

~~~bash
paint --completion zsh > _paint
~~~

Then put `_paint` somewhere in your `$fpath`, or source it.

Example:

~~~bash
mkdir -p ~/.zsh/completions
paint --completion zsh > ~/.zsh/completions/_paint
fpath=(~/.zsh/completions $fpath)
autoload -Uz compinit && compinit
~~~

---

## License

MIT (or your preferred license — add one to the repo if needed).