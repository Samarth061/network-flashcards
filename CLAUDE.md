# Flashcard Project — CSS & Design Guide

All files are **self-contained single HTML** with inline `<style>` and `<script>`. No external assets.

---

## File Types

| Type | Max width | Font base | Notes |
|------|-----------|-----------|-------|
| Flashcard (`.html` in `topics/`) | 640–760px card area | 15px | Flip card, pill filters, cheat sheet |
| Teaching guide (`*teachingguide.html`) | 860px + 260px sidebar | 16px | Fixed left sidebar, section-based layout |
| Network automation (`networkautomation/`) | varies | 15px | Separate track, different structure |

---

## Flashcard File Design System

### CSS Variables (canonical — use these for new files)
```css
:root {
  --bg:#ffffff;  --bg2:#f5f5f3;  --bg3:#eeede8;  --bgi:#e6f1fb;
  --tx:#1a1a18;  --tx2:#5f5e5a;  --tx3:#888780;  --txi:#185fa5;
  --bd:rgba(0,0,0,0.12);  --bd2:rgba(0,0,0,0.22);  --bdi:#85b7eb;
  --teal:#1D9E75;  --teal-bg:#E1F5EE;  --teal-tx:#0F6E56;  --teal-bd:#5DCAA5;
  --r:8px;  --rl:12px;
}
@media(prefers-color-scheme:dark){
  :root{
    --bg:#1e1e1c;  --bg2:#2a2a28;  --bg3:#333330;  --bgi:#0c2e4a;
    --tx:#f0ede8;  --tx2:#b4b2a9;  --tx3:#888780;  --txi:#85b7eb;
    --bd:rgba(255,255,255,0.1);  --bd2:rgba(255,255,255,0.2);  --bdi:#85b7eb;
    --teal-bg:#073d28;  --teal-tx:#5DCAA5;  --teal-bd:#1D9E75;
  }
}
```
> **Note:** `ospf.html` uses verbose `--color-background-primary` names (legacy). New files should use the short names above.

### Key Components

**3D Flip Card**
```css
.card-container { perspective: 1200px; }
.card { transform-style: preserve-3d; transition: transform 0.45s cubic-bezier(0.4,0,0.2,1); }
.card.flipped { transform: rotateY(180deg); }
.card-face { backface-visibility: hidden; }
.card-back { transform: rotateY(180deg); }
```

**Category Pills** — horizontal row, wrap on overflow
```css
.pills-wrap { display:flex; gap:7px; flex-wrap:wrap; }
.pill { border-radius:20px; font-size:12px; }
.pill.active { background:var(--teal-bg); color:var(--teal-tx); border-color:var(--teal-bd); }
.pill.active.interview { background:#FAEEDA; color:#854F0B; border-color:#e4b87c; }
```

**Badge** — category label on card face
```css
.badge { border-radius:20px; background:var(--teal-bg); color:var(--teal-tx); }
.badge.interview { background:#FAEEDA; color:#854F0B; }
```

**Progress bar** — 3px height, fills with teal
```css
.progress-wrap { height:3px; background:var(--bd); }
.progress-fill { background:var(--teal); transition:width .3s ease; }
```

**Answer content styling**
```css
.card-a { font-size:14px; line-height:1.7; color:var(--tx2); }
.card-a code { background:var(--bg2); border-radius:3px; font-family:'SFMono-Regular',Consolas,monospace; }
.card-a pre { background:var(--bg2); overflow-x:auto; }
.card-a table { width:100%; border-collapse:collapse; font-size:.88em; }
```

**Cheat sheet section**
```css
.cs-h { color:var(--teal-tx); border-bottom:0.5px solid var(--bd); }
.note-grid { display:grid; grid-template-columns:repeat(auto-fill,minmax(170px,1fr)); gap:8px; }
.note-card { background:var(--bg2); border-radius:var(--r); }
table.cheat th { background:var(--bg2); font-size:11px; color:var(--tx3); }
```

**Mobile sidebar** (added to all flashcard files)
```css
/* hamburger hidden on desktop, visible on mobile */
.hamburger { display:none; }
#sidebar { position:fixed; width:260px; transform:translateX(-100%); transition:transform .25s ease; }
#sidebar.open { transform:translateX(0); }
#sidebar-overlay { position:fixed; inset:0; background:rgba(0,0,0,0.35); z-index:200; }
@media(max-width:768px) {
  .hamburger { display:block; }
  .pills-wrap, .cats { display:none !important; }
  .card-area { max-width:96vw !important; }
}
```

### JavaScript Structure
```javascript
const ALL = [ {cat:"Category", q:"Question", a:"<p>HTML answer</p>"}, ... ];
let current = 0, activeCat = "All", filtered = [...ALL];
const cats = ["All", ...new Set(ALL.map(c=>c.cat))];
const LS_KEY = 'fc_<topic>'; // unique per file

// Key functions: buildPills/renderCats(), setCat(c), nav(dir), shuffle(),
//                flipCard(), render(), setMode(m),
//                buildSidebarCats(), toggleSidebar(), goToCard(),
//                saveState(), restoreState()

// Bootstrap:
buildPills();       // or renderCats()
restoreState();     // restore localStorage
buildPills();       // re-run to highlight restored category
render();
```

**localStorage keys:** `fc_transport`, `fc_link`, `fc_network`, `fc_ipv6`, `fc_ospf`

---

## Teaching Guide Design System

### CSS Variables
```css
:root {
  --navy:#0f1929;  --navy2:#1a2840;  --navy3:#243350;
  --teal:#00c9a7;  --teal2:#00a88c;
  --amber:#f5a623;  --coral:#e05c5c;  --blue:#4a9eff;  --purple:#a78bfa;
  --bg:#f8f9fb;  --white:#ffffff;
  --tx:#1e293b;  --tx2:#475569;  --tx3:#94a3b8;
  --border:#e2e8f0;  --border2:#cbd5e1;
  --mono:'SFMono-Regular',Consolas,monospace;
  --sidebar:260px;
}
```
*(No dark mode in teaching guides)*

### Layout
- Fixed left sidebar: `width:260px; position:fixed; background:var(--navy)`
- Main content: `max-width:860px; padding:3rem; flex:1`
- Section spacing: `margin-bottom:5rem`

### Key Components

**Callout boxes**
```css
.callout { border-radius:10px; padding:1.1rem 1.25rem; border-left:4px solid; }
.callout-key  { background:#f0fdf8;  border-color:var(--teal);  }  /* key concept */
.callout-warn { background:#fffbeb;  border-color:var(--amber); }  /* warning */
.callout-note { background:#f0f4ff;  border-color:var(--blue);  }  /* info */
.callout-alert{ background:#fff5f5;  border-color:var(--coral); }  /* danger */
```

**Tables** (`.compare-table`)
```css
thead { background:var(--navy); color:white; font-size:0.82rem; }
tbody tr:nth-child(even) { background:#f8fafc; }
td { padding:0.6rem 0.9rem; border-bottom:1px solid var(--border); }
```

**Packet field rows** (`.pf-row`)
```css
.pf-name { background:var(--navy); color:white; min-width:180px; font-weight:500; }
.pf-bits { background:#f8fafc; font-family:var(--mono); min-width:55px; }
```

**Algorithm box**
```css
background:var(--navy);  border-radius:10px;  font-family:var(--mono);  font-size:0.82rem;
.comment { color:rgba(0,201,167,0.7); }  /* teal */
.kw      { color:var(--blue); }           /* keyword */
.num     { color:var(--amber); }          /* number */
```

**Step boxes**
```css
.step-num { width:28px; height:28px; border-radius:50%; background:var(--navy); color:var(--teal); font-weight:700; }
```

**Tags**
```css
.tag-teal   { background:#d0faf2; color:#065f46; }
.tag-amber  { background:#fef3c7; color:#92400e; }
.tag-blue   { background:#dbeafe; color:#1e40af; }
.tag-red    { background:#fee2e2; color:#991b1b; }
.tag-purple { background:#ede9fe; color:#6d28d9; }
```

**Diagrams**
```css
.diagram { background:white; border:1px solid var(--border); border-radius:12px; overflow-x:auto; }
.diagram-title::before { content:''; width:3px; height:12px; background:var(--teal); }
```

**Sidebar nav links**
```css
.nav-link { font-size:12.5px; border-left:2px solid transparent; padding:6px 12px; }
.nav-link.active { color:var(--teal); border-left-color:var(--teal); background:var(--navy2); }
```

**Two-column boxes** (`.col-box`)
```css
background:white; border:1px solid var(--border); border-radius:10px; padding:1.1rem 1.25rem;
```
Grid collapses at 700px: `@media(max-width:700px){ grid-template-columns:1fr }`

---

## Color Coding Conventions

| Purpose | Color | Where used |
|---------|-------|------------|
| Primary accent / key concepts | teal `#1D9E75` / `#00c9a7` | Active states, important callouts, highlights |
| Structure / authority | navy `#0f1929` | Sidebar, table headers, algorithm backgrounds |
| Warning / interview prep | amber `#f5a623` | Interview-marked cards, warning callouts |
| Alert / error | coral `#e05c5c` | Error conditions, danger callouts |
| Info / secondary | blue `#4a9eff` | Informational callouts, info tags |
| Accent | purple `#a78bfa` | Additional visual hierarchy in teaching guides |

---

## General Rules

- **Never** add external CSS/JS files — keep everything inline in the HTML
- **Borders:** `0.5px solid` for flashcard components; `1px solid` for teaching guide sections
- **Border radius:** `var(--r)` = 8px standard; `var(--rl)` = 12px for cards; 20px for badges/pills
- **Dark mode:** Always include `@media(prefers-color-scheme:dark)` in new flashcard files; teaching guides are light-only
- **Responsive breakpoint:** 700px for two-column grid collapse; 768px for mobile sidebar
- **Interview cards:** Category name `"⭐ Interview"` → amber badge/pill styling
- **Answer HTML:** Answers can contain `<ul>`, `<ol>`, `<strong>`, `<code>`, `<pre>`, `<table>` — all styled via `.card-a` rules

---

## Interview Prep Context

This flashcard project is preparation for an **Amazon Network Development Engineer (NDE) Intern** interview.

### Topic Priority (highest → lowest)
1. **Python automation for networking** — highest priority, biggest differentiator
2. **TCP/IP troubleshooting** — layer isolation, symptoms, commands
3. **BGP** — eBGP/iBGP, FSM, attributes, best-path, route reflectors
4. **OSPF** — areas, adjacency states, DR/BDR, LSAs, cost
5. **Link/Data-link essentials** — ARP, switching/forwarding, STP basics, MTU
6. **Secondary** — VXLAN/multitenancy, tunneling (GRE, IP-in-IP), RIP/IS-IS (recognition only), regex/log parsing, system-design reasoning

> **If behind:** always return to `Python automation > TCP/IP > BGP/OSPF > ARP/switching > secondary topics`

### Depth Guidance
| Keep **deep** | Keep **shallow** |
|---|---|
| Python automation, BGP, OSPF, TCP/IP, ARP, switching/forwarding, log parsing | RIP, IS-IS, deep STP variants, deep VXLAN internals, legacy routing trivia |

### Study Mindset Per Topic
Every topic should be understood through this lens:
1. **Concept** — what it is
2. **Failure mode** — how it breaks
3. **Detection** — what symptom you would see
4. **Command/tool** — what you would check first
5. **Automation angle** — how to validate or monitor it in Python

### Interview Answer Frameworks

**Protocol questions:** what it is → why it exists → how it works → common failure modes → what to check first

**Troubleshooting questions:** clarify symptom → isolate layer → first commands → likely causes → validate the fix

**Automation questions:** inputs → checks to run → Python modules → timeout/error handling → summarize/report

**Design-ish questions:** goal → scale/constraints → safe rollout → observability → rollback/failure isolation

### Python Modules to Know
`subprocess`, `ipaddress`, `argparse`, `logging`, `concurrent.futures`, `re`

### Interview Lens (Amazon NDE)
Every answer should connect to: *automate and deploy networks at scale, Linux/Unix scripting, IP networking fundamentals, BGP/OSPF, packet forwarding architectures, repeatable processes/runbooks, debugging under ambiguity.*
