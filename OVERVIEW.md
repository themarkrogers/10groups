# 10Groups - Project Overview

## What Is This Project?

10Groups is a web-based political compass quiz that evaluates users' political beliefs across **10 different categories** (each with 2 axes, totaling 24 distinct measurement dimensions). Users answer a series of agree/disagree questions, and the system calculates their position on multiple political spectrums, visualizing results as:

1. **Compass Grid** - 10 2D compass plots showing position on paired axes
2. **Axis Bars** - Individual progress bars for each dimension
3. **Heat Map** - Overlay visualization showing combined positions
4. **Ideology Matching** - Closest matching political ideology based on scores

---

## Developer Entry Points

### Primary Files to Modify

| File | Purpose |
|------|---------|
| `questions.js` | Short quiz questions (~83) with axis effects |
| `questionslong.js` | Detailed quiz questions (~155) with axis effects |
| `ideologies.js` | Ideology definitions for result matching |
| `results.html` | Results display, canvas drawing, axis bar labels |
| `compassimage.png` | Background image for the compass grid |

### Quick Start for Development

1. **To add/modify questions**: Edit `questions.js` or `questionslong.js`
2. **To change axis labels**: Edit HTML in `results.html` (progress bar labels)
3. **To add ideologies**: Add entries to the `ideologies` array in `ideologies.js`
4. **To change compass visuals**: Replace `compassimage.png` and update canvas coordinates in `results.html`

---

## How the Quiz Works

### Question → Score Flow

```
questions.js           quiz.html              results.html
┌──────────────┐      ┌──────────────┐       ┌──────────────┐
│ Question     │      │ User clicks  │       │ Calculate    │
│ { id, text,  │─────▶│ Agree/Disagree│──────▶│ percentages  │
│   effects }  │      │ buttons      │       │ per axis     │
└──────────────┘      └──────────────┘       └──────────────┘
                                                    │
                                                    ▼
                                             ┌──────────────┐
                                             │ Draw on      │
                                             │ canvas &     │
                                             │ progress bars│
                                             └──────────────┘
```

### Question Format

Each question has an `effects` object that modifies axis scores when the user agrees:

```javascript
{
    "id": 5,
    "question": "Freedom of business is the best practical way a society can prosper.",
    "effects": { "x": 5, "ax": 5 }  // Agreeing adds +5 to axes "x" and "ax"
}
```

- **Positive effects** (+5): Agreeing increases the axis value
- **Negative effects** (-5): Agreeing decreases the axis value
- User response multipliers: Strongly Agree (1.0) → Strongly Disagree (-1.0)

### Axis Naming Convention

| Prefix | Category | Axes |
|--------|----------|------|
| `x`, `y` | General/Regular | Left-Right, Auth-Lib |
| `ax`, `ay` | Economic | Capitalism-Socialism, Free-Planned |
| `bx`, `by` | Governmental | Autocracy-Democracy, Auth-Lib |
| `cx`, `cy` | Diplomatic | Particular-Universal, Nationalism-Globalism |
| `dx`, `dy` | Societal | Tradition-Progress, Conserve-Reform |
| `ex`, `ey` | Technological | Decelerate-Accelerate, Primitivism-Transhumanism |
| `fx`, `fy` | Law | Civil-Common Law, Punitive-Rehabilitative |
| `gx`, `gy` | Cultural | Hierarchy-Equality, Mono-Multicultural |
| `hx`, `hy` | Procedural | Persist-Compromise, Direct-Transitional |
| `ix`, `iy` | Political | Moderate-Radical, Apolitical-Politicized |
| `ja`-`jd` | Moral | Collectivism, Revolution, Idealism, Consequence |

---

## Key Concepts for Adaptation

### Swapping Axes and Questions

To create a different quiz with your own axes (e.g., personality test, opinion survey):

1. **Define your axes** - Decide on axis codes (e.g., `a1`, `a2`, `b1`, `b2`)
2. **Create questions** - Write questions with `effects` mapped to your axes
3. **Update results display** - Modify `results.html` canvas drawing coordinates and progress bar IDs
4. **Create compass image** - Design a new `compassimage.png` matching your layout
5. **Update ideologies** (optional) - Create matching profiles for result classification

### Critical Coupling Points

The following elements are **tightly coupled** and must stay synchronized:

- Axis codes in `questions.js` effects ↔ URL parameters in `results.html`
- Progress bar IDs in `results.html` ↔ CSS selectors in `axes.css`
- Canvas coordinate positions ↔ Compass image grid layout
- Ideology stats keys ↔ Question effect axis codes

### Score Calculation Algorithm

Located in `quiz.html` → `percentageCalculation()`:

1. Sum all effect values per axis (weighted by user response)
2. Calculate max possible score per axis
3. Compute percentage: `(score / max) * 10` → value from -10 to +10
4. Pass scores via URL query parameters to `results.html`

---

## Important Technical Notes

### No Build System

This is a vanilla HTML/CSS/JavaScript project. No npm, bundling, or compilation required. Simply open `index.html` in a browser or host on a static file server.

### State Management

- **Quiz progress**: Stored in JavaScript variables (`answers` object, `qn` counter)
- **Results transfer**: Passed via URL query parameters between `quiz.html` → `results.html`
- **Session storage**: `sessionStorage.answers` and `sessionStorage.percentages` store backup data

### Canvas Rendering

Results visualization uses HTML5 Canvas API. Key coordinates in `results.html`:

- Compass grid is 2500×1500 pixels (scaled for display)
- Each compass cell is ~400×400 pixels at specific offsets
- Circle positions calculated as: `baseX + (400 * (axis + 10) / 20)`

### Dependencies

- jQuery 3.5.1 (CDN) - Used only in `quiz.html` for DOM manipulation
- Google Fonts (Montserrat, Roboto)
- Hotjar analytics (can be removed)

---

## License

MIT License - derived from [8values](https://github.com/8values/8values.github.io) and [SapplyValues](https://sapplyvalues.github.io/). See `LICENSE.txt` for details.

