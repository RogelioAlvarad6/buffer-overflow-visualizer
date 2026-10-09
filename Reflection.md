## The topic

We chose to visualize a **stack-based buffer overflow** because it's the example from class
that's easiest to _say_ and hardest to actually _picture_. Everyone can repeat "the input
overwrites the return address," but it wasn't until we had to lay out the stack frame —
buffer at the low addresses, then the saved frame pointer, then the saved return address
just above it — that the mechanism really clicked. Building the model forced us to answer
questions we'd been hand-waving: which direction the copy writes, why the return address is
the prize, and why exactly `strcpy()` is the villain (it keys off a null terminator in the
_source_ and never looks at the _destination's_ size).

## How building it deepened our understanding

Writing the byte-by-byte logic was the part that taught us the most. To color each cell
correctly we had to compute, for any input length and buffer size, which region each written
byte falls into. That made the boundaries concrete in a way reading about them never did —
you can see that it takes exactly `bufferSize + 1` bytes to begin corrupting the saved frame
pointer, and `bufferSize + 5` to reach the return address in our simplified 32-bit frame.
Adding the `strncpy()` toggle was almost an afterthought, but it turned the tool from "here's
the scary thing" into "here's the scary thing _and_ the one-line fix," which is the point the
class was really making about bounded copies.

## The AI-assisted workflow

We used an AI assistant (Claude) as a pair-programmer rather than a code vending machine:

1. **Framed the concept first.** We described the stack layout and the exact teaching goal —
   show overflow spilling into the frame pointer and return address, keep it safe, make it
   interactive — before any code was written.
2. **Generated a first version, then interrogated it.** The AI produced the memory diagram
   and control logic quickly, which let us spend our time on the parts that matter: checking
   that the region math was right and that the explanations were accurate, not just
   plausible.
3. **Iterated on specifics.** We asked for the buffer-size slider, the safe/unsafe toggle,
   the presets, and the live status verdicts one at a time, reviewing each change.
4. **Kept the safety line clear.** We deliberately steered away from anything resembling a
   real exploit — no shellcode, no payload construction — so the output stays an educational
   model.
5. **Had the AI draft the README and this reflection**, then edited them to match what we
   actually did.
   The biggest lesson about the workflow itself: AI is fastest when you already know what
   "correct" looks like. The code it wrote was only trustworthy because we understood the stack
   frame well enough to catch where a diagram or an explanation would have been subtly wrong.
   Using it this way made us _more_ careful about the concept, not less.
