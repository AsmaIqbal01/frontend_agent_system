# Alert Component Specification

**Component Type:** UI / Feedback
**Single Responsibility:** Display contextual feedback messages to users
**Version:** 1.0.0

---

## Purpose

Provide users with important information, notifications, warnings, errors, or success messages in a visually distinct and accessible manner. Support dismissible and persistent alerts with appropriate visual styling based on message severity.

---

## Component Responsibility

**Single Responsibility:** Communicate status, feedback, or important information to users

**Does:**
- Display message content
- Show appropriate icon based on severity
- Provide visual distinction by message type
- Support dismissible (closeable) alerts
- Handle close action
- Announce to screen readers
- Display optional title and description
- Support action buttons

**Does NOT:**
- Make API calls
- Manage application state
- Control page layout
- Contain complex business logic
- Auto-dismiss (timer-based) - handle by parent

---

## Props Interface

### Required Props

```typescript
type AlertProps = {
  children: React.ReactNode
  // Alert message content
}
```

### Optional Props

```typescript
type AlertProps = {
  // Severity/Type
  variant?: 'info' | 'success' | 'warning' | 'error'
  // Default: 'info'

  // Content
  title?: string
  // Optional title/heading for the alert

  description?: string
  // If children used for title, description for body text

  // Visual
  icon?: React.ReactNode | boolean
  // Custom icon or true to show default icon for variant
  // Default: true (show default icon)

  // Behavior
  dismissible?: boolean
  // Default: false
  // Shows close button if true

  onClose?: () => void
  // Callback when alert is dismissed
  // Required if dismissible is true

  // Actions
  actions?: React.ReactNode
  // Optional action buttons (e.g., "Retry", "Undo")

  // Layout
  fullWidth?: boolean
  // Default: false
  // Expands to container width

  // Styling
  className?: string
  // Additional CSS classes

  // Accessibility
  role?: 'alert' | 'status'
  // Default: 'alert' for errors/warnings, 'status' for info/success

  ariaLive?: 'polite' | 'assertive' | 'off'
  // Default: 'polite' (or 'assertive' for errors)

  ariaLabel?: string
  // Accessible label if needed

  // Testing
  testId?: string
  // data-testid attribute
}
```

---

## Variants

### Severity Variants

**1. Info (Default)**
- Use case: General information, tips, neutral updates
- Color: Blue
- Icon: Info circle (ⓘ)
- Examples: "Your session will expire in 10 minutes"

**2. Success**
- Use case: Successful operations, confirmations
- Color: Green
- Icon: Checkmark (✓)
- Examples: "Your changes have been saved", "Account created successfully"

**3. Warning**
- Use case: Important cautions, non-critical issues
- Color: Yellow/Orange
- Icon: Warning triangle (⚠)
- Examples: "Some features may not work in this browser", "File size is large"

**4. Error**
- Use case: Errors, failures, critical issues
- Color: Red
- Icon: Error circle with X (⊗)
- Examples: "Failed to save changes", "Invalid email address", "Network error"

---

## Internal State

### Component State

```typescript
type AlertState = {
  isVisible: boolean
  // Controls visibility (for dismiss animation)
  // Default: true
}
```

**State Management:**
- `isVisible`: Initially true
- On dismiss: Set to false (trigger exit animation)
- After animation: Call onClose
- Parent removes component from DOM

---

## Visual Structure

### Layout Components

```
┌────────────────────────────────────────────┐
│  [Icon]  Title                    [Close]  │
│          Description text...               │
│          [Action Button(s)]                │
└────────────────────────────────────────────┘
```

**Elements:**
1. Icon (left) - Optional, variant-specific
2. Content Area (center) - Title and/or description
3. Close Button (right) - Optional, if dismissible
4. Actions (bottom) - Optional action buttons

### Minimal Layout (Description Only)

```
┌────────────────────────────────────────────┐
│  [Icon]  Message text...          [Close]  │
└────────────────────────────────────────────┘
```

---

## Visual States

### Default State
- Full opacity
- Visible border or background
- Icon displayed (if enabled)

### Hover State (for dismissible)
- Close button shows hover effect
- Background slightly darker on close button hover

### Focus State
- Close button: Visible focus ring
- Action buttons: Visible focus ring

### Dismissed State
- Fade out animation (200-300ms)
- Slide up or collapse (optional)
- Remove from DOM after animation

---

## Behavior Specifications

### Display Behavior

**On Mount:**
- Render immediately
- Announce to screen readers via aria-live
- Fade in animation (optional, 150ms)

**Persistent Alert:**
- Remains visible until manually dismissed
- No auto-dismiss (parent can implement timer)

**Dismissible Alert:**
- Shows close button (X icon)
- Click close: Fade out and remove
- Keyboard: Focus close button, press Enter/Space

### Close Behavior

**User Actions:**
1. Click close button
2. Press Escape key (optional)

**Sequence:**
1. User triggers close
2. Begin exit animation (fade out)
3. Set isVisible to false
4. After animation: Call onClose callback
5. Parent removes component

---

## Accessibility Requirements

### WCAG 2.1 AA Compliance

**Semantic HTML:**
- Use appropriate ARIA role
- `role="alert"`: Errors, warnings (assertive announcements)
- `role="status"`: Info, success (polite announcements)

**Screen Reader Support:**
- Announced on mount via aria-live
- `aria-live="polite"`: Info, success (default)
- `aria-live="assertive"`: Error, warning (interrupts user)
- Close button has aria-label: "Close" or "Dismiss alert"
- Icon is decorative: `aria-hidden="true"`

**Keyboard Accessibility:**
- Close button keyboard focusable
- Close button operable with Enter/Space
- Action buttons keyboard accessible
- Optional: Escape key closes alert

**Visual Accessibility:**
- Color contrast: 4.5:1 minimum for text
- Background contrast: 3:1 minimum
- Don't rely on color alone (use icons and text)
- Icon reinforces meaning (not color only)
- Focus indicators visible (2px outline minimum)

**Screen Reader Announcements:**
- Title + Description announced together
- Variant context included ("Error:", "Success:", etc.)
- Close action confirmed (optional)

---

## Icon Specifications

### Default Icons by Variant

**Info:**
- Icon: Circle with lowercase 'i' (ⓘ)
- Color: Blue (matching variant)

**Success:**
- Icon: Checkmark or circle with check (✓)
- Color: Green

**Warning:**
- Icon: Triangle with exclamation (⚠)
- Color: Yellow/Orange

**Error:**
- Icon: Circle with X or exclamation (⊗ or ⚠)
- Color: Red

### Icon Customization

**Custom Icon:**
- Accepts any React node
- Size: 20-24px (matches text size)
- Color: Inherit from variant
- Position: Left side, vertically centered

**No Icon:**
- Set icon={false}
- Content starts at left edge (with padding)

---

## Action Buttons

### Specifications

**Purpose:**
- Provide contextual actions related to alert
- Examples: "Retry", "Undo", "View Details", "Learn More"

**Positioning:**
- Below description text
- Left-aligned
- Horizontal layout
- Spacing: 8px between buttons

**Button Styling:**
- Small size
- Variant: Secondary or link
- Inherit alert color scheme (optional)
- Maintain contrast requirements

**Interaction:**
- Keyboard accessible
- Click executes callback
- Does not auto-dismiss alert (parent decides)

---

## Styling Specifications

### No Layout Assumptions

**Component Controls:**
- Own padding (12px to 16px)
- Own border (1px solid or none)
- Own background color
- Own border radius (4px to 8px)
- Own text styles
- Icon and close button spacing

**Component Does NOT Control:**
- Margin (parent responsibility)
- Position (parent responsibility)
- Width (except fullWidth prop)
- Z-index (parent responsibility for stacking)

### Color Specifications

**Info (Blue):**
- Background: Light blue (e.g., #EBF8FF)
- Border: Medium blue (e.g., #3182CE)
- Text: Dark blue (e.g., #2C5282)
- Icon: Blue

**Success (Green):**
- Background: Light green (e.g., #F0FFF4)
- Border: Medium green (e.g., #38A169)
- Text: Dark green (e.g., #22543D)
- Icon: Green

**Warning (Yellow/Orange):**
- Background: Light yellow (e.g., #FFFBEB)
- Border: Medium orange (e.g., #DD6B20)
- Text: Dark orange (e.g., #7C2D12)
- Icon: Orange

**Error (Red):**
- Background: Light red (e.g., #FFF5F5)
- Border: Medium red (e.g., #E53E3E)
- Text: Dark red (e.g., #742A2A)
- Icon: Red

**Note:** Exact colors depend on design system; ensure WCAG AA contrast.

---

## Animation Specifications

### Enter Animation (Optional)

**Fade In:**
- Duration: 150ms
- Easing: ease-out
- Opacity: 0 → 1
- Transform: translateY(-8px) → translateY(0)

### Exit Animation (Dismissible)

**Fade Out:**
- Duration: 200ms
- Easing: ease-in
- Opacity: 1 → 0
- Transform: translateY(0) → translateY(-8px)
- Or: Height collapse

**Motion Accessibility:**
- Respect prefers-reduced-motion
- Skip animations if user preference set
- Use simple fade only (no complex motion)

---

## Reusability Considerations

### Framework Agnostic

**Core Requirements:**
- Single component
- No external dependencies
- Accepts variant, content, and callbacks
- Meets accessibility standards

**Implementation Flexibility:**
- CSS/Tailwind/Styled Components for styling
- Any icon library or SVG
- Composable with Button component for actions

### Composition Patterns

**Simple Alert:**
```
<Alert variant="success">
  Changes saved successfully!
</Alert>
```

**Alert with Title:**
```
<Alert variant="error" title="Error">
  Unable to save changes. Please try again.
</Alert>
```

**Dismissible Alert:**
```
<Alert
  variant="warning"
  dismissible
  onClose={handleClose}
>
  Your session will expire soon.
</Alert>
```

**Alert with Actions:**
```
<Alert
  variant="error"
  title="Network Error"
  actions={
    <Button variant="secondary" size="sm" onClick={handleRetry}>
      Retry
    </Button>
  }
>
  Failed to load data.
</Alert>
```

**Custom Icon:**
```
<Alert variant="info" icon={<CustomIcon />}>
  Custom notification message
</Alert>
```

---

## Usage Examples (Conceptual)

### Success Message
```
<Alert variant="success">
  Your password has been reset successfully!
</Alert>
```

### Error with Action
```
<Alert
  variant="error"
  title="Upload Failed"
  dismissible
  onClose={handleDismiss}
  actions={<Button onClick={handleRetry}>Try Again</Button>}
>
  The file could not be uploaded. Check your connection and try again.
</Alert>
```

### Warning
```
<Alert variant="warning">
  This action cannot be undone.
</Alert>
```

### Info with Title
```
<Alert variant="info" title="New Feature">
  We've added dark mode! Try it in settings.
</Alert>
```

---

## Performance Considerations

### Optimization

**Rendering:**
- Pure component (prevent unnecessary re-renders)
- Memoize if used in lists
- Minimal DOM nodes

**Animations:**
- Use CSS transitions (GPU accelerated)
- Avoid JavaScript animations
- Respect reduced motion preference

**Bundle Size:**
- Small component (<3KB)
- Tree-shakeable icons
- No heavy dependencies

---

## Testing Considerations

### Test Cases

**Rendering:**
- Renders with children content
- Renders all variants correctly
- Renders with title and description
- Renders with custom icon
- Renders without icon (icon=false)
- Renders dismissible close button

**Interaction:**
- onClose fires when close button clicked
- onClose fires when close button activated via keyboard
- Action buttons execute callbacks
- Does not dismiss on action button click (unless specified)

**Accessibility:**
- Has correct ARIA role (alert or status)
- Has correct aria-live value
- Close button has aria-label
- Icon has aria-hidden
- Announced to screen readers on mount

**States:**
- Dismissible shows close button
- Non-dismissible hides close button
- Exit animation plays on dismiss

---

## Browser Compatibility

**Supported Browsers:**
- Chrome/Edge (last 2 versions)
- Firefox (last 2 versions)
- Safari (last 2 versions)
- Mobile Safari (iOS 14+)
- Chrome Mobile (Android 10+)

**Progressive Enhancement:**
- Basic alert works without JavaScript
- Graceful degradation for animations
- Static display if JavaScript disabled

---

## Dependencies

**Required:**
- None (pure component)

**Optional:**
- Icon library (user choice)
- Button component (for actions)

---

## Acceptance Criteria

- [ ] Component renders with message content
- [ ] All 4 variants render with correct colors
- [ ] Info variant uses blue color scheme
- [ ] Success variant uses green color scheme
- [ ] Warning variant uses yellow/orange color scheme
- [ ] Error variant uses red color scheme
- [ ] Default icon displays for each variant
- [ ] Custom icon can be provided
- [ ] Icon can be hidden (icon=false)
- [ ] Title displays when provided
- [ ] Description displays when provided
- [ ] Dismissible alert shows close button
- [ ] Close button executes onClose callback
- [ ] Close button works with keyboard (Enter/Space)
- [ ] Action buttons render when provided
- [ ] Action buttons are keyboard accessible
- [ ] Has appropriate ARIA role (alert or status)
- [ ] Has correct aria-live value
- [ ] Close button has aria-label
- [ ] Icon has aria-hidden="true"
- [ ] Screen reader announces alert on mount
- [ ] Text color contrast meets 4.5:1 ratio
- [ ] Background/border contrast meets 3:1 ratio
- [ ] Focus indicator visible on close button
- [ ] Focus indicator meets 3:1 contrast ratio
- [ ] Exit animation plays on dismiss (if enabled)
- [ ] Respects prefers-reduced-motion
- [ ] Component has no margin (parent controlled)
- [ ] fullWidth prop expands to container width

---

**Related Specifications:**
- Component: Button (for actions)
- Component: Toast/Notification (transient alerts)
- Pattern: Error Handling
- Pattern: Form Validation Feedback
