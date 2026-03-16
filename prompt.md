# Flashcard App Generation Prompt

> Create a standalone, self-contained HTML flashcard study app for the topic: **[TOPIC NAME]**. Base it entirely on the provided source material (attached file or pasted content).

## Requirements

- Single `.html` file, no external dependencies, works offline in any browser
- Two modes toggled by buttons in the header: **Flashcards** and **Cheat Sheet**

### Flashcard mode
Generate as many cards as needed to cover all meaningful concepts in the source material — let the depth and breadth of the content dictate the count. Organize cards into logical categories shown as filterable pill buttons. Each card flips with a CSS 3D animation (rotateY) on click, showing the question on front and answer on back. Include a progress bar, Prev/Next navigation, and a Shuffle button. Answers can use `<ul>`, `<ol>`, `<code>`, and `<strong>` tags for formatting.

### Cheat Sheet mode
Cover everything in the source material worth referencing at a glance — key facts, timers/values, type/category comparisons, config commands, state sequences, etc. Let the material determine how many tables and grid cards are needed; do not truncate or summarize away detail.

### Styling
Clean flat design using CSS variables for full light/dark mode support (`prefers-color-scheme`). No gradients, no shadows, no external fonts. Neutral background on `<body>`, white card surfaces, teal accent (`#1D9E75`) for active states and progress bar, muted greens for category badges. Rounded corners (`12px` cards, `8px` elements), `0.5px` borders throughout.

### Cards to prioritize
- Definitions
- State/sequence lists (ordered)
- Parameter comparisons
- "What happens when" scenarios
- Configuration commands and their purpose
- Common gotchas and non-obvious behaviors

## Output
Output the complete file — do not truncate. Save it to `/mnt/user-data/outputs/[topic]_flashcards.html` and present it for download.