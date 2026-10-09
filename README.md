# Stack Buffer Overflow Visualizer

An interactive, browser-based teaching tool that shows how writing past the end of a
fixed-size stack buffer corrupts adjacent memory — the **saved frame pointer** and the
**saved return address**. It is built as a single self-contained HTML/JavaScript file, so
it runs in any modern browser with no build step, no server, and no dependencies.

> **This is a teaching module, not an attack tool.** It visualizes _why_ unchecked copies
> are dangerous and _how_ the stack is laid out. It does **not** generate shellcode,
> payloads, or any working exploit.

## What it demonstrates

A classic vulnerable C pattern:

```c
void vulnerable(const char *input) {
    char buffer[8];           // fixed-size stack buffer
    strcpy(buffer, input);    // no bounds check — overflows!
}
```

`strcpy()` copies from the source until it hits a null byte, with no regard for the size of
the destination. When the input is longer than the buffer, the extra bytes keep writing
_upward_ on the stack into whatever lives next to the buffer. The visualizer makes that
spill visible, byte by byte, and colors each region so you can see exactly when the
overflow crosses from "harmless" into "control flow is now attacker-influenced."

## Interactive controls

| Control                  | What it does                                                                                                              |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------- |
| **Input box**            | The attacker-controlled string that gets copied into the buffer.                                                          |
| **Buffer size slider**   | Resize the buffer from 4–16 bytes to change how quickly it overflows.                                                     |
| **Copy-function toggle** | Switch between the unsafe `strcpy()` and the bounded `strncpy()` to see the fix live.                                     |
| **Presets**              | Jump straight to "fits exactly," "overflow the frame pointer," "overwrite the return address," or "smash past the frame." |
| **Copy / Reset**         | Apply the input or clear the buffer.                                                                                      |

As you type, the stack diagram and the plain-English status panel update in real time:
bytes written, buffer capacity, overflow size, and whether the return address is still
intact.

## How to run

**Option 1 — open it locally**
Download the repo and double-click `index.html`, or open it in any browser. That's it.

**Option 2 — GitHub Pages (live link)**
In the repo: **Settings → Pages → Build from branch → `main` / root**. After a minute the
app is live at `https://<owner>.github.io/<repo>/`.

## The safe-coding takeaway

Flip the copy function to `strncpy()` in the app and the overflow disappears — the frame
pointer and return address stay untouched. The lesson is the same one the visualization is
built around: **always bound a copy to the size of its destination.** Real-world defenses
layer on top of that (stack canaries, ASLR, non-executable stacks, and modern
`memcpy_s`/`strlcpy`-style APIs), but bounding the write is the root fix.

## Files

| File            | Purpose                                                    |
| --------------- | ---------------------------------------------------------- |
| `index.html`    | The entire application (HTML + CSS + JS, no dependencies). |
| `README.md`     | This file.                                                 |
| `reflection.md` | Our reflection on building the tool and what we learned.   |

## Course & team

Built as a class assignment on AI-assisted development of a security teaching module.

**Team**

- Rogelio Alvarado-Diaz
- Lukas Hessling
- Ambrose Schnaufer
- Marlin Crisp

## License

MIT — free to use, modify, and share for learning.
