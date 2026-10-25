<div align="center">

<br/>

# Business Process Flow Builder

**A drag-and-drop diagramming tool for mapping business workflows**

Add shapes, connect them with arrows, drag to rearrange — arrows follow automatically. Built entirely in the browser with plain JavaScript and native SVG. No frameworks, no dependencies.

<br/>

![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)
![SVG](https://img.shields.io/badge/SVG-FFB13B?style=for-the-badge&logo=svg&logoColor=black)

<br/>

</div>

---

## Overview

The Business Process Flow Builder is a visual diagramming tool for mapping out how a business process actually works — step by step, including the decision points where it branches.

Instead of describing a process in a paragraph of text, you drag shapes onto a canvas, connect them with arrows, and end up with a clear, shareable diagram of the whole workflow.

It runs **entirely in the browser** — no backend, no database, no installation. Every shape, drag, and connection is handled with plain JavaScript. Arrows are drawn live with SVG so they always point cleanly between shapes, even after you move them.

---

## Demo

A complete order-fulfillment process, built with the tool:

```
Order Received → Check Inventory → In Stock?
                                        ↓ Yes → Ship Order
                                        ↓ No  → Notify Backorder
                                                      → Order Complete
```

---

## Features

### 🛠️ The Toolbar

Every action lives in one row:

| Button | Action |
|---|---|
| `+ Start / End` | Add a rounded start or end node |
| `+ Process Step` | Add a rectangular process step |
| `+ Decision` | Add a diamond decision point |
| `Connect Steps` | Toggle connect mode — click two shapes to draw an arrow |
| `Delete Selected` | Remove selected shape and all its connected arrows |
| `Clear Canvas` | Reset everything to start fresh |

### 🔷 Three Shape Types

- **Start / End** — rounded rectangle, marks where a process begins or finishes
- **Process Step** — plain rectangle, represents a task or action
- **Decision** — diamond, represents a yes/no branch point

### ✏️ Inline Label Editing

Double-click any shape's label to edit it directly. Type a new name and press Enter. No modal dialogs, no separate edit panel.

### 🔗 One-Click Connections

Toggle Connect mode, click a starting shape, then click the destination — an arrow is drawn automatically. The toolbar shows a hint that guides each step.

### 🎯 Live Arrow Routing

Arrows connect edge-to-edge, not center-to-center. When you drag a shape to a new position, every connected arrow updates instantly — the diagram never looks broken mid-edit.

### 🗑️ Clean Editing

Click any shape to select it (highlighted with a pink outline). Delete Selected removes it along with all connected arrows, leaving the rest of the diagram intact.

---

## Technical Highlights

This project deliberately avoids any library or framework to demonstrate the raw mechanics:

- **Drag-and-drop** — implemented from scratch with `mousedown`, `mousemove`, `mouseup` event listeners and coordinate tracking
- **Live SVG arrows** — recalculated on every shape move; each arrow connects the closest edges of its two shapes
- **Inline editing** — a hidden `<input>` positioned over the shape's label, shown on double-click
- **Connection state machine** — Connect mode tracks the "first click" and "second click" in JavaScript state before drawing the arrow

---

## Why No Framework

Built with plain JavaScript and native SVG to demonstrate the underlying mechanics that a library would otherwise hide: drag tracking, coordinate math for arrows, DOM event handling. Keeps it lightweight, dependency-free, and easy to open and run anywhere.

---

## What's Next

| Feature | Description |
|---|---|
| Save & Reload | Store a diagram in the browser or export/import as a file |
| Export as Image or PDF | One-click download of the finished diagram |
| More Shape Types | Swimlanes, sub-processes, annotations |
| Undo / Redo | Step backward and forward through changes |

---

## Project Status

This is a **real, working tool** — every screenshot in the walkthrough shows the live running application, not a mockup. The core diagramming loop (add, connect, label, move, delete) is fully functional.

---

## About

Built by **Layan Khayyat**
B.Sc. Business Information Technology · Princess Sumaya University for Technology (PSUT)

[Portfolio](https://layankhayyat.com) · [LinkedIn](https://linkedin.com/in/layankhayyat)
