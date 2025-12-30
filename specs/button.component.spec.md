# Button Component Specification

**Component Type:** UI / Interactive
**Single Responsibility:** Trigger actions or navigate when clicked
**Version:** 1.0.0

---

## Purpose

Provide a consistent, accessible, and reusable interactive element for user actions throughout the application. Support multiple visual variants, sizes, states, and icon combinations while maintaining accessibility and usability standards.

---

## Component Responsibility

**Single Responsibility:** Act as a clickable trigger for user actions

**Does:**
- Render clickable button element
- Display text label and/or icon
- Show loading, disabled, and active states
- Handle click events
- Support keyboard interaction
- Provide visual feedback on interaction
- Navigate when used as link variant

**Does NOT:**
- Contain business logic
- Make API calls directly
- Manage form state
- Control layout of parent components
- Store application state

---

## Props Interface

### Required Props

```typescript
type ButtonProps = {
  children: React.ReactNode
  // Button label text or content
}
```

### Optional Props

```typescript
type ButtonProps = {
  // Visual Variants
  variant?: 'primary' | 'secondary' | 'tertiary' | 'danger' | 'ghost' | 'link'
  // Default: 'primary'

  // Sizes
  size?: 'xs' | 'sm' | 'md' | 'lg' | 'xl'
  // Default: 'md'

  // States
  disabled?: boolean
  // Default: false

  loading?: boolean
  // Default: false

  // Icon Support
  leftIcon?: React.ReactNode
  // Icon positioned before label

  rightIcon?: React.ReactNode
  // Icon positioned after label

  iconOnly?: boolean
  // If true, only renders icon (requires aria-label)

  // Behavior
  type?: 'button' | 'submit' | 'reset'
  // Default: 'button'

  fullWidth?: boolean
  // Default: false
  // Expands button to 100% of container width

  // Events
  onClick?: (event: React.MouseEvent<HTMLButtonElement>) => void
  // Click handler

  onFocus?: (event: React.FocusEvent<HTMLButtonElement>) => void
  onBlur?: (event: React.FocusEvent<HTMLButtonElement>) => void

  // Link Behavior (when used as navigation)
  href?: string
  // If provided, renders as link-like button or actual link

  target?: '_blank' | '_self' | '_parent' | '_top'
  // Link target (only used with href)

  // Accessibility
  ariaLabel?: string
  // Required when iconOnly is true

  ariaDescribedBy?: string
  ariaExpanded?: boolean
  ariaPressed?: boolean

  // Styling
  className?: string
  // Additional CSS classes

  // Testing
  testId?: string
  // data-testid attribute
}
```

---

## Variants

### Visual Variants

**1. Primary (Default)**
- Use case: Main call-to-action
- Visual: Filled background, high contrast
- Color: Brand primary color
- Examples: "Sign Up", "Save Changes", "Submit"

**2. Secondary**
- Use case: Secondary actions
- Visual: Outlined or subtle fill
- Color: Neutral or secondary color
- Examples: "Cancel", "Go Back", "Learn More"

**3. Tertiary**
- Use case: Low-priority actions
- Visual: Text-only, minimal styling
- Color: Muted
- Examples: "Skip", "Not Now"

**4. Danger**
- Use case: Destructive actions
- Visual: Red/warning color scheme
- Examples: "Delete", "Remove", "Logout"

**5. Ghost**
- Use case: Minimal UI, on colored backgrounds
- Visual: Transparent with border/text only
- Examples: Buttons on hero images, modals

**6. Link**
- Use case: Inline navigation
- Visual: Styled like hyperlink
- Examples: "View Details", "Read More"

### Size Variants

**1. Extra Small (xs)**
- Use case: Compact UIs, dense tables
- Height: ~24px
- Padding: 4px 8px
- Font size: 12px

**2. Small (sm)**
- Use case: Secondary compact actions
- Height: ~32px
- Padding: 6px 12px
- Font size: 14px

**3. Medium (md) - Default**
- Use case: Standard actions
- Height: ~40px
- Padding: 8px 16px
- Font size: 14px

**4. Large (lg)**
- Use case: Primary CTAs, mobile
- Height: ~48px
- Padding: 12px 24px
- Font size: 16px

**5. Extra Large (xl)**
- Use case: Hero CTAs, landing pages
- Height: ~56px
- Padding: 16px 32px
- Font size: 18px

---

## Internal State

### Component State

```typescript
type ButtonState = {
  isFocused: boolean
  isHovered: boolean
  isPressed: boolean
}
```

**State Transitions:**
- `isFocused`: Managed by focus/blur events
- `isHovered`: Managed by mouse enter/leave
- `isPressed`: Managed by mouse down/up

**Note:** These states are typically managed by CSS pseudo-classes (`:focus`, `:hover`, `:active`) and don't require explicit React state unless custom behavior needed.

---

## Visual States

### 1. Default State
- Normal appearance based on variant
- Full opacity
- Cursor: pointer

### 2. Hover State
- Slightly darker/lighter background
- Cursor: pointer
- Smooth transition animation (150ms)

### 3. Focus State
- Visible focus ring (2-3px outline)
- High contrast color
- Offset from button edge
- Keyboard navigation indicator

### 4. Active/Pressed State
- Darker background
- Slight scale down (transform: scale(0.98))
- Provides tactile feedback

### 5. Disabled State
- Reduced opacity (40-50%)
- Cursor: not-allowed
- No hover/active effects
- Non-interactive

### 6. Loading State
- Spinner icon displayed
- Text may be hidden or shown with spinner
- Disabled interaction
- Cursor: not-allowed or default
- Spinner positioned based on icon position

---

## Behavior Specifications

### Click Handling

**Default Behavior:**
1. User clicks or presses Enter/Space
2. If disabled or loading: No action
3. If valid: Execute onClick handler
4. Prevent default if needed
5. Stop propagation if needed (configurable)

**Loading State:**
- Clicking while loading: No effect
- Show spinner (left, right, or replacing content)
- Disable button automatically
- onClick not called

**Disabled State:**
- No onClick execution
- No visual feedback
- Focus not allowed (tabindex=-1)

### Keyboard Interaction

**Supported Keys:**
- `Enter`: Activate button
- `Space`: Activate button
- `Tab`: Focus button (if not disabled)
- `Shift + Tab`: Focus previous element

**Focus Behavior:**
- Receives focus via Tab navigation
- Shows visible focus indicator
- Excluded from tab order when disabled

### Link Behavior

**When href provided:**
- Option A: Render as `<a>` styled as button
- Option B: Render as `<button>` with navigation onClick
- Supports target attribute
- External links: Add `rel="noopener noreferrer"`

---

## Accessibility Requirements

### WCAG 2.1 AA Compliance

**Keyboard Accessibility:**
- Fully keyboard operable
- Visible focus indicator (2px outline minimum)
- Focus indicator contrast ratio: 3:1
- Tab order respects document flow

**Screen Reader Support:**
- Button role announced (native `<button>` or `role="button"`)
- Label text announced
- State announced (disabled, pressed, expanded if applicable)
- Loading state announced via `aria-live` region

**Required ARIA Attributes:**
- `aria-label`: Required when iconOnly is true
- `aria-describedby`: Links to description if needed
- `aria-pressed`: For toggle buttons
- `aria-expanded`: For buttons controlling expandable content
- `aria-disabled`: Prefer disabled attribute, but support for custom implementations
- `aria-busy`: When in loading state

**Visual Accessibility:**
- Color contrast: 4.5:1 minimum for text
- Color contrast: 3:1 minimum for borders/focus indicators
- Minimum touch target: 44x44px (mobile)
- Minimum click target: 24x24px (desktop)
- Don't rely on color alone (use icons/text for states)

**Motion & Animation:**
- Respect `prefers-reduced-motion`
- Disable transitions/animations if user prefers
- Essential motion only (no decorative animations)

---

## Icon Support

### Icon Positioning

**Left Icon:**
- Positioned before text label
- Spacing: 8px between icon and text
- Vertical alignment: center
- Examples: "← Back", "✓ Save", "⊕ Add"

**Right Icon:**
- Positioned after text label
- Spacing: 8px between text and icon
- Examples: "Next →", "Open ↗", "Download ⬇"

**Icon Only:**
- No text label visible
- Icon centered in button
- Requires aria-label for accessibility
- Examples: Close button (×), Menu (≡), Search (🔍)

### Icon Specifications

**Size:**
- xs button: 12px icon
- sm button: 14px icon
- md button: 16px icon
- lg button: 20px icon
- xl button: 24px icon

**Color:**
- Inherit from button text color
- Same color as label text
- Change on hover/active states

---

## Loading State Specifications

### Loading Indicator

**Spinner Position:**
- If leftIcon: Replace leftIcon with spinner
- If rightIcon: Replace rightIcon with spinner
- If no icons: Show spinner to left of text
- If iconOnly: Replace icon with spinner

**Label Behavior:**
- Option A: Keep label visible: "Saving..."
- Option B: Hide label, show spinner only
- Recommended: Keep label for context

**Interaction:**
- Button disabled during loading
- onClick not triggered
- Cursor: not-allowed or wait

---

## Reusability Considerations

### Framework Agnostic Design

**Core Requirements (Any Framework):**
- Single component, no dependencies
- Accepts common props (label, onClick, disabled)
- Supports all specified variants
- Meets accessibility standards

**Implementation Flexibility:**
- Can be styled with CSS, CSS Modules, Tailwind, Styled Components
- Can use any icon library (or SVG)
- Can use any spinner component
- Composable with other components

### Composition Patterns

**Button with Icon:**
```
<Button leftIcon={<Icon />}>
  Label Text
</Button>
```

**Icon-Only Button:**
```
<Button iconOnly ariaLabel="Close">
  <CloseIcon />
</Button>
```

**Loading Button:**
```
<Button loading onClick={handleSave}>
  Save Changes
</Button>
```

**Full-Width Button:**
```
<Button fullWidth>
  Submit
</Button>
```

**Link Button:**
```
<Button href="/page" target="_blank">
  Open Page
</Button>
```

---

## Usage Examples (Conceptual)

### Primary CTA
```
<Button variant="primary" size="lg">
  Get Started
</Button>
```

### Secondary Action
```
<Button variant="secondary" onClick={handleCancel}>
  Cancel
</Button>
```

### Danger Action
```
<Button variant="danger" onClick={handleDelete}>
  Delete Account
</Button>
```

### Loading State
```
<Button loading variant="primary">
  Saving...
</Button>
```

### Icon Button
```
<Button
  leftIcon={<CheckIcon />}
  variant="primary"
>
  Confirm
</Button>
```

### Icon-Only
```
<Button
  iconOnly
  ariaLabel="Close dialog"
  variant="ghost"
>
  <CloseIcon />
</Button>
```

---

## Styling Specifications

### No Layout Assumptions

**Component Controls:**
- Own padding
- Own border
- Own background
- Own text styles
- Own icon spacing

**Component Does NOT Control:**
- Margin (parent responsibility)
- Position (parent responsibility)
- Width (except fullWidth prop)
- Float/flex properties (parent responsibility)

### Responsive Considerations

**Mobile:**
- Minimum touch target: 44x44px
- Larger default size recommended (lg)
- Full-width option common

**Desktop:**
- Standard sizes (md default)
- Hover states visible
- Focus states for keyboard users

---

## Performance Considerations

### Optimization

**Rendering:**
- Pure component (no unnecessary re-renders)
- Memoize if used in lists
- Minimal DOM nodes

**Bundle Size:**
- Small component (<2KB)
- Tree-shakeable variants
- Lazy load icons if large library

**Interactions:**
- Debounce rapid clicks (prevent double-submit)
- Throttle animation updates if needed

---

## Testing Considerations

### Test Cases

**Rendering:**
- Renders with children
- Renders all variants correctly
- Renders all sizes correctly
- Renders with icons (left, right, only)

**Interaction:**
- onClick fires when clicked
- onClick fires on Enter key
- onClick fires on Space key
- onClick NOT fired when disabled
- onClick NOT fired when loading

**States:**
- Disabled state prevents interaction
- Loading state shows spinner
- Loading state disables button
- Focus state shows focus ring

**Accessibility:**
- Has button role
- Has accessible label
- Disabled button has aria-disabled
- Loading button has aria-busy
- Icon-only button has aria-label

**Link Behavior:**
- Renders link when href provided
- Target attribute applied
- External links have rel attribute

---

## Browser Compatibility

**Supported Browsers:**
- Chrome/Edge (last 2 versions)
- Firefox (last 2 versions)
- Safari (last 2 versions)
- Mobile Safari (iOS 14+)
- Chrome Mobile (Android 10+)

**Progressive Enhancement:**
- Basic button works without JavaScript
- Form submission works natively
- Graceful degradation for animations

---

## Dependencies

**Required:**
- None (pure component)

**Optional:**
- Icon library (user choice)
- Spinner component (user choice)
- CSS framework (user choice)

---

## Acceptance Criteria

- [ ] Component renders as native `<button>` element (or semantic equivalent)
- [ ] All 6 visual variants render correctly
- [ ] All 5 size variants render correctly
- [ ] onClick handler executes on click
- [ ] onClick handler executes on Enter key
- [ ] onClick handler executes on Space key
- [ ] Disabled state prevents onClick execution
- [ ] Loading state shows spinner
- [ ] Loading state prevents onClick execution
- [ ] Left icon displays before label
- [ ] Right icon displays after label
- [ ] Icon-only mode hides label text
- [ ] Icon-only mode requires aria-label
- [ ] Full-width prop makes button 100% width
- [ ] Focus state shows visible outline
- [ ] Focus outline meets 3:1 contrast ratio
- [ ] Hover state provides visual feedback
- [ ] Active/pressed state provides tactile feedback
- [ ] Disabled button has reduced opacity
- [ ] Disabled button has not-allowed cursor
- [ ] Button text contrast meets 4.5:1 ratio
- [ ] Minimum touch target 44x44px on mobile
- [ ] href prop enables link behavior
- [ ] External links have rel="noopener noreferrer"
- [ ] Screen reader announces button role
- [ ] Screen reader announces disabled state
- [ ] Screen reader announces loading state
- [ ] Component respects prefers-reduced-motion
- [ ] Component has no margin (parent controlled)
- [ ] Component works without JavaScript (progressive enhancement)

---

**Related Specifications:**
- Component: IconButton (specialized variant)
- Component: ButtonGroup (composition)
- Pattern: Form Actions
- Pattern: CTA Sections
