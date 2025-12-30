# Badge Component Specification

**Component Type:** UI / Indicator
**Single Responsibility:** Display small status indicators, counts, or labels
**Version:** 1.0.0

---

## Purpose

Provide a compact visual indicator for status, counts, categories, or labels. Support various colors, sizes, and styles to convey different types of information clearly and consistently across the application.

---

## Component Responsibility

**Single Responsibility:** Display a small, styled label or indicator

**Does:**
- Render small text label or number
- Apply color coding based on variant
- Support different sizes
- Support optional icon
- Support dot indicator (without text)
- Display notification counts

**Does NOT:**
- Handle click events (use Button instead)
- Contain complex content
- Manage application state
- Control parent layout

---

## Props Interface

### Required Props

```typescript
type BadgeProps = {
  children?: React.ReactNode
  // Badge content (text or number)
  // Optional if dot variant used
}
```

### Optional Props

```typescript
type BadgeProps = {
  // Visual Variants
  variant?: 'default' | 'primary' | 'success' | 'warning' | 'error' | 'info'
  // Default: 'default'

  // Style Variants
  style?: 'filled' | 'outlined' | 'subtle'
  // Default: 'filled'

  // Sizes
  size?: 'sm' | 'md' | 'lg'
  // Default: 'md'

  // Display Modes
  dot?: boolean
  // Default: false
  // Shows only a dot (no text)

  pill?: boolean
  // Default: false
  // Fully rounded ends (like a pill)

  // Icon
  icon?: React.ReactNode
  // Optional icon before text

  // Max Count (for numeric badges)
  maxCount?: number
  // Default: 99
  // Show "99+" if children exceeds this

  // Styling
  className?: string
  // Additional CSS classes

  // Accessibility
  ariaLabel?: string
  // Accessible label (useful for icon-only or dot badges)

  // Testing
  testId?: string
  // data-testid attribute
}
```

---

## Variants

### Color Variants

**1. Default (Gray)**
- Use case: Neutral status, categories
- Color: Gray
- Examples: "Draft", "Inactive", tags

**2. Primary (Brand Color)**
- Use case: Primary status, emphasis
- Color: Blue (brand primary)
- Examples: "New", "Featured"

**3. Success (Green)**
- Use case: Success states, positive status
- Color: Green
- Examples: "Active", "Completed", "Online"

**4. Warning (Yellow/Orange)**
- Use case: Warnings, pending states
- Color: Yellow/Orange
- Examples: "Pending", "Expiring Soon"

**5. Error (Red)**
- Use case: Errors, critical states, urgent
- Color: Red
- Examples: "Failed", "Expired", "Offline"

**6. Info (Blue)**
- Use case: Informational, neutral emphasis
- Color: Light Blue
- Examples: "Beta", "Info", notification counts

---

## Style Variants

### Visual Styles

**1. Filled (Default)**
- Background: Solid color
- Text: White or contrasting color
- Border: None
- Use case: High emphasis

**2. Outlined**
- Background: Transparent
- Text: Variant color
- Border: 1px solid variant color
- Use case: Medium emphasis

**3. Subtle**
- Background: Light tint of variant color (10-20% opacity)
- Text: Darker variant color
- Border: None
- Use case: Low emphasis, tags

---

## Size Variants

### Badge Sizes

**Small (sm):**
- Height: 16px
- Padding: 2px 6px
- Font size: 10px
- Use case: Compact UIs, dense tables

**Medium (md) - Default:**
- Height: 20px
- Padding: 3px 8px
- Font size: 12px
- Use case: Standard badges

**Large (lg):**
- Height: 24px
- Padding: 4px 10px
- Font size: 14px
- Use case: Emphasis, larger interfaces

---

## Internal State

### Component State

**No Internal State Required**
- Pure presentational component
- No interactions
- Static display

---

## Visual Structure

### Standard Badge

```
┌──────────────┐
│ [Icon] Text  │
└──────────────┘
```

### Dot Badge

```
● (colored dot only)
```

### Pill Badge

```
╭──────────────╮
│ Text Content │
╰──────────────╯
```

### Numeric Badge with Max

```
┌─────┐
│ 99+ │
└─────┘
```

---

## Behavior Specifications

### Count Display

**Numeric Content:**
- If children is number and <= maxCount: Display exact number
- If children is number and > maxCount: Display "maxCount+"
- Default maxCount: 99
- Example: 100 items shows "99+"

**Non-Numeric Content:**
- Display text as-is
- Truncate if too long (parent defines max-width)

### Dot Mode

**When dot=true:**
- Hide text content
- Show only colored dot
- Size: 8px (sm), 10px (md), 12px (lg)
- Circular shape
- Use with ariaLabel for accessibility

---

## Accessibility Requirements

### WCAG 2.1 AA Compliance

**Semantic HTML:**
- Render as `<span>` by default
- No interactive role (not a button)

**Screen Reader Support:**
- Text content announced
- aria-label for dot badges (required)
- aria-label for icon-only badges
- Decorative badges: aria-hidden="true" (if purely visual)

**Visual Accessibility:**
- Text contrast: 4.5:1 minimum
- Filled variant: Ensure text visible on background
- Outlined variant: Border and text meet contrast requirements
- Don't rely on color alone (use text labels or icons)

**Non-Interactive:**
- No keyboard navigation (not focusable)
- Not in tab order
- Purely informational

---

## Styling Specifications

### No Layout Assumptions

**Component Controls:**
- Own padding
- Own height
- Own border (if outlined)
- Own background color
- Own border radius
- Own text styles

**Component Does NOT Control:**
- Margin (parent controls spacing)
- Position (parent controls placement)
- Width (fits content, parent can override)

### Color Specifications (Example)

**Filled - Success:**
- Background: #10B981 (green)
- Text: #FFFFFF (white)
- Border: None

**Outlined - Success:**
- Background: transparent
- Text: #10B981 (green)
- Border: 1px solid #10B981

**Subtle - Success:**
- Background: #D1FAE5 (light green, 20% opacity)
- Text: #047857 (dark green)
- Border: None

**Note:** Actual colors depend on design system; must meet WCAG AA contrast.

---

## Reusability Considerations

### Framework Agnostic

**Core Requirements:**
- Inline component
- No dependencies
- Supports all variants
- Minimal bundle size

**Implementation Flexibility:**
- CSS/Tailwind/Styled Components
- Any icon library
- Composable with other components

### Composition Patterns

**Simple Badge:**
```
<Badge>New</Badge>
```

**Variant Badge:**
```
<Badge variant="success">Active</Badge>
```

**Numeric Badge:**
```
<Badge variant="error">{unreadCount}</Badge>
```

**Badge with Max Count:**
```
<Badge variant="info" maxCount={99}>
  {notificationCount}
</Badge>
```

**Dot Badge:**
```
<Badge variant="success" dot ariaLabel="Online" />
```

**Badge with Icon:**
```
<Badge icon={<CheckIcon />} variant="success">
  Verified
</Badge>
```

**Pill Badge:**
```
<Badge pill variant="primary">
  Featured
</Badge>
```

---

## Usage Examples (Conceptual)

### Status Badge
```
<Badge variant="success">Active</Badge>
<Badge variant="error">Offline</Badge>
<Badge variant="warning">Pending</Badge>
```

### Notification Count
```
<Badge variant="error" style="filled">5</Badge>
// or with max:
<Badge variant="error" maxCount={99}>150</Badge>
// Displays: "99+"
```

### Category Tags
```
<Badge variant="default" style="subtle">JavaScript</Badge>
<Badge variant="default" style="subtle">React</Badge>
<Badge variant="default" style="subtle">TypeScript</Badge>
```

### With Icon
```
<Badge icon={<StarIcon />} variant="warning">
  Premium
</Badge>
```

### Online Indicator
```
<Badge variant="success" dot ariaLabel="User is online" />
```

---

## Common Use Cases

### In Navigation
- Show unread count on notifications icon
- Indicate new features or updates

### In Lists/Tables
- Status indicators (Active, Pending, Failed)
- Category tags
- Priority labels (High, Medium, Low)

### On Cards
- Featured badge on product cards
- "New" badge on articles
- Status labels on user profiles

### With Avatars
- Online/offline indicator (dot badge)
- Notification count overlay

---

## Performance Considerations

### Optimization

**Rendering:**
- Pure component (no re-renders)
- Minimal DOM nodes (single element)
- No JavaScript logic

**Bundle Size:**
- Tiny component (<1KB)
- No dependencies
- Inline styles or small CSS

---

## Testing Considerations

### Test Cases

**Rendering:**
- Renders with text content
- Renders all color variants correctly
- Renders all style variants correctly
- Renders all size variants
- Renders with icon
- Renders as dot (no text)
- Renders as pill shape

**Count Display:**
- Displays exact number if <= maxCount
- Displays "maxCount+" if > maxCount
- Default maxCount is 99

**Accessibility:**
- Text content announced to screen readers
- Dot badge has aria-label
- Contrast meets WCAG AA standards

---

## Browser Compatibility

**Supported Browsers:**
- Chrome/Edge (last 2 versions)
- Firefox (last 2 versions)
- Safari (last 2 versions)
- Mobile Safari (iOS 14+)
- Chrome Mobile (Android 10+)

**Progressive Enhancement:**
- Works without JavaScript
- CSS-only component
- No animations required

---

## Dependencies

**Required:**
- None (pure component)

**Optional:**
- Icon library (for icon variant)

---

## Acceptance Criteria

- [ ] Component renders with text content
- [ ] All 6 color variants render correctly
- [ ] Default variant uses gray color
- [ ] Primary variant uses brand color
- [ ] Success variant uses green color
- [ ] Warning variant uses yellow/orange color
- [ ] Error variant uses red color
- [ ] Info variant uses blue color
- [ ] Filled style has solid background
- [ ] Outlined style has border and transparent background
- [ ] Subtle style has light background tint
- [ ] All 3 size variants render correctly
- [ ] Small badge has correct dimensions
- [ ] Medium badge has correct dimensions
- [ ] Large badge has correct dimensions
- [ ] Icon displays before text when provided
- [ ] Dot mode shows only colored circle
- [ ] Dot mode hides text content
- [ ] Pill mode has fully rounded borders
- [ ] Numeric content displays exact number if <= maxCount
- [ ] Numeric content displays "maxCount+" if > maxCount
- [ ] Default maxCount is 99
- [ ] Text contrast meets 4.5:1 ratio (filled variant)
- [ ] Border contrast meets 3:1 ratio (outlined variant)
- [ ] Dot badge has aria-label when provided
- [ ] Component has no margin (parent controlled)
- [ ] Badge width fits content by default

---

**Related Specifications:**
- Component: Button (badges can be placed on buttons)
- Component: Avatar (badges can overlay avatars)
- Pattern: Status Indicators
- Pattern: Notification Systems
