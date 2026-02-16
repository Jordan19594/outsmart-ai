---
name: typography
description: Use when building, reviewing, or improving typography on any web page, landing page, or HTML document. Also use when the user mentions 'typography,' 'fonts,' 'line spacing,' 'line height,' 'readability,' 'type scale,' or 'font pairing.' For general page conversion optimization, see page-cro.
---

# Typography

You are an expert typographer applying the principles of Butterick's Practical Typography to web design. Typography is utilitarian — good typography reinforces the meaning of the text. If it doesn't serve the reader's comprehension, it fails, regardless of how it looks.

## The Core Principle

Good typography is measured by how well it reinforces the meaning of the text, not by an abstract scale of merit. Typography that is aesthetically pleasant but doesn't reinforce meaning is a failure. Typography that reinforces meaning, even if aesthetically unpleasant, is a success.

## The Five Rules (Butterick's Foundation)

Every typographic decision flows from these five rules. Apply them in order.

### 1. Start With Body Text
Body text is the most abundant element — it determines overall typographic quality. Make it look good first, then handle everything else.

### 2. Point Size
- **Web body text: 15–25 pixels**
- **Print body text: 10–12 points**
- Not every font appears equally large at a given size — adjust as needed

### 3. Line Spacing (line-height)
- **120–145% of the point size** is the classic rule
- In CSS, use unitless `line-height` values (e.g., `1.3` for 130%)
- Single-spacing (1.0) is too tight; double (2.0) is too loose
- For web body text at 17px+, 1.4–1.6 is acceptable — larger type tolerates slightly more air
- For headings: 1.1–1.3 (tighter than body, but not so tight multi-line headings collide)
- Fonts that run small need less line spacing, and vice versa

### 4. Line Length (measure)
- **45–90 characters per line**, including spaces
- Quick test: 2–3 lowercase alphabets should fit on one line
- On the web, this means preventing text from flowing to the edges of the browser window
- The major flaw in most responsive web layouts: insufficient attention to line length

### 5. Font Choice
- **Best option:** Buy a professional font
- **Good free fonts:** Charter, Source Serif, Source Code, Cooper Hewitt, IBM Plex
- **Tolerable system fonts (A-list):** Athelas, Avenir, Garamond, Gill Sans, Helvetica Neue, Palatino, Hoefler Text, Iowan Old Style, Optima, Seravek
- **Never use:** Times New Roman, Arial — permanently associated with apathy
- **F-list (fatal to credibility):** Comic Sans, Papyrus, Copperplate, Curlz, Jokerman, Brush Script, and dozens of others

## Font Mixing

Less is more.

1. Mixing fonts is an option, never a requirement — one font with size/weight/style variations works fine
2. Most documents tolerate a second font. Few tolerate a third. Almost none tolerate four+
3. You CAN mix two serifs or two sans-serifs — the "must pair serif with sans" rule is false
4. Each font should have a consistent role (e.g., one for body, one for headings)
5. It rarely works to have multiple fonts in a single paragraph
6. Reliable method: combine fonts by the same designer

## Bold and Italic

- Bold **OR** italic — think of them as mutually exclusive (Rule #1)
- Use as little as possible (Rule #2) — if everything is emphasized, nothing is
- With serif fonts: italic for gentle emphasis, bold for heavier
- With sans-serif fonts: skip italic, use bold — sans italic is just a gentle slant that doesn't stand out
- Never bold italic for emphasis
- Never run entire paragraphs in bold
- Overemphasis leaves you nowhere to go when you need real emphasis

## Type Scale

Limit to 8–10 distinct sizes. Use a consistent ratio (1.2–1.25 works well for web).

| Token | Purpose | Suggested Size |
|-------|---------|---------------|
| `--text-xs` | Fine print, labels | 12px (never below 12px) |
| `--text-sm` | Captions, meta | 14px |
| `--text-base` | Body text | 17–19px |
| `--text-lg` | Large body, subheads | 20–22px |
| `--text-xl` | H4, section labels | 24–26px |
| `--text-2xl` | H3 | 30–34px |
| `--text-3xl` | H2 | 38–44px |
| `--text-4xl` | H1 / hero | clamp(40px, 5vw, 60px) |

**Anti-pattern:** 20+ unique font sizes across a page. If you're using more than 10, consolidate.

## Vertical Rhythm and Whitespace

- Build spacing from an 8px base grid (8, 16, 24, 32, 48, 64, 80)
- Headings need more space above than below — they introduce what follows, not what precedes
- Space between paragraphs should be roughly the same as line-height
- Consistent spacing creates visual order; inconsistent spacing creates noise
- When in doubt, add more whitespace — cramped layouts are the most common amateur error

## Rules and Borders

**Use sparingly.** Before adding a rule or border, ask: can whitespace do the same job? Try spacing first.

- Border thickness: 0.5–1pt (thin enough to not upstage content)
- Thicker borders create noise that upstages the information inside
- Never use patterned borders (dots, dashes, double lines) — unnecessarily complicated
- Place rules above headings (not below) to separate the end of the previous section from the current heading
- You want to see the data, not the lines around the data

## Grids

A guide, not a panacea.

- Simpler/coarser grids encourage more consistency (fewer ways to align = more discipline)
- Grids don't have to be rigid or symmetric
- Not everything needs to go on the grid — the eye is the final judge
- Mathematical ratios don't guarantee good layout on their own
- A grid can emerge from experimentation rather than being defined in advance

## Color and Contrast

- WCAG 2.1 AA minimum: 4.5:1 for body text, 3:1 for large text (18px+ or 14px bold)
- Body text on light backgrounds: aim for 7:1+ ratio
- Never use colored text for body copy — stick to near-black on light, near-white on dark
- Low-opacity text (rgba with 0.6 or below) often fails contrast requirements
- Test every text/background combination, not just the primary ones

## ALL CAPS

- Fine for short bits: labels, navigation, eyebrows, buttons
- Never for body text or long passages — harder to read (we recognize words by shape)
- Always add letter-spacing (0.04–0.08em) when using uppercase — without it, letters crowd together

## Web-Specific Rules

- Use `rem` or `px` consistently — don't mix units between CSS and inline styles
- Set `max-width` on text containers, not just the page wrapper
- Use `clamp()` for responsive type that respects both minimum readability and maximum comfort
- Avoid `font-weight` values that aren't multiples of 100 (e.g., 450 is non-standard)
- Load web fonts with `font-display: swap` to prevent invisible text during load
- System font stacks need proper fallback chains

## Typography Audit Checklist

When reviewing a page's typography, check every item:

- [ ] Body text is 15–25px
- [ ] Line-height is 120–145% of font size (1.2–1.45 classic; 1.4–1.6 acceptable for web body at 17px+)
- [ ] Line length is 45–90 characters (check with `ch` unit or character count)
- [ ] No text smaller than 12px anywhere
- [ ] Heading line-height is 1.1–1.3 (tighter than body, but not cramped for multi-line)
- [ ] Type scale has 10 or fewer distinct sizes
- [ ] Maximum 2–3 font families loaded
- [ ] Each font has a consistent, defined role
- [ ] Bold and italic are never combined for emphasis
- [ ] Emphasis is used sparingly, not entire paragraphs
- [ ] ALL CAPS text has letter-spacing added
- [ ] Whitespace is used before borders/rules for separation
- [ ] Borders are 0.5–1pt when present
- [ ] All text/background combinations pass WCAG AA contrast
- [ ] No font-weight values that aren't multiples of 100
- [ ] Spacing follows a consistent scale (e.g., 8px base)
- [ ] Headings have more space above than below
- [ ] No Times New Roman, Arial, or F-list fonts
- [ ] Font fallback stacks are complete and appropriate

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| 20+ unique font sizes | Consolidate to 8–10 using a type scale |
| Line length over 90 characters | Add `max-width` to text containers |
| Line-height at default 1.0 or double 2.0 | Set to 1.2–1.45 range |
| Borders everywhere for visual separation | Replace with whitespace |
| Bold italic for emphasis | Choose one — bold OR italic |
| Text below 12px | Increase to 12px minimum |
| Using Arial or Times New Roman | Switch to any A-list alternative |
| Fixed font sizes at all breakpoints | Use `clamp()` for responsive type |
| No letter-spacing on uppercase text | Add 0.04–0.08em |
| Inconsistent spacing (random px values) | Adopt an 8px base grid system |
