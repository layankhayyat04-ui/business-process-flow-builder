<div align="center">

<img src="https://capsule-render.vercel.app/api?type=cylinder&color=0:4338CA,50:6366F1,100:818CF8&height=180&section=header&text=Business%20Process%20Flow%20Builder&fontSize=32&fontColor=ffffff&fontAlignY=45&animation=blinking" width="100%"/>

<br/>

<a href="#">
  <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=22&pause=1000&color=6366F1&center=true&vCenter=true&width=700&lines=Drag.+Drop.+Connect.+Export.;Zero+dependencies.+One+HTML+file.;Cubic+B%C3%A9zier+arrows+hand-coded+in+SVG." alt="Typing SVG"/>
</a>

<br/><br/>

<p>
  <img src="https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white"/>
  <img src="https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white"/>
  <img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black"/>
  <img src="https://img.shields.io/badge/SVG-FFB13B?style=for-the-badge&logo=svg&logoColor=black"/>
</p>

<p>
  <img src="https://img.shields.io/badge/Zero_Dependencies-059669?style=flat-square"/>
  &nbsp;
  <img src="https://img.shields.io/badge/Single_File-6366F1?style=flat-square"/>
  &nbsp;
  <img src="https://img.shields.io/badge/No_Build_Step-EC4899?style=flat-square"/>
  &nbsp;
  <img src="https://img.shields.io/badge/Status-Complete-22C55E?style=flat-square"/>
</p>

</div>

---

<div align="center">

## `< The Idea />`

</div>

> Visio costs hundreds per year. Draw.io requires an account. Lucidchart locks features behind a paywall. This replaces all of them — for process diagramming — with a single self-contained HTML file. Open it in a browser. Start diagramming. Close it. Nothing installed, nothing uploaded, nothing tracked.

<br/>

---

## 🎯 Six Node Types

<div align="center">

| Node | Shape | Use Case |
|:---:|:---:|:---|
| **Start** | ● Circle (Green) | Entry point of the process |
| **Process** | ▭ Rectangle (Indigo) | An action, task, or step |
| **Decision** | ◆ Diamond (Amber) | Yes/No or conditional branch |
| **Data** | ▱ Parallelogram (Cyan) | Input, output, or data store |
| **Delay** | ⏱ Rounded rect (Violet) | Wait state or timer |
| **End** | ● Circle (Red) | Process terminator |

</div>

---

## ⚡ Feature Breakdown

<details>
<summary><strong>🔗 Port-Based Connection System</strong></summary>

<br/>

Each node exposes **4 connection ports** — right, left, top, bottom. Two clicks to connect any two nodes:

1. Click the source port — it highlights
2. Click the target port — a smooth cubic Bézier curve draws instantly

No drag. No rubber-banding. No accidental misfires.

</details>

<details>
<summary><strong>✏️ Inline Label Editing</strong></summary>

<br/>

- Double-click any node's title to edit it in place — `contenteditable`, zero boilerplate
- No modal. No form. No save button. Click away to confirm.
- Labels persist across zoom/pan without any re-render

</details>

<details>
<summary><strong>🖱️ Full Canvas Interaction</strong></summary>

<br/>

| Action | How |
|:---|:---|
| **Move a node** | Click and drag |
| **Pan the canvas** | Shift + drag |
| **Zoom** | Scroll wheel |
| **Delete a node** | Right-click → Delete, or `Delete` key |
| **Delete a connection** | Double-click the arrow |
| **Duplicate a node** | Right-click → Duplicate |
| **Context menu** | Right-click anywhere on canvas |

</details>

<details>
<summary><strong>💾 SVG Export</strong></summary>

<br/>

Exports the entire diagram as a clean, self-contained `.svg` file — all nodes, arrows, and labels. Open in Figma, Illustrator, or drop straight into a slide deck.

</details>

<details>
<summary><strong>⚡ Auto-Layout</strong></summary>

<br/>

One-click grid layout that arranges all nodes into a clean, readable left-to-right flow. Resets positions without touching connections.

</details>

---

## 🏗️ Architecture

```
business-process-flow-builder/
└── index.html     # Entire app — HTML + CSS + JS, self-contained
                   # ~500 lines CSS  ·  ~400 lines JS  ·  0 dependencies
```

One file. No `node_modules`. No build step. No bundler. The whole app ships as a single `index.html`.

---

## 🧩 Key Code

### Cubic Bézier Arrow Routing

Every arrow between two ports is a smooth curve. The control-point offset of `80px` per side direction gives it the characteristic S-curve or elbow shape depending on port orientation:

```javascript
function cubicPath(a, b, aS, bS) {
  const d = 80; // control-point offset in pixels
  let c1x = a.x, c1y = a.y;
  let c2x = b.x, c2y = b.y;

  // Source port pushes control point outward
  if (aS === 'right')  c1x += d;
  if (aS === 'left')   c1x -= d;
  if (aS === 'bottom') c1y += d;
  if (aS === 'top')    c1y -= d;

  // Target port pulls control point outward
  if (bS === 'right')  c2x += d;
  if (bS === 'left')   c2x -= d;
  if (bS === 'bottom') c2y += d;
  if (bS === 'top')    c2y -= d;

  return `M${a.x},${a.y} C${c1x},${c1y} ${c2x},${c2y} ${b.x},${b.y}`;
}
```

### Port-Click State Machine

A two-state machine: waiting for first click, then waiting for second. Prevents self-loops automatically:

```javascript
function portMouseDown(e) {
  e.stopPropagation();
  const nid  = parseInt(e.currentTarget.dataset.nid);
  const side = e.currentTarget.dataset.side;

  if (!connectFrom) {
    // State 1 — record source, highlight port
    connectFrom = { nodeId: nid, side };
    e.currentTarget.classList.add('active');
  } else {
    // State 2 — complete connection (guard: no self-loop)
    if (connectFrom.nodeId !== nid) {
      addEdge(connectFrom.nodeId, connectFrom.side, nid, side);
    }
    document.querySelectorAll('.port.active')
      .forEach(p => p.classList.remove('active'));
    connectFrom = null;
  }
}
```

### Canvas Coordinate Transform — Zoom + Pan

Dragging nodes uses `screenToCanvas()` to convert mouse coordinates through the current zoom/pan transform, so nodes snap to the correct canvas position at any zoom level:

```javascript
function screenToCanvas(sx, sy) {
  const r = wrap.getBoundingClientRect();
  return {
    x: (sx - r.left - pan.x) / zoom,
    y: (sy - r.top  - pan.y) / zoom,
  };
}

function canvasMouseMove(e) {
  if (draggingNode) {
    const pos = screenToCanvas(e.clientX, e.clientY);
    const n   = nodes.find(n => n.id === draggingNode);
    n.x = pos.x - dragOffset.x;
    n.y = pos.y - dragOffset.y;
    const el = document.getElementById('node-' + draggingNode);
    el.style.left = n.x + 'px';
    el.style.top  = n.y + 'px';
    renderEdges(); // re-draw all arrows on every move
  }
}
```

### Demo — Order Fulfillment Flow (Seeds on Load)

```javascript
// Pre-seeded on first render — shows the tool in action immediately
const demoFlow = [
  { type: 'start',    label: 'Start',         x: 80,  y: 200 },
  { type: 'process',  label: 'Receive Order', x: 250, y: 200 },
  { type: 'decision', label: 'In Stock?',     x: 450, y: 200 },
  { type: 'process',  label: 'Ship Order',    x: 650, y: 120 },
  { type: 'process',  label: 'Back-order',    x: 650, y: 300 },
  { type: 'end',      label: 'End',           x: 870, y: 200 },
];
```

---

## 🎨 Design Tokens

<div align="center">

| Token | Value | Role |
|:---|:---|:---|
| `--bg` | `#0F172A` | Canvas dark background |
| `--surface` | `#1E293B` | Node card background |
| `--accent` | `#6366F1` | Indigo — process nodes, arrows, UI chrome |
| `--start` | `#22C55E` | Start node border |
| `--end` | `#EF4444` | End node border |
| `--decision` | `#F59E0B` | Decision node border |

</div>

---

## 🚀 Run It

```bash
git clone https://github.com/layankhayyat04-ui/business-process-flow-builder.git
cd business-process-flow-builder

open index.html          # macOS
start index.html         # Windows
xdg-open index.html      # Linux
```

Or just download `index.html` and open it in any browser. No internet required after download.

---

## 🗺️ What's Next

| Feature | Description |
|:---|:---|
| LocalStorage Persistence | Auto-save diagram state on every change |
| JSON Import/Export | Save and reload diagrams as structured data |
| Connection Labels | Add text labels to arrows |
| Undo / Redo Stack | Full history with Ctrl+Z / Ctrl+Y |
| Swimlanes | Organize nodes into labeled horizontal lanes |
| Minimap | Overview panel for large, complex diagrams |

---

<div align="center">

<br/>

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:818CF8,100:4338CA&height=120&section=footer&text=Built%20by%20Layan%20Khayyat&fontSize=20&fontColor=ffffff&fontAlignY=65&animation=twinkling" width="100%"/>

<br/>

[![Portfolio](https://img.shields.io/badge/Portfolio-layankhayyat.com-6366F1?style=for-the-badge&logo=vercel&logoColor=white)](https://layankhayyat.com)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/layankhayyat)

</div>
