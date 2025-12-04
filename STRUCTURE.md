# 10Groups - File Structure

This document describes every file in the project, their relationships, and what needs to change when adapting the quiz for different axes/questions.

---

## Directory Layout

```
10groups/
├── index.html          # Landing page with quiz description
├── instructions.html   # Pre-quiz page with version selection
├── quiz.html           # Main quiz engine (short version)
├── quizlong.html       # Quiz engine (detailed version)
├── results.html        # Results display with canvas/charts
├── none.html           # Placeholder for unknown ideologies
│
├── questions.js        # Short quiz question definitions
├── questionslong.js    # Detailed quiz question definitions
├── ideologies.js       # Political ideology profiles for matching
│
├── style.css           # Main stylesheet
├── axes.css            # Progress bar styles for results axes
│
├── compassimage.png    # Background for compass grid visualization
├── heatmap_img.png     # Background for heat map visualization
├── icon.png            # Favicon
│
├── heatmap/            # Alternative heatmap images (unused)
│   ├── heatmap1.png
│   ├── heatmap2.png
│   └── heatmap3.png
│
├── LICENSE.txt         # MIT License
├── README.md           # Brief project description
├── ToDo.md             # Development notes
├── OVERVIEW.md         # Project overview for developers
└── STRUCTURE.md        # This file
```

---

## File Relationships

### User Flow

```
index.html → instructions.html → quiz.html ─────────→ results.html
                                     │                     │
                                quizlong.html ─────────────┘
```

### Data Flow

```
questions.js ──────┐
                   ├──→ quiz.html ──→ URL params ──→ results.html
questionslong.js ──┘                                      │
                                                          │
ideologies.js ────────────────────────────────────────────┘
```

### Style Dependencies

```
style.css ──→ All HTML files
axes.css  ──→ results.html only
```

---

## Core Files Detail

### `questions.js` / `questionslong.js`

**Purpose**: Define quiz questions and their effects on axes.

**Format**:
```javascript
questions = [
    {
        "id": 0,                    // Unique identifier
        "question": "Text here",    // Question text shown to user
        "effects": {                // Axes affected when user agrees
            "x": 5,                 // General left-right
            "ax": 5                 // Economic capitalism-socialism
        }
    },
    // ... more questions
];
```

**Adaptation Notes**:
- Create new axis codes (keep them short: 2-3 characters)
- Effects can be any positive or negative integer (typically ±5)
- Questions can affect multiple axes simultaneously
- The ID must be unique within the file (used for answer tracking)

---

### `results.html`

**Purpose**: Display calculated results via canvas drawings and progress bars.

**Key Sections**:

#### 1. URL Parameter Parsing (lines ~219-244)
```javascript
const urlParams = new URLSearchParams(window.location.search);
x_axis = parseFloat(urlParams.get("x"))
ax_axis = parseFloat(urlParams.get("ax"))
// ... all axis codes
```
*Adaptation*: Add/remove parameters matching your axis codes.

#### 2. Canvas Drawing (lines ~245-430)
```javascript
ctx.arc(115 + (400 * (x_axis + 10) / 20), 450 - (400 * (y_axis + 10) / 20), 15, 0, 2 * Math.PI);
```
*Adaptation*: Update coordinates to match your compass image layout.

#### 3. Progress Bar Updates (lines ~433-454)
```javascript
document.getElementById("econ-a").value = (10 + (ax_axis / 1.05));
```
*Adaptation*: Match element IDs to your HTML progress bars.

#### 4. Ideology Matching (lines ~495-525)
```javascript
dist += Math.pow(Math.abs(ideologies[i].stats.econ - xvalue), 2)
```
*Adaptation*: Update stat names to match your ideology definitions.

**HTML Progress Bars** (lines ~57-197):
```html
<div class="center">
    <div class="label-l"><label>Capitalism</label></div>
    <progress id="econ-a" value="10" max="20"> </progress>
    <div class="label-r"><label>Socialism</label></div>
</div>
```
*Adaptation*: Change labels and IDs for your axes.

---

### `ideologies.js`

**Purpose**: Define political ideologies with axis positions for result matching.

**Format**:
```javascript
ideologies = [
    {
        "name": "Liberalism",
        "link": "https://en.wikipedia.org/wiki/Liberalism",
        "desc": "Description text...",
        "next": "Classical Liberalism, Neoliberalism",
        "stats": {
            "econ": 7,        // Maps to x_axis
            "govt": -1,       // Maps to y_axis
            "econsys": 7,     // Maps to ax_axis
            "econreg": -7,    // Maps to ay_axis
            // ... all axes (-10 to +10 scale)
        }
    },
    // ... more ideologies
];
```

**Stats Key Mapping**:
| Stats Key | URL Param | Description |
|-----------|-----------|-------------|
| `econ` | `x` | General left-right |
| `govt` | `y` | General auth-lib |
| `econsys` | `ax` | Economic system |
| `econreg` | `ay` | Economic regulation |
| `govtsize` | `bx` | Government size |
| `govttype` | `by` | Government type |
| `diplapp` | `cx` | Diplomatic applicability |
| `diplrel` | `cy` | Diplomatic relations |
| `sctychg` | `dx` | Societal change |
| `sctytrad` | `dy` | Societal tradition |
| `techacc` | `ex` | Tech acceleration |
| `techbio` | `ey` | Transhumanism |
| `lawtype` | `fx` | Law system |
| `lawrehab` | `fy` | Law focus |
| `cltrhrchy` | `gx` | Cultural hierarchy |
| `cltrassi` | `gy` | Cultural assimilation |
| `proccomp` | `hx` | Procedural compromise |
| `proctrans` | `hy` | Procedural transition |
| `polapol` | `ix` | Political engagement |
| `polext` | `iy` | Political extremism |

---

### `quiz.html`

**Purpose**: Quiz engine that presents questions and collects answers.

**Key Functions**:

| Function | Purpose |
|----------|---------|
| `init_question()` | Display current question |
| `next_question(answer)` | Record answer, advance to next |
| `prev_question()` | Go back to previous question |
| `results()` | Calculate scores, redirect to results |
| `percentageCalculation()` | Core scoring algorithm |

**Answer Weights**:
```javascript
// Button onclick values:
Strongly Agree:    1.0
Agree:             0.5
Partially Agree:   0.3
Neutral:           0.0
Partially Disagree:-0.3
Disagree:         -0.5
Strongly Disagree:-1.0
```

*Adaptation*: Usually no changes needed unless adding custom answer types.

---

### `compassimage.png`

**Purpose**: Background image for the 10-compass grid display.

**Specifications**:
- Dimensions: 2500 × 1500 pixels (approximate)
- Layout: 4 columns × 3 rows of compass grids
- Each grid: ~400 × 400 pixel area
- Canvas offset positions defined in `results.html`

**Current Layout**:
```
Row 1: General | Economic | Governmental | Diplomatic
Row 2: Societal | Technological | Law | Cultural
Row 3: Procedural | Political | Moral (4 axes)
```

*Adaptation*: Create new image matching your axis pairs and categories.

---

### `axes.css`

**Purpose**: Color styling for result progress bars.

**Pattern**:
```css
#econ-a, #econ-b { /* Base styles */ }
#econ-a::-webkit-progress-value { /* Left color */ }
#econ-a::-webkit-progress-bar { /* Right color */ }
```

**Color Scheme by Category**:
| Category | Left Color | Right Color |
|----------|------------|-------------|
| Economic | #FFFACD | #CD5C5C |
| Governmental | #FFE4B5 | #6ebbfa |
| Diplomatic | #AFEEEE | #B0C4DE |
| Societal | #6B8E23 | #D8BFD8 |
| Technological | #808000 | #ADD8E6 |
| Law | #A3E4D7 | #F5EEF8 |
| Cultural | #5499C7 | #F5B7B1 |
| Procedural | #DEB887 | #DDA0DD |
| Political | #222222 | #a9a9a9 |
| Moral | Various | Various |

*Adaptation*: Add new ID selectors matching your progress bar IDs.

---

## Adaptation Checklist

When creating a new quiz with different axes:

### 1. Questions
- [ ] Create new `questions.js` with your axis codes in effects
- [ ] Ensure IDs are unique
- [ ] Balance positive/negative effects across questions

### 2. Results Display
- [ ] Update `results.html` URL parameter parsing
- [ ] Modify canvas drawing coordinates for compass positions
- [ ] Add/update progress bar HTML with correct IDs and labels
- [ ] Update progress bar value assignments

### 3. Styling
- [ ] Add CSS selectors for new progress bar IDs in `axes.css`
- [ ] Choose color schemes for each axis pair

### 4. Images
- [ ] Create new `compassimage.png` with your layout
- [ ] Optionally update `heatmap_img.png`

### 5. Ideology Matching (Optional)
- [ ] Create `ideologies.js` with profiles matching your axes
- [ ] Update matching algorithm in `results.html`

### 6. Landing Page
- [ ] Update `index.html` descriptions and category explanations
- [ ] Update question count if changed

---

## Files Safe to Modify vs. Critical Dependencies

### Safe to Modify
- `index.html` - Content only, no logic
- `instructions.html` - Content and button links
- `style.css` - Visual styling
- `none.html` - Placeholder content
- Images - Replace with same dimensions

### Critical Dependencies
- `questions.js` ↔ `quiz.html` - Effects keys must match
- `results.html` URL params ↔ All axis codes
- `results.html` element IDs ↔ `axes.css` selectors
- `ideologies.js` stat keys ↔ `results.html` matching code

---

## Testing Recommendations

1. **Question Loading**: Verify all questions display in quiz
2. **Score Calculation**: Test extreme answers (all agree / all disagree)
3. **Results Display**: Confirm circles appear in correct positions
4. **Progress Bars**: Verify bars fill in correct direction
5. **URL Sharing**: Test that result URLs reproduce same visualization

