# Layout Consistency Skill

## Skill Identity
- **Name**: Layout Consistency Skill
- **Type**: Design System Skill
- **Domain**: Layout, Spacing, Sizing, Visual Consistency
- **Version**: 1.0.0
- **Responsibility**: Ensure consistent spacing, sizing, and layout patterns across the application

---

## Purpose

This skill analyzes layouts for consistency issues and provides systematic approaches to maintain visual harmony through spacing scales, sizing systems, and reusable layout patterns. It helps agents make decisions about spacing, sizing, and layout structure that create cohesive user interfaces.

---

## When to Use This Skill

Use this skill when:
- ✅ Defining spacing and sizing tokens for a design system
- ✅ Reviewing components/pages for layout inconsistencies
- ✅ Deciding spacing values (margins, padding, gaps)
- ✅ Choosing layout patterns (grid, flexbox, stack)
- ✅ Ensuring visual rhythm and alignment
- ✅ Creating responsive layout breakpoints

Do NOT use this skill for:
- ❌ Choosing colors or typography (different skills)
- ❌ Implementing interactive behaviors (use UX Agent)
- ❌ Writing layout code (use UI Agent)
- ❌ Managing layout state (use State Agent)

---

## Inputs

### Required Inputs
1. **Analysis Target**: Component, page, or entire application
2. **Current Layout**: Description or implementation to analyze
3. **Consistency Goal**: High (design system), Medium (feature-level), Low (page-specific)

### Optional Inputs
4. **Design Tokens**: Existing spacing/sizing values (if any)
5. **Framework Context**: CSS, Tailwind, CSS-in-JS (for examples)
6. **Viewport Sizes**: Target breakpoints (mobile, tablet, desktop)

---

## Outputs

### Primary Outputs
1. **Consistency Issues**: List of spacing/sizing inconsistencies found
2. **Spacing Scale**: Recommended spacing system (e.g., 4px, 8px, 16px, 24px...)
3. **Sizing Scale**: Recommended sizing system for widths, heights, icons
4. **Layout Patterns**: Recommended layout primitives and patterns

### Secondary Outputs
5. **Design Tokens**: CSS variables or constants for spacing/sizing
6. **Quick Fixes**: Immediate corrections for inconsistencies
7. **Layout Guidelines**: Rules for maintaining consistency

---

## Core Concepts

### The 8-Point Grid System

**Concept**: All spacing and sizing uses multiples of 8px (or 4px for fine-tuning)

**Why 8px?**
- Divisible by 2, 4, 8 (flexible scaling)
- Works well across device resolutions
- Creates consistent visual rhythm
- Industry standard (Material Design, iOS HIG)

**Spacing Scale (8px base)**:
```
0:   0px    (none)
1:   4px    (micro - fine-tuning only)
2:   8px    (xs - tight spacing)
3:   12px   (sm - between related items)
4:   16px   (md - default spacing)
5:   24px   (lg - between sections)
6:   32px   (xl - large gaps)
7:   48px   (2xl - very large gaps)
8:   64px   (3xl - section spacing)
9:   96px   (4xl - page-level spacing)
10:  128px  (5xl - hero spacing)
```

**Alternative: 4px base** (finer control)
```
1:  4px
2:  8px
3:  12px
4:  16px
5:  20px
6:  24px
8:  32px
10: 40px
12: 48px
16: 64px
```

---

### Layout Primitives

**Stack** (Vertical or Horizontal)
- Purpose: Arrange items in single direction with consistent spacing
- Use: Lists, form fields, navigation items, card grids

**Cluster** (Flexbox wrap)
- Purpose: Group items that wrap to new line
- Use: Tags, chips, toolbar buttons, inline lists

**Sidebar** (Two-column with fixed sidebar)
- Purpose: Main content with fixed or flexible sidebar
- Use: Dashboard, settings page, documentation

**Cover** (Centered content with padding)
- Purpose: Center content vertically with min-height
- Use: Hero sections, error pages, loading states

**Grid** (CSS Grid)
- Purpose: Two-dimensional layout with rows and columns
- Use: Product catalogs, image galleries, dashboard widgets

**Center** (Centered container)
- Purpose: Horizontally center content with max-width
- Use: Article content, forms, card containers

---

## Decision-Making Criteria

### 1. What Spacing Value Should I Use?

**Decision Tree**:
```
What's the relationship between elements?

├─ Same component (button icon + text)
│   └─ Use: xs (8px) or sm (12px)
│
├─ Related items in group (form label + input)
│   └─ Use: sm (12px) or md (16px)
│
├─ Different components same section (card + card)
│   └─ Use: md (16px) or lg (24px)
│
├─ Different sections (header + main content)
│   └─ Use: xl (32px) or 2xl (48px)
│
└─ Page-level spacing (hero + features section)
    └─ Use: 2xl (48px), 3xl (64px), or 4xl (96px)
```

**Examples**:

```css
/* Icon + Button Text: xs (8px) */
.button {
  display: flex;
  gap: 8px; /* Icon and text are tightly related */
}

/* Form Label + Input: sm (12px) */
.form-field {
  display: flex;
  flex-direction: column;
  gap: 12px; /* Label describes input */
}

/* Card + Card: lg (24px) */
.card-grid {
  display: grid;
  gap: 24px; /* Separate cards, same context */
}

/* Section + Section: 2xl (48px) */
.page-sections {
  display: flex;
  flex-direction: column;
  gap: 48px; /* Clear visual separation */
}
```

---

### 2. What Layout Pattern Should I Use?

**Decision Matrix**:

| Use Case | Pattern | Why |
|----------|---------|-----|
| Vertical list of items | **Stack** | Simple, predictable, accessible |
| Form with labels and inputs | **Stack** | Natural top-to-bottom flow |
| Navigation bar items | **Stack** (horizontal) | Evenly spaced, aligned |
| Tags/badges that wrap | **Cluster** | Wraps naturally, consistent spacing |
| Product catalog (equal-size items) | **Grid** | Structured, consistent sizing |
| Dashboard (different-size widgets) | **Grid** | Flexible, responsive |
| Article with sidebar | **Sidebar** | Content focus, related info aside |
| Hero section | **Cover** | Vertically centered, full height |
| Centered form | **Center** | Focused, clear hierarchy |

**Decision Tree**:
```
How many dimensions?

├─ One direction (row OR column)
│   ├─ Items should NOT wrap
│   │   └─ Use: Stack (overflow-x: auto if needed)
│   └─ Items should wrap
│       └─ Use: Cluster
│
└─ Two directions (rows AND columns)
    ├─ Items same size
    │   └─ Use: Grid (grid-template-columns: repeat(auto-fill, minmax(...)))
    └─ Items different sizes
        └─ Use: Grid (grid-template-areas or flexible fr units)
```

---

### 3. Responsive Breakpoints

**Question**: At what viewport widths should layout change?

**Standard Breakpoints** (Mobile-first):

```css
/* Mobile: 0-639px (default, no media query) */

/* Tablet: 640px+ */
@media (min-width: 640px) { }

/* Desktop: 1024px+ */
@media (min-width: 1024px) { }

/* Large Desktop: 1280px+ */
@media (min-width: 1280px) { }
```

**Tailwind-style** (common standard):
```
sm:  640px
md:  768px
lg:  1024px
xl:  1280px
2xl: 1536px
```

**Content-based Breakpoints**:
Instead of arbitrary values, break where content needs it:
```css
/* Break when sidebar becomes cramped */
@media (min-width: 800px) {
  .layout { grid-template-columns: 250px 1fr; }
}

/* Break when 3 columns fit comfortably */
@media (min-width: 900px) {
  .grid { grid-template-columns: repeat(3, 1fr); }
}
```

---

### 4. Container Width Strategy

**Question**: Should container be full-width or constrained?

**Decision Criteria**:

**Full-width** (`width: 100%`):
- Hero sections, banners
- Dashboard layouts
- Image backgrounds
- Sidebars

**Constrained** (`max-width: ...`):
- Article content (max-width: 65ch for readability)
- Forms (max-width: 600px prevents wide inputs)
- Centered cards (max-width: 500px)
- Page content (max-width: 1200px prevents ultra-wide)

**Hybrid** (Full background, constrained content):
```html
<section class="full-width bg-gray">
  <div class="constrained-content max-w-1200">
    Content here
  </div>
</section>
```

---

## Layout Consistency Checklist

### ✅ Spacing Consistency

**Check**: Are spacing values from defined scale?

**Audit Process**:
1. List all margin/padding values in component/page
2. Compare against spacing scale (0, 4, 8, 12, 16, 24, 32, 48, 64...)
3. Flag inconsistent values (e.g., 18px, 23px, 37px)

**Example**:
```css
/* ❌ Inconsistent spacing */
.card {
  padding: 18px;        /* Not on scale */
  margin-bottom: 23px;  /* Not on scale */
}

.button {
  padding: 10px 15px;   /* Not on scale */
}

/* ✅ Consistent spacing (8px scale) */
.card {
  padding: 16px;        /* 16px = scale step 4 */
  margin-bottom: 24px;  /* 24px = scale step 5 */
}

.button {
  padding: 8px 16px;    /* 8px and 16px on scale */
}
```

**Common Issues**:
- Arbitrary values (17px, 22px, 35px)
- Magic numbers from eyeballing
- Copy-pasted values without checking scale

---

### ✅ Sizing Consistency

**Check**: Are widths, heights, icon sizes from defined scale?

**Sizing Scale**:
```
Icons:
- xs: 12px
- sm: 16px
- md: 20px (default)
- lg: 24px
- xl: 32px

Buttons:
- sm: height 32px, padding 8px 12px
- md: height 40px, padding 10px 16px (default)
- lg: height 48px, padding 12px 24px

Avatar:
- xs: 24px
- sm: 32px
- md: 40px (default)
- lg: 48px
- xl: 64px

Container widths:
- sm: 640px
- md: 768px
- lg: 1024px
- xl: 1280px
- 2xl: 1536px
```

**Example**:
```css
/* ❌ Inconsistent sizing */
.icon { width: 19px; height: 19px; }      /* Not on scale */
.avatar { width: 43px; height: 43px; }    /* Not on scale */
.button { height: 37px; }                  /* Not on scale */

/* ✅ Consistent sizing */
.icon { width: 20px; height: 20px; }      /* md icon */
.avatar { width: 40px; height: 40px; }    /* md avatar */
.button { height: 40px; }                  /* md button */
```

---

### ✅ Alignment Consistency

**Check**: Are elements aligned to grid?

**Audit Points**:
- Text baselines aligned horizontally
- Icons vertically centered with text
- Form inputs aligned left
- Card edges aligned in grid

**Example**:
```css
/* ❌ Misaligned icon and text */
.button {
  display: flex;
  /* Icon and text not centered */
}

/* ✅ Aligned icon and text */
.button {
  display: flex;
  align-items: center;  /* Vertically center icon and text */
  gap: 8px;
}
```

---

### ✅ Visual Rhythm

**Check**: Is there consistent spacing between similar elements?

**Pattern**: Same-type elements should have same spacing

**Example**:
```css
/* ❌ Inconsistent card spacing */
.featured-card {
  margin-bottom: 32px;
}

.regular-card {
  margin-bottom: 24px;  /* Different spacing for similar elements */
}

/* ✅ Consistent card spacing */
.card {
  margin-bottom: 24px;  /* All cards have same spacing */
}

.card.featured {
  /* Differentiate with border, shadow, not spacing */
  border: 2px solid blue;
}
```

---

### ✅ Responsive Consistency

**Check**: Do breakpoints follow defined values?

**Audit Process**:
1. List all media query breakpoints
2. Compare against standard breakpoints
3. Flag custom breakpoints without clear reason

**Example**:
```css
/* ❌ Arbitrary breakpoints */
@media (min-width: 673px) { }  /* Why 673? */
@media (min-width: 891px) { }  /* Inconsistent */
@media (min-width: 1150px) { } /* Not standard */

/* ✅ Standard breakpoints */
@media (min-width: 640px) { }  /* sm */
@media (min-width: 1024px) { } /* lg */
@media (min-width: 1280px) { } /* xl */
```

---

## Layout Patterns

### Pattern 1: Stack (Vertical or Horizontal List)

**Use When**: Arranging items in single direction with consistent spacing

**Structure**:
```css
.stack {
  display: flex;
  flex-direction: column; /* or row for horizontal */
  gap: var(--space-4);    /* 16px default */
}

/* Variants */
.stack-xs { gap: var(--space-2); }  /* 8px */
.stack-sm { gap: var(--space-3); }  /* 12px */
.stack-md { gap: var(--space-4); }  /* 16px */
.stack-lg { gap: var(--space-5); }  /* 24px */
.stack-xl { gap: var(--space-6); }  /* 32px */
```

**Usage**:
```html
<!-- Vertical stack of form fields -->
<form class="stack stack-md">
  <div class="form-field">...</div>
  <div class="form-field">...</div>
  <div class="form-field">...</div>
</form>

<!-- Horizontal stack of buttons -->
<div class="stack stack-sm" style="flex-direction: row;">
  <button>Cancel</button>
  <button>Save</button>
</div>
```

---

### Pattern 2: Cluster (Wrapping Items)

**Use When**: Items should wrap to new line with consistent spacing

**Structure**:
```css
.cluster {
  display: flex;
  flex-wrap: wrap;
  gap: var(--space-2);  /* 8px */
  align-items: center;
}
```

**Usage**:
```html
<!-- Tags that wrap -->
<div class="cluster">
  <span class="tag">JavaScript</span>
  <span class="tag">React</span>
  <span class="tag">TypeScript</span>
  <span class="tag">Node.js</span>
  <span class="tag">CSS</span>
</div>
```

---

### Pattern 3: Grid (Equal Columns)

**Use When**: Items should be in structured grid with consistent sizing

**Structure**:
```css
.grid {
  display: grid;
  gap: var(--space-4);  /* 16px */

  /* Auto-fit: Creates as many columns as fit */
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
}

/* Responsive variants */
.grid-2 { grid-template-columns: repeat(2, 1fr); }
.grid-3 { grid-template-columns: repeat(3, 1fr); }
.grid-4 { grid-template-columns: repeat(4, 1fr); }

@media (max-width: 768px) {
  .grid-2, .grid-3, .grid-4 {
    grid-template-columns: 1fr; /* Stack on mobile */
  }
}
```

**Usage**:
```html
<!-- Product grid -->
<div class="grid">
  <div class="product-card">...</div>
  <div class="product-card">...</div>
  <div class="product-card">...</div>
  <div class="product-card">...</div>
</div>
```

---

### Pattern 4: Sidebar Layout

**Use When**: Main content with fixed or flexible sidebar

**Structure**:
```css
.sidebar-layout {
  display: grid;
  gap: var(--space-6);  /* 32px */

  /* Mobile: Stack */
  grid-template-columns: 1fr;

  /* Desktop: Sidebar + Content */
  @media (min-width: 1024px) {
    grid-template-columns: 250px 1fr;  /* Fixed sidebar */
    /* OR */
    grid-template-columns: 1fr 3fr;    /* Flexible sidebar */
  }
}
```

**Usage**:
```html
<div class="sidebar-layout">
  <aside class="sidebar">
    <nav>...</nav>
  </aside>
  <main class="content">
    <h1>Main Content</h1>
  </main>
</div>
```

---

### Pattern 5: Center Container

**Use When**: Centering content with max-width

**Structure**:
```css
.center {
  max-width: var(--container-md);  /* 768px */
  margin-left: auto;
  margin-right: auto;
  padding-left: var(--space-4);    /* 16px */
  padding-right: var(--space-4);
}

/* Variants */
.center-sm { max-width: 640px; }   /* Forms */
.center-md { max-width: 768px; }   /* Articles */
.center-lg { max-width: 1024px; }  /* Pages */
.center-xl { max-width: 1280px; }  /* Wide layouts */
```

**Usage**:
```html
<article class="center center-md">
  <h1>Article Title</h1>
  <p>Article content with optimal reading width...</p>
</article>
```

---

### Pattern 6: Cover (Vertically Centered)

**Use When**: Content should be vertically centered with min-height

**Structure**:
```css
.cover {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  min-height: 100vh;  /* Or specific height */
  padding: var(--space-6);  /* 32px */
}
```

**Usage**:
```html
<!-- Hero section -->
<section class="cover">
  <h1>Welcome to Our App</h1>
  <p>Discover amazing features</p>
  <button>Get Started</button>
</section>

<!-- Error page -->
<main class="cover">
  <h1>404 - Page Not Found</h1>
  <a href="/">Go Home</a>
</main>
```

---

## Design Tokens (CSS Variables)

### Spacing Scale
```css
:root {
  --space-0: 0;
  --space-1: 0.25rem;  /* 4px */
  --space-2: 0.5rem;   /* 8px */
  --space-3: 0.75rem;  /* 12px */
  --space-4: 1rem;     /* 16px */
  --space-5: 1.5rem;   /* 24px */
  --space-6: 2rem;     /* 32px */
  --space-7: 3rem;     /* 48px */
  --space-8: 4rem;     /* 64px */
  --space-9: 6rem;     /* 96px */
  --space-10: 8rem;    /* 128px */
}
```

### Sizing Scale
```css
:root {
  /* Icon sizes */
  --icon-xs: 0.75rem;  /* 12px */
  --icon-sm: 1rem;     /* 16px */
  --icon-md: 1.25rem;  /* 20px */
  --icon-lg: 1.5rem;   /* 24px */
  --icon-xl: 2rem;     /* 32px */

  /* Container widths */
  --container-sm: 40rem;    /* 640px */
  --container-md: 48rem;    /* 768px */
  --container-lg: 64rem;    /* 1024px */
  --container-xl: 80rem;    /* 1280px */
  --container-2xl: 96rem;   /* 1536px */
}
```

### Breakpoints (JavaScript)
```javascript
const breakpoints = {
  sm: '640px',
  md: '768px',
  lg: '1024px',
  xl: '1280px',
  '2xl': '1536px'
}
```

---

## Framework-Specific Implementation

### Tailwind CSS

**Spacing** (built-in 4px scale):
```html
<div class="p-4 mb-6 gap-2">
  <!-- p-4 = padding: 16px -->
  <!-- mb-6 = margin-bottom: 24px -->
  <!-- gap-2 = gap: 8px -->
</div>
```

**Layout Patterns**:
```html
<!-- Stack -->
<div class="flex flex-col gap-4">

<!-- Cluster -->
<div class="flex flex-wrap gap-2">

<!-- Grid -->
<div class="grid grid-cols-3 gap-4">

<!-- Center -->
<div class="max-w-4xl mx-auto px-4">
```

---

### CSS-in-JS (styled-components, emotion)

```javascript
const spacing = {
  xs: '8px',
  sm: '12px',
  md: '16px',
  lg: '24px',
  xl: '32px',
}

const Stack = styled.div`
  display: flex;
  flex-direction: column;
  gap: ${spacing.md};
`

const Grid = styled.div`
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: ${spacing.lg};
`
```

---

### Plain CSS with Variables

```css
.card {
  padding: var(--space-4);
  margin-bottom: var(--space-5);
  border-radius: var(--radius-md);
}

.button {
  padding: var(--space-2) var(--space-4);
  gap: var(--space-2);
}
```

---

## Example: Consistency Audit Report

### Page: Product Catalog

**Audit Date**: 2024-01-15
**Auditor**: Layout Consistency Skill
**Score**: **68/100** (Fair)

---

#### Spacing Issues Found (8)

1. **Inconsistent Card Spacing**
   - **Issue**: Cards use 20px, 24px, and 28px margins inconsistently
   - **Location**: ProductGrid.css lines 12, 34, 56
   - **Fix**: Standardize to 24px (--space-5)
   - **Impact**: Visual rhythm disrupted

2. **Arbitrary Padding Values**
   - **Issue**: Button padding is 11px 17px (not on scale)
   - **Location**: Button.css line 8
   - **Fix**: Change to 12px 16px (--space-3 --space-4)
   - **Impact**: Inconsistent button sizing

3. **Non-standard Gap**
   - **Issue**: Product grid gap is 19px
   - **Location**: ProductGrid.css line 18
   - **Fix**: Change to 16px (--space-4) or 24px (--space-5)
   - **Impact**: Doesn't align with spacing scale

---

#### Sizing Issues Found (3)

1. **Custom Icon Sizes**
   - **Issue**: Icons are 18px, 22px, 26px (not standard sizes)
   - **Location**: Icons.css lines 5, 12, 19
   - **Fix**: Use 16px (sm), 20px (md), 24px (lg)
   - **Impact**: Inconsistent icon scaling

2. **Arbitrary Avatar Size**
   - **Issue**: User avatar is 42px
   - **Location**: UserAvatar.css line 7
   - **Fix**: Change to 40px (md) or 48px (lg)
   - **Impact**: Doesn't match avatar scale

---

#### Alignment Issues Found (2)

1. **Icon-Text Misalignment**
   - **Issue**: Icons not vertically centered with text in buttons
   - **Location**: Button.css line 15
   - **Fix**: Add `align-items: center` to flex container
   - **Impact**: Visual misalignment

2. **Grid Items Not Aligned**
   - **Issue**: Card heights vary, creating jagged grid
   - **Location**: ProductCard.css
   - **Fix**: Use `align-items: stretch` on grid or set min-height
   - **Impact**: Inconsistent grid appearance

---

#### Recommendations

**Quick Fixes** (1 hour):
1. Replace all spacing values with tokens (20px → --space-5, 11px → --space-3)
2. Standardize icon sizes to 16/20/24px
3. Fix button icon alignment with `align-items: center`
4. Update avatar to 40px

**After Fixes**: Estimated score **88/100** (Good)

**Design Tokens Implementation** (2 hours):
1. Create CSS variables for all spacing values
2. Create CSS variables for all sizing values
3. Replace hardcoded values with variables
4. Document spacing scale in design system

**Full Consistency**: Estimated score **95/100** (Excellent)

---

## Quick Reference

### Spacing Decision Chart

```
Icon ↔ Text in button:        8px  (xs)
Label → Input:                 12px (sm)
Form field → Form field:       16px (md)
Card → Card:                   24px (lg)
Section → Section:             48px (2xl)
Hero → Features section:       64px (3xl)
```

### Layout Pattern Selector

```
Single direction, no wrap:     Stack
Single direction, wraps:       Cluster
Two directions, equal sizes:   Grid (auto-fit)
Two directions, varied sizes:  Grid (template-areas)
Content + Sidebar:             Sidebar Layout
Centered with max-width:       Center Container
Vertically centered:           Cover
```

### Common Mistakes

❌ Spacing: 15px, 23px, 37px (arbitrary values)
✅ Spacing: 16px, 24px, 32px (on scale)

❌ Icons: 18px, 22px, 26px (custom sizes)
✅ Icons: 16px, 20px, 24px (standard sizes)

❌ Breakpoints: 675px, 892px (arbitrary)
✅ Breakpoints: 640px, 1024px (standard)

❌ Multiple spacing systems (4px here, 5px there)
✅ Single spacing system (8px scale everywhere)

---

## Success Criteria

This skill is successfully applied when:

1. ✅ **Spacing Scale Defined**: Clear spacing scale (4px or 8px base) established
2. ✅ **Sizing Scale Defined**: Icon, button, avatar sizes standardized
3. ✅ **Inconsistencies Identified**: All spacing/sizing issues found and documented
4. ✅ **Fixes Provided**: Specific replacements for each inconsistent value
5. ✅ **Tokens Created**: CSS variables or constants for spacing/sizing
6. ✅ **Patterns Documented**: Layout patterns (Stack, Grid, etc.) defined
7. ✅ **Breakpoints Standardized**: Consistent media query breakpoints
8. ✅ **Guidelines Established**: Rules for choosing spacing/sizing values
9. ✅ **Score Calculated**: Objective consistency score (0-100)
10. ✅ **Framework-Agnostic**: Principles work with any CSS approach

---

## Version History

- **1.0.0** (2024-01-15): Initial Layout Consistency Skill
  - Defined 8-point grid system
  - Created spacing and sizing scales
  - Documented 6 layout patterns (Stack, Cluster, Grid, Sidebar, Center, Cover)
  - Provided consistency checklist (5 categories)
  - Established design tokens structure
  - Included framework-specific implementations
  - Provided example audit report
  - Created quick reference guide
