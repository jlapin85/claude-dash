# claude-dash

A one-screen control room for every Claude Code session running on your Mac —
written in ~1,300 lines of Bash.

If you run several Claude Code sessions at once (one per task, across a few
projects), the failure mode is always the same: a session stops to ask
*"may I run this command?"* and sits there politely, hand raised, while you
look at a different window. claude-dash puts the whole fleet on one screen so
you can see who's working, who's idle, and who needs a human — and answer
without switching windows.

```
HEADER   brand · fleet summary (needs-you / working / idle) · total context · clock
TABLE    one row per session: project, name, context fuel-gauge, model, age, status
PEEK     the selected session's live tail — permission prompts answerable inline
INPUT    send an instruction straight into the selected session
```

Each project gets a stable identity color so you can pick it out of the table
at a glance. Context per session renders as a thin fuel gauge — green while
healthy, amber as it gets pricey, coral when it's time to compact. Sessions
whose transcript started long ago get a dim `stale` marker (marathon sessions
bloat context and cost).

## Keys

| Key | Action |
|---|---|
| `↑/↓` or `j/k` | move the selection (peek pane follows) |
| digits + `⏎` | jump to that row's Terminal tab |
| `⏎` | attach to the selected session |
| `y` / `n` | approve / deny the selected session's permission prompt — from here |
| `s` | send an instruction to the selected session |
| `tab` | cycle to the next session that needs you |
| `v` | speak a status briefing out loud (press again / esc to stop) |
| `a` | ask a question about the fleet — by voice or text — and hear the answer |
| `?` | shortcuts panel |
| `q` | quit (sessions keep running) |

Yes, `v` talks. The dashboard reads you the state of the fleet through macOS
speech, which means the laptop across the room can tell you when something
needs a human. It's a baby monitor for the machines.

## Requirements

- **macOS.** Jump/attach/send target Terminal.app tabs (matched by tty) via
  System Events keystrokes. macOS will prompt once to enable Terminal under
  **System Settings → Privacy & Security → Accessibility**.
- **[Claude Code](https://claude.com/claude-code)** — the dashboard reads the
  per-process metadata Claude Code writes to `~/.claude/sessions/` and the
  transcripts in `~/.claude/projects/`. No API key needed for the dashboard
  itself; the `a` (ask) key shells out to `claude -p` using your existing auth.
- **jq** — `brew install jq`.
- A **truecolor terminal** (iTerm2, Ghostty, kitty, WezTerm, Terminal.app on
  recent macOS — anything advertising `COLORTERM=truecolor`).
- Stock Bash is fine (built for macOS's bash 3.2 — no bash 4 features).

## Install

```sh
git clone https://github.com/jlapin85/claude-dash.git
cd claude-dash && chmod +x claude-dash
ln -s "$PWD/claude-dash" /usr/local/bin/claude-dash   # or anywhere on your PATH
```

Run it in its own terminal window:

```sh
claude-dash          # interactive
claude-dash --once   # print one frame and exit
claude-dash --mock   # render the reference frame with demo data
```

## Tuning

A few knobs at the top of the script:

```sh
REFRESH=1        # refresh interval, seconds
BELL=1           # ring the terminal bell when a session flips to needs-you
STALE_HOURS=12   # transcripts older than this get the dim 'stale' marker
VOICE="Samantha" # `say -v '?'` to list voices
SPEECH_RATE=195  # words per minute for spoken briefings
```

Project identity colors live in `project_color()` — known folder names get
fixed palette slots; anything else hashes to a stable hue. Edit the list to
pin colors for your own project folders.

## Honestly

This was built for one desk (mine), with Claude Code, first working version in
a day. It is not a product; it's a Bash script with opinions. It assumes
Terminal.app, it drives it with AppleScript keystrokes, and it parses
transcript JSONL that Anthropic could reshape any day. Read it before you run
it — it's one file — then bend it to your setup and tell me what you turned
it into.

— [Joseph Lapin](https://josephlapin.com) · *[The Human in the Loop](https://josephlapin.com)*
