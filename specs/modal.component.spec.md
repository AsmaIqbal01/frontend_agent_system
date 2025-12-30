# Modal Component Specification

**Component Type:** UI / Overlay
**Single Responsibility:** Display content in an overlay dialog that requires user attention
**Version:** 1.0.0

---

## Purpose

Provide a modal dialog overlay for displaying important content, confirmations, forms, or detailed information that requires user focus and action before returning to the main interface. Ensure accessibility and proper focus management.

---

## Component Responsibility

**Single Responsibility:** Display overlay content and manage focus/interaction

**Does:**
- Render overlay backdrop
- Display centered dialog content
- Trap focus within modal
- Handle close actions (X button, backdrop click, Escape key)
- Prevent body scroll when open
- Provide header, body, and footer sections
- Announce to screen readers
- Manage open/close state transitions

**Does NOT:**
- Make API calls
- Manage application state
- Contain complex business logic
- Control page layout outside modal
- Define specific form logic

---

## Props Interface

### Required Props

```typescript
type ModalProps = {
  isOpen: boolean
  // Controls modal visibility (parent controls state)

  onClose: () => void
  // Callback when modal should close

  children: React.ReactNode
  // Modal body content
}
```

### Optional Props

```typescript
type ModalProps = {
  // Header
  title?: string
  // Modal title/heading

  showCloseButton?: boolean
  // Default: true
  // Shows X button in header

  // Footer
  footer?: React.ReactNode
  // Footer content (typically action buttons)

  // Size
  size?: 'xs' | 'sm' | 'md' | 'lg' | 'xl' | 'full'
  // Default: 'md'
  // Controls modal width

  // Behavior
  closeOnBackdropClick?: boolean
  // Default: true
  // Close when clicking outside modal

  closeOnEscape?: boolean
  // Default: true
  // Close when pressing Escape key

  preventBodyScroll?: boolean
  // Default: true
  // Prevents scrolling page behind modal

  // Focus Management
  initialFocus?: React.RefObject<HTMLElement>
  // Element to focus when modal opens

  returnFocus?: boolean
  // Default: true
  // Return focus to trigger element on close

  // Visual
  centered?: boolean
  // Default: true
  // Vertically centers modal

  scrollBehavior?: 'inside' | 'outside'
  // Default: 'inside'
  // Where scrollbar appears if content overflows

  // Styling
  className?: string
  // Additional CSS classes for modal content

  backdropClassName?: string
  // Additional CSS classes for backdrop

  // Accessibility
  ariaLabelledBy?: string
  // ID of element labeling modal

  ariaDescribedBy?: string
  // ID of element describing modal

  role?: 'dialog' | 'alertdialog'
  // Default: 'dialog'

  // Animation
  animationDuration?: number
  // Default: 200 (ms)

  // Testing
  testId?: string
  // data-testid attribute
}
```

---

## Internal State

### Component State

```typescript
type ModalState = {
  isAnimating: boolean
  // Tracks enter/exit animation
  // Default: false

  triggerElement: HTMLElement | null
  // Element that opened modal (for focus return)
}
```

**State Management:**
- `isAnimating`: True during mount/unmount animations
- `triggerElement`: Stored on mount, used on unmount

---

## Size Variants

### Modal Width Sizes

**Extra Small (xs):**
- Max width: 320px
- Use case: Simple confirmations, alerts

**Small (sm):**
- Max width: 400px
- Use case: Short forms, confirmations

**Medium (md) - Default:**
- Max width: 560px
- Use case: Standard forms, content

**Large (lg):**
- Max width: 720px
- Use case: Complex forms, detailed content

**Extra Large (xl):**
- Max width: 960px
- Use case: Multi-section content, wide forms

**Full:**
- Width: 90vw or 100vw
- Height: 90vh or 100vh
- Use case: Immersive content, image viewers

---

## Visual Structure

### Modal Layout

```
┌─────────────────────────────────────────┐
│ BACKDROP (semi-transparent overlay)    │
│                                         │
│   ┌─────────────────────────────────┐ │
│   │ Header                     [X]  │ │
│   ├─────────────────────────────────┤ │
│   │                                 │ │
│   │ Body (scrollable if needed)     │ │
│   │                                 │ │
│   ├─────────────────────────────────┤ │
│   │ Footer (actions)                │ │
│   └─────────────────────────────────┘ │
│                                         │
└─────────────────────────────────────────┘
```

### Layout Sections

**1. Backdrop**
- Full viewport overlay
- Semi-transparent (rgba(0,0,0,0.5))
- Click closes modal (if enabled)
- Prevents interaction with page behind

**2. Modal Container**
- Centered (horizontal and vertical)
- Max-width based on size variant
- White background
- Border radius
- Box shadow

**3. Header (Optional)**
- Title (heading)
- Close button (X icon, right-aligned)
- Border bottom (optional)

**4. Body (Required)**
- Main content area (children)
- Scrollable if content exceeds height
- Padding around content

**5. Footer (Optional)**
- Action buttons
- Right-aligned or space-between
- Border top (optional)

---

## Visual States

### Closed State
- Not rendered in DOM (or display: none)
- Backdrop invisible
- Modal invisible

### Opening State
- Backdrop fades in (opacity: 0 → 0.5)
- Modal slides up and fades in
- Duration: 200ms (configurable)

### Open State
- Backdrop visible (opacity: 0.5)
- Modal fully visible
- Focus trapped inside modal
- Body scroll prevented

### Closing State
- Backdrop fades out
- Modal slides down and fades out
- Duration: 200ms
- Focus returned to trigger element

---

## Behavior Specifications

### Open Behavior

**On isOpen Changes to True:**
1. Store trigger element (document.activeElement)
2. Render modal in DOM
3. Disable body scroll (add CSS overflow: hidden)
4. Play open animation
5. Trap focus in modal
6. Move focus to initialFocus or first focusable element
7. Listen for Escape key
8. Announce to screen readers

### Close Behavior

**Trigger Close:**
- Click close button (X)
- Click backdrop (if enabled)
- Press Escape key (if enabled)
- Parent calls onClose (programmatic)

**On Close Sequence:**
1. Call onClose callback
2. Play close animation
3. After animation: Restore body scroll
4. Return focus to trigger element (if enabled)
5. Remove event listeners
6. Unmount modal from DOM

### Focus Management

**Focus Trap:**
- Focus cannot leave modal via Tab
- Tab from last element: Loop to first
- Shift+Tab from first: Loop to last
- Prevent focus on elements behind modal

**Initial Focus:**
- Custom element (initialFocus ref)
- Or first focusable element (button, input)
- Or modal container itself

**Return Focus:**
- Store element that opened modal
- On close: Restore focus to stored element

### Scroll Behavior

**Inside (Default):**
- Modal content scrolls
- Backdrop and header/footer fixed
- Body content area scrollable

**Outside:**
- Entire modal scrolls
- Backdrop fixed
- Modal scrolls as unit

**Body Scroll:**
- Page scroll prevented when modal open
- Restore scroll on modal close
- Maintain scroll position

---

## Accessibility Requirements

### WCAG 2.1 AA Compliance

**Semantic HTML:**
- role="dialog" or role="alertdialog"
- aria-modal="true"
- aria-labelledby pointing to title
- aria-describedby pointing to description (optional)

**Focus Management:**
- Focus moved to modal on open
- Focus trapped within modal
- Focus returns to trigger on close
- Tab order logical (header → body → footer)

**Keyboard Accessibility:**
- Escape key closes modal (if enabled)
- Tab navigates within modal
- Shift+Tab navigates backward
- Enter/Space activate buttons
- Close button keyboard accessible

**Screen Reader Support:**
- Modal announced on open
- Title announced as dialog name
- role="dialog" or "alertdialog" announced
- Close action announced (when button focused)
- Content accessible to screen readers

**ARIA Attributes:**
- `role="dialog"` or `role="alertdialog"`
- `aria-modal="true"`
- `aria-labelledby`: ID of title
- `aria-describedby`: ID of description (optional)
- Close button: `aria-label="Close dialog"`

**Visual Accessibility:**
- Text contrast: 4.5:1 minimum
- Focus indicators visible (2px outline)
- Focus trap prevents background interaction
- Backdrop provides clear visual separation

---

## Animation Specifications

### Enter Animation

**Backdrop:**
- Opacity: 0 → 0.5
- Duration: 200ms
- Easing: ease-out

**Modal:**
- Opacity: 0 → 1
- Transform: translateY(20px) → translateY(0)
- Duration: 200ms
- Easing: ease-out

### Exit Animation

**Backdrop:**
- Opacity: 0.5 → 0
- Duration: 200ms
- Easing: ease-in

**Modal:**
- Opacity: 1 → 0
- Transform: translateY(0) → translateY(20px)
- Duration: 200ms
- Easing: ease-in

**Motion Accessibility:**
- Respect prefers-reduced-motion
- Skip animations if preference set
- Instant show/hide if reduced motion

---

## Reusability Considerations

### Framework Agnostic

**Core Requirements:**
- Overlay dialog component
- Focus trap functionality
- Close on backdrop/Escape
- Accessibility compliant
- Animation support

**Implementation Flexibility:**
- Portal/teleport for rendering
- CSS/Tailwind/Styled Components
- Any animation library or CSS transitions
- Composable with Button, Form components

### Composition Patterns

**Simple Modal:**
```
<Modal isOpen={isOpen} onClose={handleClose} title="Modal Title">
  <p>Modal content here</p>
</Modal>
```

**Modal with Footer:**
```
<Modal
  isOpen={isOpen}
  onClose={handleClose}
  title="Confirm Action"
  footer={
    <>
      <Button variant="secondary" onClick={handleClose}>Cancel</Button>
      <Button onClick={handleConfirm}>Confirm</Button>
    </>
  }
>
  Are you sure you want to proceed?
</Modal>
```

**Large Modal:**
```
<Modal
  isOpen={isOpen}
  onClose={handleClose}
  size="lg"
  title="Detailed View"
>
  <LargeContent />
</Modal>
```

**Alert Dialog:**
```
<Modal
  isOpen={isOpen}
  onClose={handleClose}
  role="alertdialog"
  closeOnBackdropClick={false}
  title="Warning"
>
  This action cannot be undone.
</Modal>
```

---

## Usage Examples (Conceptual)

### Confirmation Modal
```
<Modal
  isOpen={showConfirm}
  onClose={() => setShowConfirm(false)}
  title="Delete Item"
  size="sm"
  footer={
    <>
      <Button variant="secondary" onClick={handleCancel}>
        Cancel
      </Button>
      <Button variant="danger" onClick={handleDelete}>
        Delete
      </Button>
    </>
  }
>
  Are you sure you want to delete this item? This action cannot be undone.
</Modal>
```

### Form Modal
```
<Modal
  isOpen={showForm}
  onClose={handleCloseForm}
  title="Add New User"
  size="md"
>
  <UserForm onSubmit={handleSubmit} onCancel={handleCloseForm} />
</Modal>
```

### Full-Screen Modal
```
<Modal
  isOpen={showViewer}
  onClose={handleCloseViewer}
  size="full"
  showCloseButton={true}
>
  <ImageGallery images={images} />
</Modal>
```

---

## Performance Considerations

### Optimization

**Rendering:**
- Lazy render (only when isOpen)
- Portal rendering (outside main tree)
- Memoize modal content
- Unmount on close (clean up DOM)

**Focus Management:**
- Use focus-trap library or implement efficiently
- Query focusable elements once
- Clean up listeners on unmount

**Animations:**
- Use CSS transitions (GPU accelerated)
- Avoid JavaScript animations
- Short durations (150-250ms)

**Body Scroll:**
- Add/remove overflow CSS class
- Restore scroll position accurately
- Account for scrollbar width shift

**Bundle Size:**
- Component: ~5-8KB
- Focus trap logic included
- No heavy dependencies

---

## Testing Considerations

### Test Cases

**Rendering:**
- Renders when isOpen is true
- Does not render when isOpen is false
- Renders title when provided
- Renders footer when provided
- Renders close button by default
- Hides close button when showCloseButton=false

**Interaction:**
- onClose fires when close button clicked
- onClose fires when backdrop clicked (if enabled)
- onClose fires when Escape pressed (if enabled)
- onClose does NOT fire when backdrop clicked (if disabled)
- Modal content clickable

**Focus Management:**
- Focus moves to modal on open
- Focus trapped within modal
- Tab cycles through focusable elements
- Focus returns to trigger on close
- Initial focus goes to specified element (if provided)

**Accessibility:**
- Has role="dialog"
- Has aria-modal="true"
- Has aria-labelledby pointing to title
- Close button has aria-label
- Keyboard navigation works

**Body Scroll:**
- Body scroll prevented when modal open
- Body scroll restored when modal closes

**Animations:**
- Enter animation plays on open
- Exit animation plays on close
- Animations skip if prefers-reduced-motion

---

## Browser Compatibility

**Supported Browsers:**
- Chrome/Edge (last 2 versions)
- Firefox (last 2 versions)
- Safari (last 2 versions)
- Mobile Safari (iOS 14+)
- Chrome Mobile (Android 10+)

**Progressive Enhancement:**
- Basic modal works without animations
- Focus trap essential (no fallback)
- Portal rendering required

---

## Dependencies

**Required:**
- Portal/Teleport functionality (React Portal, etc.)
- Focus trap implementation

**Optional:**
- Animation library (Framer Motion, etc.) or CSS transitions
- Icon library (for close button)

---

## Acceptance Criteria

- [ ] Modal renders when isOpen is true
- [ ] Modal does not render when isOpen is false
- [ ] Backdrop displays behind modal
- [ ] Backdrop is semi-transparent
- [ ] Modal is centered vertically and horizontally
- [ ] All size variants render with correct widths
- [ ] Title displays when provided
- [ ] Footer displays when provided
- [ ] Close button displays by default
- [ ] Close button hidden when showCloseButton=false
- [ ] onClose fires when close button clicked
- [ ] onClose fires when backdrop clicked (if enabled)
- [ ] onClose fires when Escape key pressed (if enabled)
- [ ] Backdrop click does not close if closeOnBackdropClick=false
- [ ] Escape key does not close if closeOnEscape=false
- [ ] Focus moves to modal on open
- [ ] Focus trapped within modal (Tab cycles)
- [ ] Focus returns to trigger element on close
- [ ] Initial focus goes to specified element (if provided)
- [ ] Body scroll prevented when modal open
- [ ] Body scroll restored when modal closes
- [ ] Enter animation plays on open
- [ ] Exit animation plays on close
- [ ] Animations respect prefers-reduced-motion
- [ ] has role="dialog"
- [ ] has aria-modal="true"
- [ ] has aria-labelledby pointing to title
- [ ] Close button has aria-label="Close dialog"
- [ ] Text contrast meets 4.5:1 ratio
- [ ] Focus indicator visible on all focusable elements
- [ ] Modal scrolls correctly (inside or outside based on scrollBehavior)
- [ ] Modal works on mobile devices
- [ ] Modal accessible via keyboard only

---

**Related Specifications:**
- Component: Button (for close and actions)
- Component: Alert (for confirmations)
- Pattern: Confirmation Dialogs
- Pattern: Form Modals
