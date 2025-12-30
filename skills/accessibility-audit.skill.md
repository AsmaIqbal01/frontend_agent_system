# Accessibility Audit Skill

## Skill Identity
- **Name**: Accessibility Audit Skill
- **Type**: Quality Assurance Skill
- **Domain**: Accessibility, WCAG Compliance
- **Version**: 1.0.0
- **Responsibility**: Perform rapid accessibility assessment and identify critical issues

---

## Purpose

This skill provides a systematic checklist for auditing UI components, pages, or features for accessibility compliance. It helps agents quickly identify WCAG violations, keyboard navigation issues, and screen reader problems without requiring deep accessibility expertise.

---

## When to Use This Skill

Use this skill when:
- ✅ Reviewing a component specification for accessibility requirements
- ✅ Validating an implemented UI for accessibility compliance
- ✅ Performing pre-delivery accessibility check
- ✅ Identifying accessibility gaps before formal testing
- ✅ Triaging accessibility issues by severity
- ✅ Creating accessibility improvement recommendations

Do NOT use this skill for:
- ❌ Implementing accessibility fixes (use Accessibility Agent)
- ❌ Writing ARIA code (use Accessibility Agent)
- ❌ Designing accessible UX flows (use UX Agent with accessibility constraints)
- ❌ Comprehensive WCAG 2.1 AA certification (requires formal audit)

---

## Inputs

### Required Inputs
1. **Audit Target**: Component name, page, or feature to audit
2. **Audit Type**: Specification Review OR Implementation Review
3. **Component/Page Description**: What is being audited

### Optional Inputs
4. **WCAG Level**: A, AA (default), or AAA
5. **Priority Focus**: Keyboard, Screen Reader, Visual, or All (default)
6. **Framework Context**: React, Vue, HTML (for code examples)

---

## Outputs

### Primary Outputs
1. **Accessibility Issues List**: Categorized by severity (Critical, High, Medium, Low)
2. **WCAG Success Criteria Status**: Which criteria pass/fail
3. **Recommendations**: Specific fixes for each issue
4. **Priority Score**: Overall accessibility score (0-100)

### Secondary Outputs
5. **Quick Wins**: Easy fixes that improve accessibility significantly
6. **Testing Checklist**: Manual tests to perform
7. **Code Examples**: How to fix common issues (framework-specific)

---

## Audit Checklist

### Category 1: Semantic HTML (WCAG 1.3.1, 4.1.1, 4.1.2)

**Question**: Is proper semantic HTML used?

**Checklist**:
- [ ] **Headings**: Proper hierarchy (h1 → h2 → h3, only one h1 per page)
  - ❌ **Issue**: Headings skipped (h1 → h3), multiple h1s, or visual headings using `<div class="heading">`
  - ✅ **Fix**: Use semantic heading tags in proper order

- [ ] **Landmarks**: Proper HTML5 landmarks used
  - ❌ **Issue**: Missing `<header>`, `<nav>`, `<main>`, `<aside>`, `<footer>`
  - ✅ **Fix**: Use semantic landmarks or add `role="banner|navigation|main|complementary|contentinfo"`

- [ ] **Buttons**: Actual `<button>` elements for actions
  - ❌ **Issue**: `<div onClick={...}>` or `<a onClick={...}>` for buttons
  - ✅ **Fix**: Use `<button type="button">` for actions, `<a href="...">` only for navigation

- [ ] **Links**: Proper `<a>` with `href` for navigation
  - ❌ **Issue**: `<a>` without `href`, or `<button>` for navigation
  - ✅ **Fix**: Links must have `href` attribute

- [ ] **Lists**: `<ul>`, `<ol>`, `<li>` for lists
  - ❌ **Issue**: List items using `<div>` with styling
  - ✅ **Fix**: Use proper list elements

- [ ] **Tables**: `<table>`, `<th>`, `<td>` for tabular data
  - ❌ **Issue**: Tables for layout, or data tables missing headers
  - ✅ **Fix**: Use CSS Grid/Flexbox for layout, add `<th>` with `scope` for data tables

**Severity**: Critical (blocks screen readers)

---

### Category 2: Keyboard Navigation (WCAG 2.1.1, 2.1.2, 2.4.3, 2.4.7)

**Question**: Is all functionality keyboard accessible?

**Checklist**:
- [ ] **Tab Navigation**: All interactive elements reachable via Tab
  - ❌ **Issue**: Custom elements without `tabindex`, or elements with `tabindex > 0`
  - ✅ **Fix**: Native elements (button, a, input) are focusable by default. For custom elements, add `tabindex="0"`

- [ ] **Tab Order**: Logical tab order matching visual order
  - ❌ **Issue**: Tab order jumps around illogically, `tabindex="1,2,3..."` creating custom order
  - ✅ **Fix**: Use source order for tab order, avoid positive tabindex values

- [ ] **Focus Visible**: Visible focus indicator on all elements
  - ❌ **Issue**: `outline: none` without replacement, or focus indicator has insufficient contrast
  - ✅ **Fix**: Use `:focus-visible` with 3:1 contrast minimum (2px outline or box-shadow)

- [ ] **No Keyboard Trap**: Can navigate away from all elements
  - ❌ **Issue**: Modal without Escape key, carousel that traps focus
  - ✅ **Fix**: Modals need Escape to close, carousels need keyboard controls

- [ ] **Keyboard Shortcuts**: Space/Enter activates buttons, arrow keys work where expected
  - ❌ **Issue**: Custom interactive element doesn't respond to Space/Enter
  - ✅ **Fix**: Add `onKeyDown` handling for Space (key code 32) and Enter (13)

- [ ] **Skip Links**: Skip to main content link for keyboard users
  - ❌ **Issue**: No skip link, making keyboard users tab through entire navigation
  - ✅ **Fix**: Add visually-hidden skip link as first focusable element: `<a href="#main">Skip to main content</a>`

**Severity**: Critical (blocks keyboard users)

**Quick Test**: Tab through entire interface with keyboard only. Can you reach everything? Is focus visible?

---

### Category 3: Screen Reader Support (WCAG 1.1.1, 2.4.4, 2.4.6, 3.3.2, 4.1.2)

**Question**: Is content properly announced to screen readers?

**Checklist**:
- [ ] **Form Labels**: All inputs have associated labels
  - ❌ **Issue**: Input without label, or placeholder as label
  - ✅ **Fix**: Use `<label for="id">` or wrap input with label, add `aria-label` for icon-only inputs

- [ ] **Image Alt Text**: All images have meaningful alt text
  - ❌ **Issue**: Missing alt attribute, or `alt="image"` (not descriptive)
  - ✅ **Fix**: Informative images get descriptive alt, decorative images get `alt=""`

- [ ] **Link Text**: Links have descriptive text (not "click here")
  - ❌ **Issue**: "Click here", "Read more", "Learn more" without context
  - ✅ **Fix**: "Read more about accessibility" or use `aria-label` to add context

- [ ] **Button Text**: Buttons have clear text or aria-label
  - ❌ **Issue**: Icon-only button without aria-label
  - ✅ **Fix**: Add `aria-label="Close dialog"` for icon buttons

- [ ] **Form Errors**: Errors linked to inputs and announced
  - ❌ **Issue**: Error message not associated with field
  - ✅ **Fix**: Use `aria-describedby="error-id"` and `aria-invalid="true"`, error in `role="alert"`

- [ ] **Live Regions**: Dynamic content updates announced
  - ❌ **Issue**: Loading/success/error states not announced
  - ✅ **Fix**: Use `aria-live="polite|assertive"` for status updates

- [ ] **Hidden Content**: Decorative content hidden from screen readers
  - ❌ **Issue**: Decorative icons announced, causing clutter
  - ✅ **Fix**: Add `aria-hidden="true"` to decorative SVGs/icons

**Severity**: Critical (blocks screen reader users)

**Quick Test**: Read page/component description aloud. Does it make sense? Are all interactive elements described?

---

### Category 4: Color & Contrast (WCAG 1.4.1, 1.4.3, 1.4.11)

**Question**: Is visual information accessible to users with low vision or color blindness?

**Checklist**:
- [ ] **Text Contrast**: 4.5:1 for normal text, 3:1 for large text (18px+ or 14px+ bold)
  - ❌ **Issue**: Light gray text on white background (2.5:1)
  - ✅ **Fix**: Use darker text color (#767676 = 4.5:1 on white)
  - **Tool**: Use browser DevTools or WebAIM contrast checker

- [ ] **UI Component Contrast**: 3:1 for buttons, inputs, focus indicators
  - ❌ **Issue**: Subtle button borders (1.5:1), light focus outline
  - ✅ **Fix**: Ensure all interactive elements have 3:1 contrast with background

- [ ] **Color Not Sole Indicator**: Don't rely on color alone to convey information
  - ❌ **Issue**: Red/green for error/success with no icon or text
  - ✅ **Fix**: Add icon (✓ for success, ⚠ for error) or text label

- [ ] **Focus Indicator Contrast**: Focus outline has 3:1 contrast
  - ❌ **Issue**: Light blue outline on white (2:1)
  - ✅ **Fix**: Use darker outline color or add background to outline

**Severity**: High (affects low vision, color blind users)

**Quick Test**: Use browser DevTools to check contrast ratios. Try viewing in grayscale mode.

---

### Category 5: Form Accessibility (WCAG 1.3.1, 3.3.1, 3.3.2, 3.3.3, 3.3.4)

**Question**: Are forms accessible and error-friendly?

**Checklist**:
- [ ] **Label Association**: Every input has visible label with `for` attribute
  - ❌ **Issue**: Label and input not associated
  - ✅ **Fix**: `<label for="email">Email</label> <input id="email">`

- [ ] **Required Fields**: Required fields marked visually AND with `aria-required`
  - ❌ **Issue**: Only asterisk (*) indicates required (not announced)
  - ✅ **Fix**: Add `aria-required="true"` or `required` attribute

- [ ] **Field Instructions**: Instructions provided before input (not after)
  - ❌ **Issue**: "Password must be 8+ characters" shown after password field
  - ✅ **Fix**: Show instructions before field or use `aria-describedby`

- [ ] **Error Identification**: Errors clearly identified and associated with fields
  - ❌ **Issue**: Generic "Form has errors" message at top
  - ✅ **Fix**: Link each error to field with `aria-describedby`, set `aria-invalid="true"`

- [ ] **Error Suggestions**: Errors provide suggestions to fix
  - ❌ **Issue**: "Invalid email" (not helpful)
  - ✅ **Fix**: "Please enter a valid email address (e.g., user@example.com)"

- [ ] **Error Prevention**: Confirmation for destructive actions
  - ❌ **Issue**: Delete button with no confirmation
  - ✅ **Fix**: Show confirmation dialog: "Are you sure you want to delete this item?"

**Severity**: High (blocks form submission for assistive tech users)

**Quick Test**: Fill out form with keyboard only. Are errors clear and actionable?

---

### Category 6: ARIA Usage (WCAG 4.1.2)

**Question**: Is ARIA used correctly (or is native HTML used instead)?

**Checklist**:
- [ ] **First Rule of ARIA**: Use native HTML first
  - ❌ **Issue**: `<div role="button">` instead of `<button>`
  - ✅ **Fix**: Prefer `<button>` over `<div role="button">`

- [ ] **ARIA Roles**: Correct roles for custom components
  - ❌ **Issue**: Modal without `role="dialog"`, tabs without `role="tablist"`
  - ✅ **Fix**: Add appropriate ARIA roles for custom widgets

- [ ] **ARIA States**: Dynamic states reflected in ARIA
  - ❌ **Issue**: Accordion expanded but `aria-expanded` still "false"
  - ✅ **Fix**: Update `aria-expanded`, `aria-selected`, `aria-pressed` on state change

- [ ] **ARIA Labels**: Descriptive labels for custom components
  - ❌ **Issue**: Modal without `aria-labelledby` or `aria-label`
  - ✅ **Fix**: Add `aria-labelledby="modal-title"` pointing to heading

- [ ] **ARIA Descriptions**: Additional context via `aria-describedby`
  - ❌ **Issue**: Complex component without description
  - ✅ **Fix**: Use `aria-describedby="description-id"` for additional context

- [ ] **ARIA Live Regions**: Status updates announced
  - ❌ **Issue**: "Saving..." → "Saved!" not announced
  - ✅ **Fix**: Wrap status in `<div role="status" aria-live="polite">`

**Severity**: Medium to High (improper ARIA worse than no ARIA)

**Quick Test**: Inspect accessibility tree in DevTools. Are roles and labels correct?

---

### Category 7: Responsive & Zoom (WCAG 1.4.4, 1.4.10)

**Question**: Does the interface work at different sizes and zoom levels?

**Checklist**:
- [ ] **200% Zoom**: Interface works at 200% browser zoom without horizontal scroll
  - ❌ **Issue**: Horizontal scrollbar appears, content cut off
  - ✅ **Fix**: Use responsive units (rem, %, vw), test at 200% zoom

- [ ] **Mobile Responsive**: Works on mobile devices (320px width minimum)
  - ❌ **Issue**: Layout breaks on mobile, text too small
  - ✅ **Fix**: Mobile-first design, test at 320px width

- [ ] **Text Resize**: Text can be resized without breaking layout
  - ❌ **Issue**: Fixed-height containers cut off text when resized
  - ✅ **Fix**: Use min-height instead of height, allow containers to grow

- [ ] **Touch Targets**: Tap targets at least 44x44 pixels on mobile
  - ❌ **Issue**: Small buttons (20x20px) hard to tap
  - ✅ **Fix**: Ensure all tap targets are 44x44px minimum

**Severity**: Medium (affects mobile users, low vision users)

**Quick Test**: Zoom to 200%, resize to mobile width (320px). Does everything still work?

---

### Category 8: Content Structure (WCAG 1.3.2, 2.4.2, 2.4.6, 3.1.1)

**Question**: Is content well-structured and easy to navigate?

**Checklist**:
- [ ] **Page Title**: Descriptive, unique page title
  - ❌ **Issue**: All pages titled "My App"
  - ✅ **Fix**: `<title>Login - My App</title>`, `<title>Dashboard - My App</title>`

- [ ] **Reading Order**: Visual order matches source order
  - ❌ **Issue**: CSS positioning makes tab order illogical
  - ✅ **Fix**: Ensure DOM order matches visual order

- [ ] **Language**: Page language declared
  - ❌ **Issue**: No `lang` attribute on `<html>`
  - ✅ **Fix**: `<html lang="en">`

- [ ] **Headings Descriptive**: Headings describe section content
  - ❌ **Issue**: "Section 1", "Section 2" (not descriptive)
  - ✅ **Fix**: "User Profile", "Account Settings" (descriptive)

- [ ] **Consistent Navigation**: Navigation in same place on all pages
  - ❌ **Issue**: Navigation moves between pages
  - ✅ **Fix**: Keep navigation structure consistent

**Severity**: Medium (affects navigation and comprehension)

---

## Severity Levels

### Critical (Must Fix Before Release)
- No keyboard access to functionality
- No screen reader access to content
- Missing form labels
- No text alternatives for images
- Semantic HTML violations that block assistive tech

**Impact**: Completely blocks users with disabilities

---

### High (Should Fix Before Release)
- Insufficient color contrast
- Missing focus indicators
- Forms without proper error handling
- Missing ARIA for custom components
- Keyboard trap in non-modal contexts

**Impact**: Significantly degrades experience for users with disabilities

---

### Medium (Fix in Next Iteration)
- Non-descriptive link text ("click here")
- Missing skip links
- Inconsistent navigation
- Non-responsive at 200% zoom
- Decorative images not hidden from screen readers

**Impact**: Makes experience harder but not impossible

---

### Low (Nice to Have)
- AAA contrast (7:1) instead of AA (4.5:1)
- Additional ARIA descriptions
- Keyboard shortcuts beyond standard
- Optimizations for specific screen readers

**Impact**: Enhances experience but not required for compliance

---

## Audit Scoring

### Calculate Accessibility Score (0-100)

**Formula**:
```
Score = 100 - (Critical × 20) - (High × 10) - (Medium × 5) - (Low × 2)
```

**Example**:
- 2 Critical issues: -40 points
- 3 High issues: -30 points
- 5 Medium issues: -25 points
- 3 Low issues: -6 points
- **Total Score**: 100 - 40 - 30 - 25 - 6 = **-1 → 0/100** (minimum 0)

**Grading**:
- **90-100**: Excellent (WCAG AA compliant)
- **75-89**: Good (minor issues)
- **60-74**: Fair (notable issues)
- **40-59**: Poor (significant issues)
- **0-39**: Failing (critical issues)

---

## Quick Audit Process

### 5-Minute Quick Audit

1. **Keyboard Test** (2 min)
   - Tab through entire page
   - Check: Can reach everything? Is focus visible?

2. **Heading Check** (1 min)
   - View heading outline (browser extension)
   - Check: Proper hierarchy? Descriptive?

3. **Contrast Check** (1 min)
   - Use DevTools to check 3-5 text samples
   - Check: All above 4.5:1?

4. **Form Review** (1 min)
   - Inspect form inputs
   - Check: Labels associated? Required marked?

**Output**: Pass/Fail, list of critical issues

---

### 15-Minute Standard Audit

1. **Semantic HTML** (3 min) - Run through Category 1 checklist
2. **Keyboard Navigation** (3 min) - Run through Category 2 checklist
3. **Screen Reader** (3 min) - Run through Category 3 checklist
4. **Color/Contrast** (2 min) - Run through Category 4 checklist
5. **Forms** (2 min) - Run through Category 5 checklist
6. **ARIA** (2 min) - Run through Category 6 checklist

**Output**: Categorized issues, severity levels, score

---

### 30-Minute Comprehensive Audit

1. Run all 8 category checklists (20 min)
2. Test with screen reader (5 min) - NVDA or VoiceOver
3. Test with automated tool (5 min) - axe DevTools
4. Generate report with score, issues, recommendations

**Output**: Full audit report with testing evidence

---

## Framework-Specific Quick Fixes

### React

**Fix: Button without keyboard support**
```jsx
// ❌ Bad
<div onClick={handleClick}>Click me</div>

// ✅ Good
<button type="button" onClick={handleClick}>
  Click me
</button>
```

**Fix: Missing form label**
```jsx
// ❌ Bad
<input type="email" placeholder="Email" />

// ✅ Good
<label htmlFor="email">Email address</label>
<input type="email" id="email" name="email" />
```

**Fix: Icon button without label**
```jsx
// ❌ Bad
<button onClick={handleClose}>
  <XIcon />
</button>

// ✅ Good
<button onClick={handleClose} aria-label="Close dialog">
  <XIcon aria-hidden="true" />
</button>
```

---

### Vue

**Fix: Missing focus indicator**
```vue
<style>
/* ❌ Bad - removes focus outline */
button:focus {
  outline: none;
}

/* ✅ Good - adds visible focus indicator */
button:focus-visible {
  outline: 2px solid #0066cc;
  outline-offset: 2px;
}
</style>
```

---

### HTML

**Fix: Low contrast text**
```css
/* ❌ Bad - 2.5:1 contrast */
.text-muted {
  color: #999999; /* on white background */
}

/* ✅ Good - 4.6:1 contrast */
.text-muted {
  color: #767676; /* on white background */
}
```

---

## Example Audit Report

### Component: Login Form

**Audit Date**: 2024-01-15
**Auditor**: Accessibility Agent
**WCAG Level**: AA
**Score**: **42/100** (Poor - Failing)

---

#### Critical Issues (3)

1. **Missing Form Labels**
   - **WCAG**: 1.3.1, 3.3.2
   - **Issue**: Email and password inputs have no associated labels
   - **Impact**: Screen reader users cannot identify fields
   - **Fix**: Add `<label for="email">` and `<label for="password">`
   - **Location**: LoginForm.tsx lines 23, 28

2. **Button Not Keyboard Accessible**
   - **WCAG**: 2.1.1
   - **Issue**: Submit action on `<div onClick>` instead of `<button>`
   - **Impact**: Keyboard users cannot submit form
   - **Fix**: Replace `<div onClick={submit}>` with `<button type="submit">`
   - **Location**: LoginForm.tsx line 35

3. **No Focus Indicator**
   - **WCAG**: 2.4.7
   - **Issue**: `outline: none` on inputs without replacement
   - **Impact**: Keyboard users cannot see where focus is
   - **Fix**: Remove `outline: none` or add `:focus-visible` with 3:1 contrast
   - **Location**: LoginForm.module.css line 12

---

#### High Issues (2)

1. **Low Contrast Error Text**
   - **WCAG**: 1.4.3
   - **Issue**: Error message color #cc0000 on white = 3.8:1 (needs 4.5:1)
   - **Impact**: Low vision users cannot read errors
   - **Fix**: Change to #d00000 for 4.6:1 contrast
   - **Location**: LoginForm.module.css line 45

2. **Form Errors Not Announced**
   - **WCAG**: 3.3.1, 4.1.3
   - **Issue**: Errors displayed but not announced to screen readers
   - **Impact**: Screen reader users don't know validation failed
   - **Fix**: Add `role="alert"` and `aria-live="assertive"` to error container
   - **Location**: LoginForm.tsx line 40

---

#### Medium Issues (3)

1. Missing "Remember me" label association
2. No skip link for keyboard users
3. Page title not descriptive ("App" instead of "Login - App")

---

#### Recommendations

**Quick Wins** (30 min effort, high impact):
1. Add form labels (10 min)
2. Change `<div>` to `<button>` (5 min)
3. Fix error text contrast (5 min)
4. Add error announcements (10 min)

**After Quick Wins**: Score improves to **80/100** (Good)

**Next Iteration**:
1. Add focus indicators
2. Associate "Remember me" label
3. Add skip link
4. Update page title

**Full Compliance ETA**: 1-2 hours

---

## Success Criteria

This skill is successfully applied when:

1. ✅ **Issues Identified**: All accessibility issues found and categorized
2. ✅ **Severity Assigned**: Each issue has Critical/High/Medium/Low severity
3. ✅ **WCAG Referenced**: Issues mapped to WCAG success criteria
4. ✅ **Fixes Provided**: Specific, actionable fix for each issue
5. ✅ **Score Calculated**: Objective accessibility score (0-100)
6. ✅ **Quick Wins Identified**: High-impact, low-effort fixes highlighted
7. ✅ **Testing Methods**: Manual and automated testing methods noted
8. ✅ **Report Generated**: Clear, actionable audit report
9. ✅ **Timeline Estimated**: Realistic fix timeline provided
10. ✅ **Re-audit Path**: Clear path to compliance

---

## Version History

- **1.0.0** (2024-01-15): Initial Accessibility Audit Skill
  - Created 8-category audit checklist
  - Defined 4 severity levels
  - Provided scoring formula
  - Documented quick audit processes (5/15/30 min)
  - Included framework-specific quick fixes
  - Provided example audit report
  - Established success criteria
