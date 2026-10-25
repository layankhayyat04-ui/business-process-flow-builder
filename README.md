<div align="center">

<br/>

<img src="https://capsule-render.vercel.app/api?type=waving&color=6366F1&height=200&section=header&text=Business%20Process%20Flow%20Builder&fontSize=30&fontColor=ffffff&fontAlignY=38&desc=Drag%20%C2%B7%20Drop%20%C2%B7%20Connect%20%C2%B7%20Export&descAlignY=58&descSize=18&animation=fadeIn" width="100%"/>

<br/>

<p align="center">
  <img src="https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white"/>
  <img src="https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white"/>
  <img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black"/>
  <img src="https://img.shields.io/badge/SVG-FFB13B?style=for-the-badge&logo=svg&logoColor=black"/>
  <img src="https://img.shields.io/badge/Zero_Dependencies-059669?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Status-Complete-6366F1?style=for-the-badge"/>
</p>

<p align="center">
  <strong>A fully interactive, browser-based flow diagram builder — no frameworks, no libraries, no server.</strong><br/>
  Drag nodes from the sidebar, connect them with curved SVG arrows, rename inline, and export as SVG.
</p>

<br/>

</div>

---

## ✨ Overview

A **single self-contained HTML file** that turns any browser into a professional flow-diagramming tool. Built from scratch in vanilla JavaScript — every interaction, every SVG arrow, every port connection is written by hand with zero runtime dependencies.

Open `index.html` → start diagramming. No install, no build step, no server.

---

## 🖥️ Features

### 🎯 Six Node Types — Drag Any One onto the Canvas

| Node | Icon | Use Case |
|---|---|---|
| **Start** | 🟢 | Entry point of the process |
| **Process** | ⬜ | An action, task, or step |
| **Decision** | 🔷 | Yes/No or conditional branch |
| **Data** | 🗄 | Input, output, or data store |
| **Delay** | ⏳ | Wait state or timer |
| **End** | 🔴 | Process terminator |

### 🔗 Port-Based Connection System

Each node exposes **4 connection ports** (right, left, top, bottom). Click one port, then click a target port — a smooth **cubic Bézier curve** draws automatically between them.

### ✏️ Inline Label Editing

Double-click any node's title or body text to edit in place — `contenteditable` with zero boilerplate. No modal, no form.

### 🖱️ Full Canvas Interaction

| Action | How |
|---|---|
| **Move a node** | Click and drag |
| **Pan the canvas** | Shift + drag, or middle-click |
| **Zoom in/out** | Scroll wheel |
| **Delete a node** | Right-click → Delete, or `Delete` key |
| **Delete a connection** | Double-click the arrow |
| **Duplicate a node** | Right-click → Duplicate |

### 💾 SVG Export

Exports the entire diagram as a clean, self-contained `.svg` file — ready for Figma, Illustrator, or a slide deck.

### ⚡ Auto-Layout

One-click grid layout that arranges all nodes into a clean, readable grid.

### 🌑 Dark-First Design

Full dark UI with an indigo accent (`#6366F1`), dot-grid canvas background, and color-coded node borders — Start (green), End (red), Decision (amber), Process (indigo), Data (cyan), Delay (violet).

---

## 🚀 Getting Started

```bash
# Clone
git clone https://github.com/layankhayyat04-ui/business-process-flow-builder.git
cd business-process-flow-builder

# Open — that's it
open index.html
```

Or just download `index.html` and open it in any browser.

---

## 🧩 Key Code

### Cubic Bézier Arrow Routing

```javascript
function cubicPath(a, b, aS, bS) {
  const d = 80; // control-point offset
  let c1x = a.x, c1y = a.y;
  let c2x = b.x, c2y = b.y;

  if (aS === 'right')  c1x += d;
  if (aS === 'left')   c1x -= d;
  if (aS === 'bottom') c1y += d;
  if (aS === 'top')    c1y -= d;

  if (bS === 'right')  c2x += d;
  if (bS === 'left')   c2x -= d;
  if (bS === 'bottom') c2y += d;
  if (bS === 'top')    c2y -= d;

  return `M${a.x},${a.y} C${c1x},${c1y} ${c2x},${c2y} ${b.x},${b.y}`;
}
```

### Port-Click Connection State Machine

```javascript
function portMouseDown(e) {
  e.stopPropagation();
  const nid  = parseInt(e.currentTarget.dataset.nid);
  const side = e.currentTarget.dataset.side;

  if (!connectFrom) {
    // First click — start a connection
    connectFrom = { nodeId: nid, side };
    e.currentTarget.classList.add('active');
  } else {
    // Second click — complete the connection
    if (connectFrom.nodeId !== nid) {
      addEdge(connectFrom.nodeId, connectFrom.side, nid, side);
    }
    document.querySelectorAll('.port.active')
      .forEach(p => p.classList.remove('active'));
    connectFrom = null;
  }
}
```

### Drag Node with Canvas-Space Coordinate Transform

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
    // Update DOM + re-render all edges
    const el = document.getElementById('node-' + draggingNode);
    el.style.left = n.x + 'px';
    el.style.top  = n.y + 'px';
    renderEdges();
  }
}
```

---

## 🗂️ Project Structure

```
business-process-flow-builder/
└── index.html     # Entire app — HTML + CSS + JS in one self-contained file
                   # ~500 lines CSS  ·  ~400 lines JS  ·  zero dependencies
```

---

## 🗺️ What's Next

| Feature | Description |
|---|---|
| LocalStorage Persistence | Auto-save diagram state across sessions |
| JSON Import/Export | Save and reload diagrams as structured JSON |
| Swimlanes | Organize nodes into labeled lanes |
| Connection Labels | Add text labels to arrows |
| Undo / Redo Stack | Full history with Ctrl+Z / Ctrl+Y |
| Minimap | Overview panel for large diagrams |

---

<div align="center">

<br/>

**Built by [Layan Khayyat](https://layankhayyat.com)**<br/>
B.Sc. Business Information Technology · Princess Sumaya University for Technology (PSUT)

<br/>

[![Portfolio](https://img.shields.io/badge/Portfolio-layankhayyat.com-6366F1?style=for-the-badge&logo=vercel&logoColor=white)](https://layankhayyat.com)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/layankhayyat)

<br/>

<img src="https://capsule-render.vercel.app/api?type=waving&color=0891B2&height=100&section=footer" width="100%"/>

</div>
