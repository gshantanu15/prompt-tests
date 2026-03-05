# Copilot Session 01 — Printer CV (Scroll-to-Print Animation)

**Date:** March 5, 2026  
**Files created:** `printer-cv.html`

---

## Task 1: Create Scroll-Controlled Printer Animation

### User Request
Build a single self-contained HTML file simulating a printer printing paper, controlled by scrolling. Paper feeds out of a printer, content appears line-by-line, and scrolling back retracts the paper. Future intent: use as a CV/portfolio website.

### Chain of Thought

1. **Architecture decision:** Fixed-position printer scene + tall scroll-spacer div. Scroll progress (0→1) maps to paper reveal height. No external dependencies — pure HTML/CSS/JS.

2. **Printer design:** Built with layered divs — top cover (input tray), main body (brand label, buttons, LED, stripe), paper slot (dark with roller marks), base with feet. All using CSS gradients and box-shadows for depth.

3. **Paper mechanism:** A `.paper-track` div acts as a clipping container (`overflow: hidden`, `height: 0`). JS dynamically sets its height based on scroll progress, revealing the paper inside.

4. **Content sections:** Each `.cv-section` starts with `opacity: 0; transform: translateY(6px)`. JS adds `.visible` class when a section's top enters the revealed area, triggering a CSS transition for fade-in + slide.

5. **Scroll calibration:** On init, temporarily set track height to `auto` to measure paper's natural `scrollHeight`. Scroll spacer = `viewport + paperHeight × multiplier(3.5)`. This ensures 100% scroll = fully printed.

6. **Details added:**
   - Progress bar (thin gradient at viewport top)
   - Scroll hint with bouncing arrow (fades after scrolling starts)
   - Print head (blue animated sweep line at printing edge, visible only while scrolling)
   - LED status (green idle → amber pulse while printing)
   - Completion glow (green shadow on paper at 98%+ progress)
   - Responsive breakpoints for mobile

7. **Performance:** Scroll handler throttled via `requestAnimationFrame`. Resize recalibrates. All listeners are passive.

### Output
Single `printer-cv.html` file (~550 lines) with embedded CSS and JS. Sample CV content (Jane Doe, full-stack developer) used as placeholder.

---

## Task 2: Reverse Paper Feed (Real Printer Behavior)

### User Request
Make the page behave like a real printer — the header/top of the CV should print first (exit the slot first) and slide downward. The bottom sections print last. Final state after full scroll: interests at top, header at bottom.

### Chain of Thought

1. **Understanding the physics:** In a face-up output printer, the top of the page exits the slot first and slides away. As more paper feeds, the first-printed content moves further from the slot. New content always appears near the slot. So the CV header should be at the paper's bottom (exits first), and interests at the top (exits last).

2. **Approach — three CSS changes + two JS changes:**

   **CSS:**
   - `.paper` gets `display: flex; flex-direction: column-reverse` — this visually reverses the section order so the header (first in DOM) renders at the bottom of the paper.
   - `.paper-track` gets `display: flex; justify-content: flex-end` — anchors the paper to the bottom of the clipping container. As track height grows, the paper's bottom (header) is revealed first.
   - `.paper-track::before` changed from `position: sticky` to `position: absolute` — the sticky approach broke when track became a flex container.
   - Header border flipped from `border-bottom` → `border-top` since it now faces upward.

   **JS:**
   - Section reveal logic inverted: instead of checking `sectionTop < revealed` (top-down), now checks `sectionBottom > (paperHeight - revealed) + buffer` (bottom-up). The clip line starts at the paper's top and moves upward as more is revealed from the bottom.
   - Print head positioned at `paperHeight - revealed` (top of revealed area, near the printer slot) instead of at `revealed` (bottom edge).

3. **Why `column-reverse` over DOM reorder:** Keeps the HTML in logical reading order (header first → interests last), which is better for accessibility, SEO, and future editing. The visual reversal is purely presentational.

4. **Sanity check on reveal math:**
   - At scroll 0%: `revealed = 0`, track height = 0, nothing visible.
   - At scroll 50%: `revealed = paperHeight/2`, bottom half of paper visible. Header and early sections are showing.
   - At scroll 100%: `revealed = paperHeight`, full paper visible. All sections shown.
   - `clipTop = paperHeight - revealed` represents the Y coordinate where the hidden/visible boundary is. Sections with `bottom > clipTop` are in the visible zone.

### Output
Updated `printer-cv.html` with 6 targeted edits (3 CSS, 1 CSS border flip, 2 JS logic changes).

---

## Key Design Decisions Summary

| Decision | Rationale |
|---|---|
| Single scroll-spacer + fixed scene | Cleanest way to map scroll → animation without scroll-hijacking |
| `requestAnimationFrame` throttling | Prevents layout thrashing, smooth 60fps updates |
| CSS transitions on sections (not JS animation) | GPU-composited, less code, easily reversible |
| `flex-direction: column-reverse` | Reverses visual order while keeping DOM in reading order |
| `justify-content: flex-end` on track | Bottom-anchors paper so clipping reveals from bottom up |
| Dynamic spacer calibration | Adapts to any content length — add/remove CV sections freely |
| Passive scroll listeners | Avoids blocking the main thread |

## File Structure

```
test/
├── printer-cv.html                           # Main animated printer page
├── cv-template.md                            # Markdown CV template for future reference
├── README.md                                 # Project overview & prompt test report
├── demo/
│   ├── test_v.01.gif                         # Early prototype recording
│   ├── test_v.1.0.gif                        # Revised version recording
│   └── test_v1.mp4                           # Video demo
└── chat-log/
    └── copilot-session-01-printer-cv.md      # This session log
```
