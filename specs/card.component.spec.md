# Card Component Specification

**Component Type:** UI / Container
**Single Responsibility:** Provide a consistent container for grouping related content
**Version:** 1.0.0

---

## Purpose

Serve as a flexible, reusable container component that groups related content with consistent styling, spacing, and visual hierarchy. Support various content layouts while maintaining design consistency across the application.

---

## Component Responsibility

**Single Responsibility:** Act as a styled container for grouping related content

**Does:**
- Provide consistent padding and spacing
- Display optional header with title and actions
- Display body content area
- Display optional footer with actions
- Apply border, shadow, and background styling
- Support interactive/clickable variants
- Support image/media content

**Does NOT:**
- Define specific content structure (flexible children)
- Make API calls
- Manage application state
- Control business logic
- Define external layout (margin, position)

---

## Props Interface

### Required Props

```typescript
type CardProps = {
  children: React.ReactNode
  // Card content (body area)
}
```

### Optional Props

```typescript
type CardProps = {
  // Header
  title?: string
  // Card title/heading

  subtitle?: string
  // Optional subtitle below title

  headerActions?: React.ReactNode
  // Actions in header (buttons, menu, etc.)

  // Media
  image?: string
  // Image URL for card media

  imageAlt?: string
  // Alt text for image (required if image provided)

  imagePosition?: 'top' | 'left' | 'right' | 'background'
  // Default: 'top'

  // Footer
  footer?: React.ReactNode
  // Footer content (actions, metadata, etc.)

  // Visual Variants
  variant?: 'elevated' | 'outlined' | 'filled' | 'flat'
  // Default: 'elevated'

  // Interaction
  clickable?: boolean
  // Default: false
  // Adds hover effect and cursor pointer

  onClick?: (event: React.MouseEvent<HTMLDivElement>) => void
  // Click handler (requires clickable=true)

  href?: string
  // Makes entire card a link

  // Padding
  padding?: 'none' | 'sm' | 'md' | 'lg'
  // Default: 'md'
  // Applies to body area

  // Styling
  className?: string
  // Additional CSS classes

  // Accessibility
  role?: string
  // ARIA role if needed

  ariaLabel?: string
  // Accessible label for clickable cards

  // Testing
  testId?: string
  // data-testid attribute
}
```

---

## Variants

### Visual Variants

**1. Elevated (Default)**
- Use case: Standard cards, emphasis
- Visual: White background, box shadow
- Border: None or subtle
- Examples: Product cards, article cards

**2. Outlined**
- Use case: Secondary cards, less emphasis
- Visual: Border, no shadow
- Background: White or transparent
- Examples: Form sections, feature lists

**3. Filled**
- Use case: Highlighting, colored backgrounds
- Visual: Subtle background color, no shadow
- Border: None
- Examples: Info cards, stats cards

**4. Flat**
- Use case: Minimal styling, clean look
- Visual: No border, no shadow
- Background: White or transparent
- Examples: Simple content grouping

---

## Internal State

### Component State

```typescript
type CardState = {
  isHovered: boolean
  // For clickable cards
  // Default: false
}
```

**State Management:**
- `isHovered`: Managed by mouse enter/leave (if clickable)
- Typically handled by CSS `:hover` pseudo-class
- No explicit React state needed unless custom behavior required

---

## Visual Structure

### Full Featured Card

```
┌─────────────────────────────────────────┐
│  [Image/Media]                          │
├─────────────────────────────────────────┤
│  Title                      [Actions]   │
│  Subtitle                               │
├─────────────────────────────────────────┤
│                                         │
│  Body Content (children)                │
│                                         │
├─────────────────────────────────────────┤
│  Footer Content                         │
└─────────────────────────────────────────┘
```

### Minimal Card

```
┌─────────────────────────────────────────┐
│                                         │
│  Content (children)                     │
│                                         │
└─────────────────────────────────────────┘
```

### Layout Sections

**1. Media Area (Optional)**
- Image at top, left, right, or background
- Maintains aspect ratio
- Responsive sizing

**2. Header (Optional)**
- Title (prominent text)
- Subtitle (smaller, muted)
- Actions (right-aligned)

**3. Body (Required)**
- Main content area
- Children rendered here
- Configurable padding

**4. Footer (Optional)**
- Actions, metadata, links
- Bottom-aligned
- Separate from body

---

## Visual States

### Default State
- Standard variant styling
- No interaction effects

### Hover State (Clickable Only)
- Subtle elevation increase (shadow grows)
- Slight upward transform (1-2px)
- Cursor: pointer
- Transition: 150ms ease

### Focus State (Clickable/Link)
- Visible focus ring (2-3px)
- High contrast outline
- Keyboard navigation indicator

### Active/Pressed State (Clickable)
- Slight downward transform
- Shadow reduces slightly
- Provides tactile feedback

---

## Behavior Specifications

### Click Handling

**Clickable Card:**
- Set clickable={true}
- Entire card becomes interactive
- onClick fires on click or keyboard activation
- Cursor changes to pointer
- Hover and focus effects enabled

**Link Card:**
- Provide href prop
- Entire card becomes anchor link
- Supports target, rel attributes
- Maintains accessibility

**Non-Clickable:**
- Default behavior
- No hover effects
- Children can have their own click handlers

### Image Positioning

**Top (Default):**
- Image spans full card width
- Above header and content
- Common for articles, products

**Left:**
- Image on left, content on right
- Side-by-side layout
- Responsive: Stacks on mobile

**Right:**
- Image on right, content on left
- Mirror of left layout

**Background:**
- Image as card background
- Content overlaid on image
- Ensure text contrast

---

## Accessibility Requirements

### WCAG 2.1 AA Compliance

**Semantic HTML:**
- Use `<article>`, `<section>`, or `<div>` as base
- Header uses appropriate heading level
- Image has alt text (required if image provided)

**Clickable Card Accessibility:**
- Entire card focusable (tabindex=0 or wrapped in link)
- Keyboard operable (Enter/Space activates)
- Focus indicator visible
- aria-label describes card purpose
- role="button" or role="link" if applicable

**Screen Reader Support:**
- Title announced as heading
- Image alt text announced
- Clickable cards announced as interactive
- Complex cards may need aria-labelledby

**Keyboard Accessibility:**
- Clickable cards in tab order
- Enter/Space activates onClick
- Focus indicator meets 3:1 contrast
- Nested interactive elements (buttons) accessible

**Visual Accessibility:**
- Text contrast: 4.5:1 minimum
- Focus indicator: 3:1 contrast
- Don't rely on color alone for variants
- If background image: Ensure text overlay contrast

---

## Padding Specifications

### Padding Variants

**None:**
- Padding: 0
- Use case: Full-bleed content, custom padding

**Small (sm):**
- Padding: 12px
- Use case: Compact cards, mobile

**Medium (md) - Default:**
- Padding: 16px (mobile) / 20px (desktop)
- Use case: Standard cards

**Large (lg):**
- Padding: 24px (mobile) / 32px (desktop)
- Use case: Feature cards, hero cards

**Applies To:**
- Body content area
- Header and footer have fixed padding (typically 16px)

---

## Image Specifications

### Image Sizing

**Top Image:**
- Width: 100% of card
- Height: Auto (maintain aspect ratio) or fixed (e.g., 200px)
- Object-fit: cover

**Side Image (Left/Right):**
- Width: 40% of card (desktop), 100% (mobile)
- Height: Match content height or fixed
- Object-fit: cover

**Background Image:**
- Width/Height: 100% of card
- Background-size: cover
- Background-position: center

### Image Optimization

**Loading:**
- Lazy load images below fold
- Placeholder while loading (skeleton or blur)
- Handle load errors (fallback image)

**Responsive:**
- Use srcset for different screen sizes
- WebP format support
- Appropriate dimensions for context

---

## Styling Specifications

### No Layout Assumptions

**Component Controls:**
- Own padding (body content)
- Own border (variant-dependent)
- Own background color
- Own box shadow (variant-dependent)
- Own border radius (4px to 12px)
- Internal spacing (header, footer)

**Component Does NOT Control:**
- Margin (parent controls spacing)
- Width (parent controls size)
- Position (parent controls placement)
- Flex/grid properties (parent controls layout)

### Shadow Specifications

**Elevated Variant:**
- Default: 0 1px 3px rgba(0,0,0,0.12)
- Hover: 0 4px 12px rgba(0,0,0,0.15)

**Outlined/Filled/Flat:**
- No shadow (or minimal: 0 1px 2px)

---

## Reusability Considerations

### Framework Agnostic

**Core Requirements:**
- Container component
- Flexible children
- Optional header, footer, image
- Variant styling
- Accessibility compliant

**Implementation Flexibility:**
- CSS/Tailwind/Styled Components
- Any image optimization library
- Composable with Button, Badge, etc.

### Composition Patterns

**Simple Card:**
```
<Card>
  <p>Card content here</p>
</Card>
```

**Card with Header:**
```
<Card title="Card Title" subtitle="Subtitle">
  <p>Card body content</p>
</Card>
```

**Card with Image:**
```
<Card
  image="/image.jpg"
  imageAlt="Description"
  title="Title"
>
  Content here
</Card>
```

**Clickable Card:**
```
<Card
  clickable
  onClick={handleClick}
  ariaLabel="View details"
>
  Interactive content
</Card>
```

**Card with Footer:**
```
<Card
  title="Product"
  footer={<Button>View Details</Button>}
>
  Product description
</Card>
```

**Link Card:**
```
<Card
  href="/destination"
  title="Article Title"
  image="/thumb.jpg"
>
  Article preview...
</Card>
```

---

## Usage Examples (Conceptual)

### Product Card
```
<Card
  variant="elevated"
  image="/product.jpg"
  imageAlt="Product name"
  title="Product Name"
  footer={<Button>Add to Cart</Button>}
>
  $29.99 - In stock
</Card>
```

### Article Card
```
<Card
  clickable
  onClick={handleReadArticle}
  image="/article.jpg"
  title="Article Headline"
  subtitle="By Author Name"
>
  Article excerpt text...
</Card>
```

### Stats Card
```
<Card variant="filled" padding="lg">
  <h3>Total Users</h3>
  <p>1,234</p>
</Card>
```

### Feature Card
```
<Card
  variant="outlined"
  title="Feature Name"
  headerActions={<Badge>New</Badge>}
>
  Feature description goes here.
</Card>
```

---

## Performance Considerations

### Optimization

**Rendering:**
- Pure component
- Memoize if in large lists
- Minimize DOM nodes

**Images:**
- Lazy load images
- Use appropriate formats (WebP)
- Srcset for responsive images
- Optimize file sizes

**Interactions:**
- CSS transforms for hover (GPU accelerated)
- Avoid JavaScript animations
- Debounce click handlers if needed

**Bundle Size:**
- Small component (<4KB)
- No heavy dependencies

---

## Testing Considerations

### Test Cases

**Rendering:**
- Renders with children
- Renders all variants correctly
- Renders with title and subtitle
- Renders with image (all positions)
- Renders with header actions
- Renders with footer
- Renders with different padding sizes

**Interaction:**
- onClick fires when clickable card clicked
- onClick fires on Enter key (clickable)
- onClick fires on Space key (clickable)
- href navigates when link card clicked
- Non-clickable cards don't respond to clicks

**Accessibility:**
- Title rendered as heading
- Image has alt text
- Clickable card has aria-label
- Clickable card is keyboard accessible
- Focus indicator visible

**States:**
- Hover effect shows on clickable cards
- No hover effect on non-clickable cards
- Focus state visible on keyboard navigation

---

## Browser Compatibility

**Supported Browsers:**
- Chrome/Edge (last 2 versions)
- Firefox (last 2 versions)
- Safari (last 2 versions)
- Mobile Safari (iOS 14+)
- Chrome Mobile (Android 10+)

**Progressive Enhancement:**
- Basic card works without JavaScript
- Link cards work with JavaScript disabled
- Graceful degradation for animations

---

## Dependencies

**Required:**
- None (pure component)

**Optional:**
- Image optimization library
- Button component (for actions)
- Badge component (for header)

---

## Acceptance Criteria

- [ ] Component renders with children content
- [ ] All 4 visual variants render correctly
- [ ] Title displays when provided
- [ ] Subtitle displays when provided
- [ ] Header actions display when provided
- [ ] Footer displays when provided
- [ ] Image displays at top position
- [ ] Image displays at left position (desktop)
- [ ] Image displays at right position (desktop)
- [ ] Image has alt text when provided
- [ ] All padding variants apply correctly
- [ ] Clickable card has pointer cursor
- [ ] Clickable card has hover effect
- [ ] onClick fires when clickable card clicked
- [ ] onClick fires on Enter key (clickable)
- [ ] onClick fires on Space key (clickable)
- [ ] href creates link behavior
- [ ] Link card navigates on click
- [ ] Non-clickable cards have no interaction effects
- [ ] Focus indicator visible on clickable cards
- [ ] Focus indicator meets 3:1 contrast
- [ ] Text contrast meets 4.5:1 ratio
- [ ] Elevated variant has box shadow
- [ ] Outlined variant has border
- [ ] Clickable card has aria-label (if needed)
- [ ] Title rendered as heading element
- [ ] Component has no margin (parent controlled)
- [ ] Images lazy load (below fold)
- [ ] Hover animation smooth (150ms transition)
- [ ] Respects prefers-reduced-motion

---

**Related Specifications:**
- Component: Button (for actions)
- Component: Badge (for header decoration)
- Pattern: Product Cards
- Pattern: Content Grids
