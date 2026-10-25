<div align="center">

<a href="./showcase.html">
<img src="https://img.shields.io/badge/%E2%9C%A8%20Live%20Showcase-6366F1?style=for-the-badge&logoColor=white" height="36"/>
</a>

</div>

<br/>

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=shark&color=0:0F0C29,50:302B63,100:24243E&height=220&section=header&text=Flow%20Builder&fontSize=80&fontColor=818CF8&fontAlignY=55&desc=Drag.%20Drop.%20Connect.%20Export.%20%E2%80%94%20Zero%20dependencies.&descSize=17&descAlignY=75&descFontColor=C7D2FE&animation=twinkling&reversal=false" width="100%"/>

</div>

<br/>

<div align="center">
  <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=20&pause=1000&color=818CF8&center=true&vCenter=true&width=680&lines=One+%3Cindex.html%3E.+Zero+npm+install.+Open+in+browser.;Cubic+B%C3%A9zier+arrows+hand-coded+in+SVG.;Port-based+state+machine+%E2%80%94+two+clicks+to+connect." alt="typing"/>
</div>

<br/>

<div align="center">

<img src="https://skillicons.dev/icons?i=html,css,js&theme=dark" height="50"/>

<br/><br/>

<img src="https://img.shields.io/badge/Zero_Dependencies-059669?style=for-the-badge&logo=checkmarx&logoColor=white"/>
&nbsp;
<img src="https://img.shields.io/badge/Single_HTML_File-6366F1?style=for-the-badge"/>
&nbsp;
<img src="https://img.shields.io/badge/No_Build_Step-EC4899?style=for-the-badge"/>
&nbsp;
<img src="https://img.shields.io/badge/SVG_Export-F59E0B?style=for-the-badge"/>

</div>

---

<br/>

<table>
<tr>
<td width="55%">

### 💡 What & Why

Visio costs $500/yr. Draw.io requires an account. Lucidchart locks features behind a paywall.

**This replaces all of them** — for business process diagramming — with a single self-contained `index.html`. Download it. Open it. Start diagramming.

Nothing installed. Nothing uploaded. Nothing tracked.

</td>
<td width="45%">

### 🎯 Six Node Types

| Node | Shape | Meaning |
|:-----|:------|:--------|
| **Start** | ● Green | Entry point |
| **Process** | ▭ Indigo | Action / task |
| **Decision** | ◆ Amber | Yes / No branch |
| **Data** | ▱ Cyan | Input / output |
| **Delay** | ⏱ Violet | Wait state |
| **End** | ● Red | Terminator |

</td>
</tr>
</table>

<br/>

---

## 🏗️ The Entire App

```
business-process-flow-builder/
└── index.html     ← ~500 lines CSS  ·  ~400 lines JS  ·  0 dependencies
```

One file. No `node_modules`. No build step. No bundler. Ships as a single self-contained `index.html`.

<br/>

---

## 🧩 Technical Deep-Dive

<details>
<summary><b>Cubic Bézier arrow routing — smooth curves from any port to any port</b></summary>

<br/>

Each arrow is a smooth SVG cubic Bézier curve. The 80px control-point offset gives it the characteristic S-curve or elbow shape depending on the two port orientations:

```javascript
function cubicPath(a, b, aS, bS) {
  const d = 80; // control-point offset in pixels
  let c1x = a.x, c1y = a.y;
  let c2x = b.x, c2y = b.y;

  // Source port pushes its control point outward
  if (aS === 'right')  c1x += d;
  if (aS === 'left')   c1x -= d;
  if (aS === 'bottom') c1y += d;
  if (aS === 'top')    c1y -= d;

  // Target port pulls its control point outward
  if (bS === 'right')  c2x += d;
  if (bS === 'left')   c2x -= d;
  if (bS === 'bottom') c2y += d;
  if (bS === 'top')    c2y -= d;

  return `M${a.x},${a.y} C${c1x},${c1y} ${c2x},${c2y} ${b.x},${b.y}`;
}
```

</details>

<details>
<summary><b>Port-click state machine — two clicks, no drag, no misfires</b></summary>

<br/>

A minimal two-state machine: waiting for source click, then waiting for target. Self-loops are prevented automatically. No rubber-banding. No accidental connections.

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
    document.querySelectorAll('.port.active').forEach(p => p.classList.remove('active'));
    connectFrom = null;
  }
}
```

</details>

<details>
<summary><b>Canvas coordinate transform — zoom + pan at any level</b></summary>

<br/>

All mouse coordinates pass through `screenToCanvas()` before being applied to node positions, so dragging snaps correctly at any zoom level.

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
    document.getElementById('node-' + draggingNode).style.left = n.x + 'px';
    document.getElementById('node-' + draggingNode).style.top  = n.y + 'px';
    renderEdges();
  }
}
```

</details>

<details>
<summary><b>Interaction reference — full controls</b></summary>

<br/>

| Action | How |
|:-------|:----|
| **Add a node** | Drag from left panel |
| **Move a node** | Click and drag |
| **Connect nodes** | Click source port → click target port |
| **Edit label** | Double-click node title (contenteditable) |
| **Pan canvas** | Shift + drag background |
| **Zoom** | Scroll wheel |
| **Delete node** | Right-click → Delete, or `Delete` key |
| **Delete connection** | Double-click the arrow |
| **Duplicate node** | Right-click → Duplicate |
| **Auto-layout** | Toolbar button — clean grid arrangement |
| **Export SVG** | Toolbar button — self-contained `.svg` file |

</details>

<br/>

---

## 🎨 Design Tokens

<div align="center">

| Token | Hex | Role |
|:------|:----|:-----|
| `--bg` | ![#0F172A](https://placehold.co/14x14/0F172A/0F172A.png) `#0F172A` | Canvas dark background |
| `--surface` | ![#1E293B](https://placehold.co/14x14/1E293B/1E293B.png) `#1E293B` | Node card surface |
| `--accent` | ![#6366F1](https://placehold.co/14x14/6366F1/6366F1.png) `#6366F1` | Indigo — process nodes · arrows · UI chrome |
| `--start` | ![#22C55E](https://placehold.co/14x14/22C55E/22C55E.png) `#22C55E` | Start node border |
| `--decision` | ![#F59E0B](https://placehold.co/14x14/F59E0B/F59E0B.png) `#F59E0B` | Decision node border |
| `--end` | ![#EF4444](https://placehold.co/14x14/EF4444/EF4444.png) `#EF4444` | End node border |

</div>

<br/>

---

## 🚀 Run It

```bash
# Clone
git clone https://github.com/layankhayyat04-ui/business-process-flow-builder.git
cd business-process-flow-builder

# Open (no server, no install)
open index.html          # macOS
start index.html         # Windows
xdg-open index.html      # Linux
```

Or just download `index.html` and open it in any browser. No internet required.

<br/>

---

## 🗺️ Roadmap

| Feature | Status |
|:--------|:-------|
| LocalStorage persistence | 📋 Planned |
| JSON import / export | 📋 Planned |
| Arrow label editing | 📋 Planned |
| Undo / Redo stack | 📋 Planned |
| Swimlanes | 📋 Planned |
| Minimap overview | 📋 Planned |

<br/>

---

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:24243E,50:302B63,100:0F0C29&height=120&section=footer&text=Built%20by%20Layan%20Khayyat&fontSize=22&fontColor=818CF8&fontAlignY=70&animation=twinkling" width="100%"/>

<br/>

[![Portfolio](https://img.shields.io/badge/%F0%9F%8C%90%20layankhayyat.com-6366F1?style=for-the-badge)](https://layankhayyat.com)
&nbsp;
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/layankhayyat)

</div>
