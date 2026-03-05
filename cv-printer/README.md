# Printer CV — Scroll-to-Print Prompt Test

A single-page animated printer simulation built entirely with HTML, CSS, and vanilla JavaScript. Scrolling feeds a sheet of paper out of a printer, revealing a CV line-by-line — just like watching a real printer at work.

This project was created as a **prompt-driven development test** using GitHub Copilot (Claude Opus 4.6), built from a single natural-language description and refined through iterative conversation.

---

## Demo

| Version | Preview |
|---------|---------|
| v0.1 — Initial top-down feed | ![v0.1](demo/test_v.01.gif) |
| v1.0 — Realistic bottom-up feed | ![v1.0](demo/test_v.1.0.gif) |

> Full video: [`demo/test_v1.mp4`](demo/test_v1.mp4)

---

## How It Works

The page has a tall invisible scroll spacer. A fixed-position printer scene sits on top. As you scroll:

1. **Paper feeds out** of the printer slot — the clipping container grows, revealing content from the bottom up.
2. **Content prints in order** — the CV header exits the printer first and slides downward, just like a face-up output tray. Each section fades in as it clears the print head.
3. **Scroll back up** — the paper retracts smoothly back into the printer.

### Key Details

- **Print head** — an animated blue sweep line appears at the printing edge while scrolling
- **Status LED** — pulses amber during active printing, returns to green when idle
- **Progress bar** — thin gradient bar at the top of the viewport
- **Responsive** — scales down for mobile screens
- **Zero dependencies** — pure HTML, CSS, and JS in a single file

---

## Prompt Engineering Notes

### Initial prompt
The full prompt used to kick off the build: [`PROMPT.MD`](PROMPT.MD)

It described:
- Visual requirements (printer body, paper slot, line-by-line reveal)
- Interaction model (scroll-controlled, retract on scroll-up)
- Technical constraints (single HTML file, smooth animation, responsive)
- Future intent (CV / portfolio site)

### Iterative refinements
1. **Initial build** — produced a working top-down printer where the page header appeared first near the printer and the bottom of the CV appeared last at the bottom of the screen.
2. **Physics correction** — prompted to match real printer behavior: the header should exit the slot first and slide *away*, with later sections appearing near the slot. This required reversing the visual section order (`flex-direction: column-reverse`), bottom-anchoring the paper in its clipping container, and inverting the reveal math.

### What worked well
- The initial prompt produced a complete, functional file in one shot (~550 lines)
- Copilot correctly chose `requestAnimationFrame` throttling, passive listeners, and CSS transitions over JS animation
- The reversal was implemented with minimal, targeted changes (3 CSS + 2 JS edits) rather than a full rewrite

### Session log (abridged)
Abridged reasoning and chain of thought: [`chat-log/copilot-session-01-printer-cv.md`](chat-log/copilot-session-01-printer-cv.md)

---

## Project Structure

```
test/
├── printer-cv.html                           # Main animated printer page
├── cv-template.md                            # Markdown CV template for future reference
├── PROMPT.MD                                 # Initial prompt given to Copilot
├── README.md                                 # This file
├── demo/
│   ├── test_v.01.gif                         # Early prototype recording
│   ├── test_v.1.0.gif                        # Revised version recording
│   └── test_v1.mp4                           # Video demo
└── chat-log/
    └── copilot-session-01-printer-cv.md      # Session log with full chain of thought
```

---

## Try It

Open `printer-cv.html` in any modern browser and scroll. No build step, no server, no dependencies.

---

## Future Ideas

- Add print sound effects triggered by scroll
- Implement a "tear off" animation when fully printed
- Support multiple pages / continuous feed

---