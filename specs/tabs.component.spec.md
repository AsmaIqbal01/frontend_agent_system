# Tabs Component Specification

**Component Type:** UI / Navigation
**Single Responsibility:** Provide navigation between content sections within the same context
**Version:** 1.0.0

---

## Purpose

Enable users to switch between different content views or sections without leaving the current page. Provide clear visual indication of the active tab and support keyboard navigation for accessibility.

---

## Component Responsibility

**Single Responsibility:** Manage tab selection and display corresponding content panels

**Does:**
- Render tab list with labels
- Indicate active tab visually
- Handle tab selection (click and keyboard)
- Display content panel for active tab
- Support controlled and uncontrolled modes
- Provide keyboard navigation (arrow keys)
- Announce state changes to screen readers

**Does NOT:**
- Fetch data for tab content (parent responsibility)
- Manage complex application state
- Handle routing (use router integration for URL tabs)
- Contain business logic

---

## Props Interface

### Required Props

```typescript
type TabsProps = {
  tabs: Array<{
    id: string
    label: string
    content: React.ReactNode
    icon?: React.ReactNode
    disabled?: boolean
    badge?: React.ReactNode
  }>
  // Array of tab configurations
}
```

### Optional Props

```typescript
type TabsProps = {
  // Controlled Mode
  activeTab?: string
  // Active tab ID (controlled by parent)

  onTabChange?: (tabId: string) => void
  // Callback when tab changes
  // Required if controlled mode (activeTab provided)

  // Uncontrolled Mode
  defaultTab?: string
  // Default active tab ID (uncontrolled mode)

  // Visual Variants
  variant?: 'line' | 'enclosed' | 'pills'
  // Default: 'line'

  // Orientation
  orientation?: 'horizontal' | 'vertical'
  // Default: 'horizontal'

  // Behavior
  lazy?: boolean
  // Default: false
  // Lazy render tab content (only render active)

  keepMounted?: boolean
  // Default: false
  // Keep inactive tabs mounted in DOM (hidden)

  // Layout
  fullWidth?: boolean
  // Default: false
  // Tabs span full container width

  // Styling
  className?: string
  // Additional CSS classes for container

  tabListClassName?: string
  // Additional CSS classes for tab list

  // Accessibility
  ariaLabel?: string
  // Accessible label for tab list

  // Testing
  testId?: string
  // data-testid attribute
}
```

---

## Variants

### Visual Variants

**1. Line (Default)**
- Use case: Standard tabs, clean look
- Visual: Tabs with bottom border indicator on active
- Border: Bottom border moves to active tab
- Examples: Content sections, settings

**2. Enclosed**
- Use case: Distinct separation, emphasis
- Visual: Tabs look like folder tabs
- Border: Active tab connects to content panel
- Examples: Multi-step forms, complex UIs

**3. Pills**
- Use case: Modern look, button-like
- Visual: Rounded pill-shaped tabs
- Background: Active tab has filled background
- Examples: Filters, categories

---

## Internal State

### Component State

```typescript
type TabsState = {
  activeTabId: string
  // Currently active tab
  // Managed internally (uncontrolled) or by parent (controlled)

  focusedTabIndex: number
  // For keyboard navigation
  // Default: -1 (no focus)
}
```

**State Management:**
- Uncontrolled: Component manages activeTabId internally
- Controlled: Parent manages activeTabId via props
- focusedTabIndex: Tracks which tab has keyboard focus

---

## Visual Structure

### Horizontal Tabs

```
┌───────────────────────────────────────┐
│ Tab 1   Tab 2   Tab 3                │ ← Tab List
└───────────────────────────────────────┘
    ▔▔▔▔▔  (active indicator)

┌───────────────────────────────────────┐
│                                       │
│  Content for active tab               │ ← Tab Panel
│                                       │
└───────────────────────────────────────┘
```

### Vertical Tabs

```
┌──────┬────────────────────────────────┐
│ Tab1 │                                │
├──────┤  Content for active tab        │
│►Tab2 │                                │
├──────┤                                │
│ Tab3 │                                │
└──────┴────────────────────────────────┘
  ▌
  Active indicator
```

### Tab Structure

**Tab Button:**
- Icon (optional, left)
- Label text
- Badge (optional, right)
- Active indicator (underline, background, or border)

---

## Visual States

### Tab States

**Inactive Tab:**
- Normal text color (gray or muted)
- No active indicator
- Cursor: pointer

**Active Tab:**
- Bold or emphasized text
- Active indicator visible
- Color: Primary brand color

**Hover Tab (Inactive):**
- Background highlight (subtle)
- Text color darkens
- Cursor: pointer

**Focus Tab:**
- Visible focus ring (2px outline)
- High contrast
- Keyboard navigation indicator

**Disabled Tab:**
- Reduced opacity (40-50%)
- Cursor: not-allowed
- Not selectable
- Not in keyboard tab order

---

## Behavior Specifications

### Tab Selection

**Click Selection:**
1. User clicks tab button
2. If tab disabled: No action
3. If tab active: No change (optional: re-trigger callback)
4. If different tab:
   - Call onTabChange with new tab ID
   - Update activeTabId (uncontrolled) or parent updates (controlled)
   - Display new tab content
   - Announce change to screen readers

**Keyboard Selection:**
1. Focus on tab list
2. Arrow Left/Right (horizontal) or Up/Down (vertical): Move focus
3. Home: Focus first tab
4. End: Focus last tab
5. Enter/Space: Activate focused tab
6. Tab key: Move out of tab list to content

### Content Display

**Active Content:**
- Show content panel for active tab
- Hide other tab panels

**Lazy Loading:**
- If lazy=true: Only render active tab content
- Unmount inactive tabs
- Mount on tab activation

**Keep Mounted:**
- If keepMounted=true: Render all tab content
- Hide inactive with display:none or visibility
- Preserve state of inactive tabs

---

## Accessibility Requirements

### WCAG 2.1 AA Compliance

**Semantic HTML:**
- Tab list: `role="tablist"`
- Tab button: `role="tab"`
- Tab panel: `role="tabpanel"`
- aria-selected on active tab
- aria-controls linking tab to panel
- aria-labelledby linking panel to tab

**Keyboard Navigation:**
- Tab to tab list: Focus first tab (or active tab)
- Arrow keys: Navigate between tabs (circular)
- Home: First tab
- End: Last tab
- Enter/Space: Activate focused tab
- Tab key: Move to tab panel content
- Shift+Tab: Move out of tabs backward

**Screen Reader Support:**
- Tab role announced: "Tab, X of Y"
- Active state announced: "Selected"
- Tab label announced
- Tab panel content announced
- Badge content announced (if present)

**ARIA Attributes:**
- Tab list: `role="tablist"`, optional `aria-label`
- Tab: `role="tab"`, `aria-selected`, `aria-controls`, `aria-disabled`
- Panel: `role="tabpanel"`, `aria-labelledby`, `tabindex="0"`

**Focus Management:**
- Tab buttons focusable
- Disabled tabs not focusable (tabindex=-1)
- Active tab panel focusable (for screen reader navigation)
- Focus visible indicators

**Visual Accessibility:**
- Active indicator contrast: 3:1 minimum
- Text contrast: 4.5:1 minimum
- Focus ring: 2px outline, 3:1 contrast
- Don't rely on color alone (use indicator + text weight)

---

## Orientation Specifications

### Horizontal (Default)

**Tab List:**
- Flex direction: row
- Tabs arranged left to right
- Active indicator: Bottom border

**Keyboard:**
- Arrow Right: Next tab
- Arrow Left: Previous tab

**Responsive:**
- May scroll horizontally if too many tabs
- Or wrap to multiple rows (design decision)

### Vertical

**Tab List:**
- Flex direction: column
- Tabs arranged top to bottom
- Active indicator: Left border or background

**Keyboard:**
- Arrow Down: Next tab
- Arrow Up: Previous tab

**Layout:**
- Tabs on left, content on right (typical)
- Fixed width tab list, flexible content area

---

## Icon and Badge Support

### Tab with Icon

**Icon Position:**
- Left of label text
- Vertically centered
- Spacing: 8px between icon and text
- Size: 16-20px

**Icon States:**
- Inherits tab color (inactive, active, hover)
- Decorative: aria-hidden="true"

### Tab with Badge

**Badge Position:**
- Right of label text
- Vertically centered
- Spacing: 8px between text and badge
- Use case: Notification counts, status

**Badge Interaction:**
- Badge is part of tab button (not separate click target)
- Announced by screen reader as part of tab label

---

## Reusability Considerations

### Framework Agnostic

**Core Requirements:**
- Tab navigation component
- Controlled and uncontrolled modes
- Keyboard navigation
- Accessibility compliant

**Implementation Flexibility:**
- CSS/Tailwind/Styled Components
- Any icon library
- Composable with Badge, Icon components
- Support for lazy loading patterns

### Composition Patterns

**Simple Tabs:**
```
<Tabs
  tabs={[
    { id: 'tab1', label: 'Tab 1', content: <Content1 /> },
    { id: 'tab2', label: 'Tab 2', content: <Content2 /> },
    { id: 'tab3', label: 'Tab 3', content: <Content3 /> }
  ]}
/>
```

**Controlled Tabs:**
```
<Tabs
  tabs={tabsData}
  activeTab={currentTab}
  onTabChange={setCurrentTab}
/>
```

**Tabs with Icons:**
```
<Tabs
  tabs={[
    { id: 'home', label: 'Home', icon: <HomeIcon />, content: <Home /> },
    { id: 'profile', label: 'Profile', icon: <UserIcon />, content: <Profile /> }
  ]}
/>
```

**Tabs with Badges:**
```
<Tabs
  tabs={[
    { id: 'inbox', label: 'Inbox', badge: <Badge>5</Badge>, content: <Inbox /> },
    { id: 'sent', label: 'Sent', content: <Sent /> }
  ]}
/>
```

**Vertical Pills:**
```
<Tabs
  tabs={tabsData}
  variant="pills"
  orientation="vertical"
/>
```

---

## Usage Examples (Conceptual)

### Settings Tabs
```
<Tabs
  variant="line"
  tabs={[
    { id: 'general', label: 'General', content: <GeneralSettings /> },
    { id: 'security', label: 'Security', content: <SecuritySettings /> },
    { id: 'notifications', label: 'Notifications', content: <NotificationSettings /> }
  ]}
/>
```

### Dashboard Sections
```
<Tabs
  variant="pills"
  defaultTab="overview"
  tabs={[
    { id: 'overview', label: 'Overview', icon: <DashboardIcon />, content: <Overview /> },
    { id: 'analytics', label: 'Analytics', icon: <ChartIcon />, content: <Analytics /> },
    { id: 'reports', label: 'Reports', icon: <FileIcon />, content: <Reports /> }
  ]}
/>
```

### Content with Notifications
```
<Tabs
  tabs={[
    { id: 'all', label: 'All', content: <AllMessages /> },
    {
      id: 'unread',
      label: 'Unread',
      badge: <Badge variant="error">{unreadCount}</Badge>,
      content: <UnreadMessages />
    }
  ]}
/>
```

---

## Performance Considerations

### Optimization

**Rendering:**
- Lazy render tab content (lazy prop)
- Memoize tab panels
- Only re-render active panel on tab change

**Keyboard Navigation:**
- Debounce arrow key navigation (prevent rapid firing)
- Use keyboard event listeners efficiently

**Bundle Size:**
- Component: ~5-7KB
- No heavy dependencies

**Large Number of Tabs:**
- Consider virtualization if 50+ tabs
- Scrollable tab list
- Lazy load content

---

## Testing Considerations

### Test Cases

**Rendering:**
- Renders all tabs in tab list
- Renders active tab content
- Renders tab with icon
- Renders tab with badge
- Renders disabled tab

**Interaction:**
- Click tab activates it
- onTabChange fires with correct tab ID
- Disabled tab cannot be clicked
- Active tab click triggers callback (or doesn't, based on design)

**Keyboard Navigation:**
- Arrow keys navigate between tabs
- Home key focuses first tab
- End key focuses last tab
- Enter activates focused tab
- Space activates focused tab
- Tab key moves to panel content

**Controlled/Uncontrolled:**
- Uncontrolled mode manages state internally
- Controlled mode uses parent state
- defaultTab sets initial tab (uncontrolled)

**Accessibility:**
- Tab list has role="tablist"
- Tabs have role="tab"
- Panels have role="tabpanel"
- aria-selected on active tab
- aria-controls links tab to panel
- Keyboard navigation works
- Screen reader announces tab state

**Content Display:**
- Active tab content visible
- Inactive tab content hidden (or unmounted if lazy)
- keepMounted keeps content in DOM

---

## Browser Compatibility

**Supported Browsers:**
- Chrome/Edge (last 2 versions)
- Firefox (last 2 versions)
- Safari (last 2 versions)
- Mobile Safari (iOS 14+)
- Chrome Mobile (Android 10+)

**Progressive Enhancement:**
- Basic tab navigation works without JavaScript (if server-rendered)
- Keyboard navigation requires JavaScript
- Fallback: Show all content if JavaScript disabled

---

## Dependencies

**Required:**
- None (pure component)

**Optional:**
- Icon library (for tab icons)
- Badge component (for notifications)

---

## Acceptance Criteria

- [ ] Component renders all tabs in tab list
- [ ] Active tab content displays
- [ ] Inactive tab content hidden
- [ ] All 3 visual variants render correctly
- [ ] Line variant shows bottom border on active tab
- [ ] Enclosed variant shows connected tabs and panel
- [ ] Pills variant shows filled background on active tab
- [ ] Horizontal orientation arranges tabs left-to-right
- [ ] Vertical orientation arranges tabs top-to-bottom
- [ ] Click tab activates it and shows content
- [ ] onTabChange fires with correct tab ID
- [ ] Disabled tab cannot be clicked
- [ ] Disabled tab has reduced opacity
- [ ] Tab with icon displays icon before label
- [ ] Tab with badge displays badge after label
- [ ] Arrow Right navigates to next tab (horizontal)
- [ ] Arrow Left navigates to previous tab (horizontal)
- [ ] Arrow Down navigates to next tab (vertical)
- [ ] Arrow Up navigates to previous tab (vertical)
- [ ] Home key focuses first tab
- [ ] End key focuses last tab
- [ ] Enter key activates focused tab
- [ ] Space key activates focused tab
- [ ] Tab list has role="tablist"
- [ ] Tabs have role="tab"
- [ ] Panels have role="tabpanel"
- [ ] Active tab has aria-selected="true"
- [ ] Inactive tabs have aria-selected="false"
- [ ] Tab has aria-controls pointing to panel ID
- [ ] Panel has aria-labelledby pointing to tab ID
- [ ] Focus indicator visible on tabs
- [ ] Focus indicator meets 3:1 contrast
- [ ] Active indicator meets 3:1 contrast
- [ ] Text contrast meets 4.5:1 ratio
- [ ] Lazy mode only renders active tab content
- [ ] keepMounted keeps all content in DOM (hidden)
- [ ] Controlled mode respects activeTab prop
- [ ] Uncontrolled mode manages state internally
- [ ] defaultTab sets initial active tab (uncontrolled)
- [ ] fullWidth prop makes tabs span container

---

**Related Specifications:**
- Component: Button (tabs are button-like)
- Component: Badge (for notifications)
- Pattern: Multi-Step Forms
- Pattern: Content Organization
