# Component Decomposition Skill

## Skill Identity
- **Name**: Component Decomposition Skill
- **Type**: Analysis & Design Skill
- **Domain**: UI Architecture, Component Design
- **Version**: 1.0.0
- **Responsibility**: Break down complex UI into atomic, reusable components

---

## Purpose

This skill analyzes UI requirements and decomposes them into a hierarchy of atomic, reusable components following the Single Responsibility Principle. It helps agents make decisions about component boundaries, reusability, and composition patterns.

---

## When to Use This Skill

Use this skill when:
- ✅ Analyzing a page specification to identify components
- ✅ Designing a new feature with complex UI
- ✅ Refactoring monolithic components into smaller pieces
- ✅ Determining if a UI pattern should be extracted as a component
- ✅ Planning component hierarchy for a design system
- ✅ Evaluating if components are properly atomic

Do NOT use this skill for:
- ❌ Writing component implementations (use UI Agent instead)
- ❌ Defining component behavior/interactions (use UX Agent instead)
- ❌ Managing component state (use State Agent instead)

---

## Inputs

### Required Inputs
1. **UI Description**: Text, wireframe, or specification describing the UI
2. **Context**: Where this UI will be used (page, feature, design system)
3. **Reusability Goal**: High (design system), Medium (feature-level), Low (page-specific)

### Optional Inputs
4. **Existing Components**: List of components already available
5. **Framework Context**: React, Vue, Svelte (for example syntax only)
6. **Constraints**: Performance, bundle size, accessibility requirements

---

## Outputs

### Primary Outputs
1. **Component Hierarchy**: Tree structure of components and their relationships
2. **Component Inventory**: List of components with purpose and responsibility
3. **Reusability Classification**: Atomic, Molecular, Organism, Template
4. **Composition Patterns**: How components compose together

### Secondary Outputs
5. **Extraction Recommendations**: Which patterns should be extracted
6. **Naming Suggestions**: Semantic, consistent component names
7. **Props Interface Draft**: Initial props for each component (framework-agnostic)

---

## Decision-Making Criteria

### 1. Single Responsibility Principle (SRP)

**Question**: Does this component have exactly one reason to change?

**Decision Tree**:
```
Does the component do multiple things?
├─ No → ✅ Keep as single component
└─ Yes → Split by responsibility
    ├─ Presentation + Data Fetching → Split into Container + Presentational
    ├─ Multiple UI concerns → Split by visual/functional boundary
    └─ Conditional rendering of different UI → Split into separate components
```

**Examples**:
- ❌ **Bad**: `UserProfileCard` that fetches user data, displays profile, and handles editing
- ✅ **Good**: `UserProfileCard` (display), `UserProfileEditor` (editing), `useUserData` (data fetching)

---

### 2. Reusability Assessment

**Question**: Will this component be used in multiple places?

**Decision Matrix**:

| Usage Pattern | Reusability | Action |
|---------------|-------------|--------|
| Used 3+ times across different pages/features | **High** | Extract as atomic/molecular component in design system |
| Used 2-3 times within same feature | **Medium** | Extract as feature-level component |
| Used once, but pattern is common (button, input, card) | **High** | Extract as atomic component (common pattern) |
| Used once, highly specific to context | **Low** | Keep inline or as page-specific component |

**Examples**:
- ✅ **High Reusability**: Button, Input, Card, Modal, Alert (used everywhere)
- ✅ **Medium Reusability**: ProductCard, CommentItem (used within e-commerce/social features)
- ✅ **Low Reusability**: CheckoutSuccessMessage, LoginPageHeader (page-specific)

---

### 3. Atomic Design Classification

**Atoms**: Smallest, indivisible UI elements
- Cannot be broken down further
- Single, focused purpose
- Highly reusable
- Examples: Button, Input, Label, Icon, Badge, Spinner

**Molecules**: Simple groups of atoms functioning together
- 2-4 atoms combined
- Represent a single UI pattern
- Moderately reusable
- Examples: SearchInput (Input + Button), FormField (Label + Input + ErrorMessage), IconButton (Icon + Button)

**Organisms**: Complex groups of molecules and/or atoms
- Form complete sections of UI
- Self-contained functionality
- Feature-specific reusability
- Examples: NavigationBar, ProductCard, CommentThread, UserProfileCard

**Templates**: Page-level layouts
- Arrange organisms into page structure
- Define layout patterns
- Provide structure without content
- Examples: DashboardLayout, TwoColumnLayout, AuthLayout

**Decision Tree**:
```
Can this be broken down further?
├─ No → Is it a single element?
│   ├─ Yes → ATOM
│   └─ No → MOLECULE (2-4 atoms)
└─ Yes → Is it a page layout?
    ├─ Yes → TEMPLATE
    └─ No → ORGANISM
```

---

### 4. Composition vs Specialization

**Question**: Should this be a composite component or a specialized variant?

**Choose Composition When**:
- Component combines multiple distinct parts
- Parts can be rearranged or optional
- Flexibility is more important than convenience

**Example - Composition**:
```typescript
// Composite Card - flexible, parts can be rearranged
<Card>
  <CardHeader>
    <CardTitle>Title</CardTitle>
    <CardActions>...</CardActions>
  </CardHeader>
  <CardContent>Content</CardContent>
  <CardFooter>Footer</CardFooter>
</Card>
```

**Choose Specialization When**:
- Component is a specific variant of a base component
- Variant has consistent, predictable structure
- Convenience is more important than flexibility

**Example - Specialization**:
```typescript
// Specialized variants - convenient, consistent
<PrimaryButton>Submit</PrimaryButton>
<DangerButton>Delete</DangerButton>
<GhostButton>Cancel</GhostButton>

// Better than:
<Button variant="primary">Submit</Button>
<Button variant="danger">Delete</Button>
<Button variant="ghost">Cancel</Button>
```

**Recommendation**: Prefer composition for complex components, specialization for simple variants.

---

### 5. Container vs Presentational Pattern

**Question**: Does this component manage state/side-effects or just present UI?

**Container Component (Smart)**:
- Manages state
- Handles data fetching
- Contains business logic
- Passes data to presentational components
- Examples: `UserProfileContainer`, `ProductListContainer`, `CheckoutFormContainer`

**Presentational Component (Dumb)**:
- Receives data via props
- No state management (except UI state like "open/closed")
- Pure presentation
- Highly reusable
- Examples: `UserProfileCard`, `ProductList`, `CheckoutForm`

**Decision**:
```
Does component need to fetch data or manage complex state?
├─ Yes → Split into Container (logic) + Presentational (UI)
└─ No → Keep as single Presentational component
```

**Example**:
```
❌ Bad - Mixed concerns:
- UserProfile (fetches data + renders UI)

✅ Good - Separated:
- UserProfileContainer (fetches data, passes to UI)
- UserProfileCard (receives props, renders UI)
```

---

## Decomposition Patterns

### Pattern 1: Feature Decomposition

**Use When**: Breaking down a complex feature into components

**Process**:
1. Identify major visual sections (header, content, sidebar, footer)
2. Extract repeated patterns within sections
3. Break sections into smaller components
4. Extract atomic UI elements

**Example: E-commerce Product Page**

```
ProductPage (Template)
├── ProductPageHeader (Organism)
│   ├── Breadcrumb (Molecule)
│   │   └── BreadcrumbItem (Atom)
│   └── BackButton (Atom)
├── ProductDetails (Organism)
│   ├── ProductImageGallery (Organism)
│   │   ├── ProductImage (Molecule)
│   │   └── ImageThumbnail (Atom)
│   ├── ProductInfo (Organism)
│   │   ├── ProductTitle (Atom)
│   │   ├── ProductPrice (Molecule)
│   │   │   ├── Price (Atom)
│   │   │   └── Badge (Atom) [if on sale]
│   │   ├── ProductRating (Molecule)
│   │   │   ├── StarRating (Molecule)
│   │   │   │   └── Star (Atom)
│   │   │   └── RatingCount (Atom)
│   │   ├── ProductDescription (Atom)
│   │   └── AddToCartButton (Molecule)
│   │       ├── QuantitySelector (Molecule)
│   │       │   └── Button (Atom)
│   │       └── Button (Atom)
├── ProductReviews (Organism)
│   ├── ReviewsSummary (Molecule)
│   ├── ReviewFilters (Molecule)
│   └── ReviewList (Organism)
│       └── ReviewItem (Molecule)
│           ├── Avatar (Atom)
│           ├── StarRating (Molecule)
│           ├── ReviewText (Atom)
│           └── ReviewDate (Atom)
└── RelatedProducts (Organism)
    └── ProductCard (Molecule)
        ├── ProductImage (Molecule)
        ├── ProductTitle (Atom)
        └── ProductPrice (Molecule)
```

**Reusable Components Identified**:
- **High Reusability**: Button, Badge, Avatar, Star, Price, ProductImage, ProductTitle, ProductPrice, ProductCard, StarRating
- **Medium Reusability**: Breadcrumb, QuantitySelector, ReviewItem
- **Low Reusability**: ProductPageHeader, ProductDetails, ProductReviews

---

### Pattern 2: Layout Extraction

**Use When**: Multiple pages share similar layout structure

**Process**:
1. Identify common layout patterns across pages
2. Extract layout structure into template components
3. Use composition (slots/children) for variable content

**Example: Dashboard Layouts**

```
DashboardLayout (Template)
├── Sidebar (Organism) - Fixed
├── Header (Organism) - Fixed
└── MainContent (Slot) - Variable per page
    └── [Page-specific content]

// Usage:
<DashboardLayout>
  <AnalyticsPage /> // Different content
</DashboardLayout>

<DashboardLayout>
  <SettingsPage /> // Different content
</DashboardLayout>
```

**Common Layout Templates**:
- `AuthLayout`: Centered card on branded background (login, register, reset password)
- `DashboardLayout`: Sidebar + header + main content
- `TwoColumnLayout`: Main content + sidebar
- `MarketingLayout`: Full-width header + content + footer
- `LandingLayout`: Hero + sections + footer

---

### Pattern 3: Repeated Pattern Extraction

**Use When**: Same UI pattern appears multiple times

**Process**:
1. Identify repeated visual/functional patterns
2. Parameterize differences as props
3. Extract as reusable component

**Example: Stat Cards**

```
// Instead of repeating:
<div class="stat-card">
  <h3>Total Users</h3>
  <p class="stat-value">1,234</p>
  <span class="stat-change positive">+12%</span>
</div>

<div class="stat-card">
  <h3>Revenue</h3>
  <p class="stat-value">$45,678</p>
  <span class="stat-change negative">-5%</span>
</div>

// Extract as:
<StatCard
  title="Total Users"
  value="1,234"
  change="+12%"
  changeType="positive"
/>

<StatCard
  title="Revenue"
  value="$45,678"
  change="-5%"
  changeType="negative"
/>
```

---

### Pattern 4: Conditional Component Splitting

**Use When**: Component has significantly different UI based on conditions

**Process**:
1. Identify major conditional branches
2. If branches are substantially different, split into separate components
3. Use a wrapper/router component to choose which to render

**Example: Multi-Step Form**

```
❌ Bad - One component with many conditionals:
function CheckoutForm() {
  if (step === 1) return <ShippingAddressForm />
  if (step === 2) return <PaymentMethodForm />
  if (step === 3) return <OrderReviewForm />
  if (step === 4) return <OrderConfirmation />
}

✅ Good - Separate components:
// CheckoutStepper.tsx (router component)
function CheckoutStepper({ step }) {
  const steps = {
    1: ShippingAddressForm,
    2: PaymentMethodForm,
    3: OrderReviewForm,
    4: OrderConfirmation,
  }
  const StepComponent = steps[step]
  return <StepComponent />
}

// Separate files:
// - ShippingAddressForm.tsx
// - PaymentMethodForm.tsx
// - OrderReviewForm.tsx
// - OrderConfirmation.tsx
```

---

### Pattern 5: Data Structure Decomposition

**Use When**: Component renders a complex data structure

**Process**:
1. Map data structure to component hierarchy
2. Create a component for each data entity
3. Use composition to build complete structure

**Example: Nested Comments**

```
// Data Structure:
{
  id: 1,
  text: "Parent comment",
  author: { name: "John", avatar: "..." },
  replies: [
    { id: 2, text: "Reply 1", author: {...}, replies: [] },
    { id: 3, text: "Reply 2", author: {...}, replies: [...] }
  ]
}

// Component Structure (mirrors data):
CommentThread (Organism)
└── Comment (Molecule)
    ├── CommentHeader (Molecule)
    │   ├── Avatar (Atom)
    │   └── AuthorName (Atom)
    ├── CommentText (Atom)
    ├── CommentActions (Molecule)
    │   └── Button (Atom)
    └── CommentReplies (Organism) // Recursive
        └── Comment (Molecule) // Same component, nested
```

---

## Component Naming Conventions

### Atomic Components (Atoms)
- Use simple, descriptive nouns
- Indicate element type
- Examples: `Button`, `Input`, `Label`, `Badge`, `Avatar`, `Icon`, `Divider`, `Spinner`

### Composite Components (Molecules)
- Combine atom names or use domain term
- Indicate composition
- Examples: `SearchInput`, `FormField`, `IconButton`, `PriceTag`, `StatCard`, `BreadcrumbItem`

### Complex Components (Organisms)
- Use domain/feature-specific names
- Indicate purpose/context
- Examples: `NavigationBar`, `ProductCard`, `UserProfile`, `CommentThread`, `ShoppingCart`, `AuthForm`

### Layout Components (Templates)
- Use "Layout" suffix
- Indicate structure pattern
- Examples: `DashboardLayout`, `TwoColumnLayout`, `AuthLayout`, `GridLayout`, `StackLayout`

### Container Components
- Use "Container" suffix
- Indicate data/logic management
- Examples: `ProductListContainer`, `UserProfileContainer`, `CheckoutContainer`

---

## Quality Checks

### ✅ Well-Decomposed Component Hierarchy

**Indicators**:
- [ ] Each component has single, clear responsibility
- [ ] No component is overly complex (< 200 lines of template/JSX)
- [ ] Atomic components are truly atomic (can't be broken down)
- [ ] Molecules combine 2-4 atoms meaningfully
- [ ] Organisms are self-contained, complete UI sections
- [ ] Templates define layout without content specifics
- [ ] Component names are clear and semantic
- [ ] Nesting depth is reasonable (< 5 levels)
- [ ] Repeated patterns are extracted as reusable components
- [ ] Container/presentational separation is clear

### ❌ Poor Decomposition Indicators

**Warning Signs**:
- [ ] Components over 300 lines
- [ ] Components with multiple responsibilities
- [ ] Duplicate UI patterns not extracted
- [ ] Inconsistent naming (Button, SubmitBtn, ActionButton for same thing)
- [ ] Too many props (> 10 indicates too much responsibility)
- [ ] Deep prop drilling (passing props through 3+ levels)
- [ ] Conditionals determining completely different UI (should be separate components)
- [ ] Hard to test (too many dependencies, too complex)
- [ ] Components mixing presentation and data fetching

---

## Framework-Agnostic Patterns

### Composition Pattern (Slots/Children)

**Concept**: Allow components to accept variable content

**React**:
```jsx
<Card>
  <CardHeader>Title</CardHeader>
  <CardContent>Content</CardContent>
</Card>
```

**Vue**:
```vue
<Card>
  <template #header>Title</template>
  <template #content>Content</template>
</Card>
```

**Svelte**:
```svelte
<Card>
  <svelte:fragment slot="header">Title</svelte:fragment>
  <svelte:fragment slot="content">Content</svelte:fragment>
</Card>
```

**Framework-Agnostic Description**:
> Component accepts named content areas (header, content, footer) that can be filled with arbitrary content.

---

### Props Interface Pattern

**Framework-Agnostic Props Definition**:

```typescript
// Define props interface (works for React, Vue, Svelte)
type ButtonProps = {
  variant: 'primary' | 'secondary' | 'danger'
  size: 'sm' | 'md' | 'lg'
  disabled: boolean
  loading: boolean
  onClick: () => void
  children: ReactNode // React
  // OR
  default: Slot // Vue
  // OR
  $$slots: { default: any } // Svelte
}
```

**Description**:
> Component accepts configuration via props/attributes. Props define appearance (variant, size), state (disabled, loading), behavior (onClick), and content (children/slots).

---

## Examples

### Example 1: Blog Post Page Decomposition

**Input**:
> Design a blog post page with header, post content, author bio, related posts, and comments.

**Analysis**:
- **Reusability Goal**: High (design system)
- **Context**: Marketing site blog
- **Complexity**: High (multiple sections)

**Output**:

**Component Hierarchy**:
```
BlogPostPage (Template)
├── PageHeader (Organism)
│   ├── Breadcrumb (Molecule)
│   │   └── BreadcrumbItem (Atom)
│   └── NavigationBar (Organism)
│       └── NavLink (Atom)
├── BlogPostContent (Organism)
│   ├── PostHeader (Molecule)
│   │   ├── PostTitle (Atom)
│   │   ├── PostMeta (Molecule)
│   │   │   ├── Avatar (Atom)
│   │   │   ├── AuthorName (Atom)
│   │   │   ├── PublishDate (Atom)
│   │   │   └── ReadingTime (Atom)
│   │   └── PostTags (Molecule)
│   │       └── Tag (Atom)
│   ├── FeaturedImage (Atom)
│   ├── PostBody (Organism)
│   │   └── RichTextContent (Atom)
│   └── PostFooter (Molecule)
│       ├── ShareButtons (Molecule)
│       │   └── SocialButton (Atom)
│       └── LikeButton (Atom)
├── AuthorBio (Organism)
│   ├── Avatar (Atom)
│   ├── AuthorName (Atom)
│   ├── AuthorDescription (Atom)
│   └── SocialLinks (Molecule)
│       └── SocialLink (Atom)
├── RelatedPosts (Organism)
│   ├── SectionHeading (Atom)
│   └── PostGrid (Organism)
│       └── PostCard (Molecule)
│           ├── PostImage (Atom)
│           ├── PostTitle (Atom)
│           ├── PostExcerpt (Atom)
│           └── ReadMoreLink (Atom)
└── CommentsSection (Organism)
    ├── CommentForm (Molecule)
    │   ├── Textarea (Atom)
    │   └── Button (Atom)
    └── CommentList (Organism)
        └── Comment (Molecule)
            ├── Avatar (Atom)
            ├── CommentHeader (Molecule)
            │   ├── AuthorName (Atom)
            │   └── CommentDate (Atom)
            ├── CommentText (Atom)
            └── CommentActions (Molecule)
                └── Button (Atom)
```

**Reusability Classification**:
- **High** (Design System): Avatar, Button, Tag, PostTitle, PostCard, Comment, Breadcrumb, NavigationBar
- **Medium** (Feature-level): AuthorBio, RelatedPosts, CommentsSection
- **Low** (Page-specific): BlogPostPage, BlogPostContent

**Extraction Recommendations**:
1. Extract PostCard as reusable component (use in blog index, related posts, search results)
2. Extract Comment component (use in comment threads, reviews, discussions)
3. Extract AuthorBio (use in blog posts, author pages, team pages)
4. Extract SocialLinks, ShareButtons (use across marketing site)

---

### Example 2: Dashboard Analytics Component

**Input**:
> Create an analytics dashboard showing metrics cards, charts, and recent activity.

**Analysis**:
- **Reusability Goal**: Medium (dashboard feature)
- **Context**: Admin dashboard
- **Complexity**: Medium

**Output**:

**Component Hierarchy**:
```
AnalyticsDashboard (Organism)
├── DashboardHeader (Molecule)
│   ├── PageTitle (Atom)
│   └── DateRangePicker (Molecule)
│       ├── DateInput (Atom)
│       └── Button (Atom)
├── MetricsGrid (Organism)
│   └── MetricCard (Molecule)
│       ├── MetricIcon (Atom)
│       ├── MetricLabel (Atom)
│       ├── MetricValue (Atom)
│       └── MetricChange (Molecule)
│           ├── ChangeIcon (Atom)
│           └── ChangeText (Atom)
├── ChartsSection (Organism)
│   ├── RevenueChart (Organism)
│   │   ├── ChartHeader (Molecule)
│   │   │   ├── ChartTitle (Atom)
│   │   │   └── ChartLegend (Molecule)
│   │   └── LineChart (Molecule) // Wrapper for chart library
│   └── TrafficChart (Organism)
│       ├── ChartHeader (Molecule)
│       └── BarChart (Molecule)
└── RecentActivityFeed (Organism)
    ├── FeedHeader (Molecule)
    │   └── SectionTitle (Atom)
    └── ActivityList (Organism)
        └── ActivityItem (Molecule)
            ├── ActivityIcon (Atom)
            ├── ActivityText (Atom)
            └── ActivityTime (Atom)
```

**Reusability Classification**:
- **High**: MetricCard, ActivityItem, ChartHeader, Button, DateInput
- **Medium**: DateRangePicker, RecentActivityFeed
- **Low**: AnalyticsDashboard, ChartsSection

**Container/Presentational Split**:
```
AnalyticsDashboardContainer (Smart)
├── Fetches analytics data
├── Manages date range state
└── Passes data to:
    └── AnalyticsDashboard (Dumb)
        └── Renders UI with received data
```

---

### Example 3: Form Component Extraction

**Input**:
> User registration form with multiple fields, validation, and error handling.

**Analysis**:
- **Complexity**: High (many fields, validation)
- **Pattern**: Repeated form field pattern

**Output**:

**Before Decomposition** (Monolithic):
```
RegisterForm (400 lines, multiple responsibilities)
├── Form layout
├── All input fields
├── Validation logic
├── Error display
├── Submit handling
└── Success/error states
```

**After Decomposition**:
```
RegisterForm (Organism) - 100 lines
├── FormField (Molecule) - Reusable ✅
│   ├── Label (Atom)
│   ├── Input (Atom)
│   ├── HelperText (Atom)
│   └── ErrorMessage (Atom)
├── FormField (email)
├── FormField (password)
├── PasswordStrengthMeter (Molecule) - Reusable ✅
│   └── ProgressBar (Atom)
├── FormField (confirm password)
├── TermsCheckbox (Molecule)
│   ├── Checkbox (Atom)
│   └── Label (Atom)
├── SubmitButton (Molecule)
│   ├── Button (Atom)
│   └── Spinner (Atom) - shown when loading
└── FormAlert (Molecule) - Reusable ✅
    ├── AlertIcon (Atom)
    └── AlertText (Atom)
```

**Extracted Reusable Components**:
1. **FormField** - Use in all forms (login, contact, checkout, etc.)
2. **PasswordStrengthMeter** - Use in password creation flows
3. **FormAlert** - Use for form-level success/error messages
4. **Input, Label, Checkbox, Button** - Use everywhere

**Benefits**:
- RegisterForm reduced from 400 to ~100 lines
- FormField reusable in 20+ forms
- Each component testable independently
- Consistent form styling across app

---

## Success Criteria

This skill is successfully applied when:

1. ✅ **Clear Hierarchy**: Component tree has logical structure (atoms → molecules → organisms → templates)
2. ✅ **Single Responsibility**: Each component has one clear purpose
3. ✅ **Appropriate Granularity**: Not too granular (dozens of tiny components), not too coarse (monolithic components)
4. ✅ **Reusability Identified**: Repeated patterns extracted as reusable components
5. ✅ **Composition Patterns**: Components use composition for flexibility
6. ✅ **Naming Consistency**: Components follow consistent naming conventions
7. ✅ **Framework-Agnostic**: Decomposition applies to any framework
8. ✅ **Container/Presentational Split**: Data/logic separated from presentation where appropriate
9. ✅ **Testability**: Each component can be tested independently
10. ✅ **Maintainability**: Changes are isolated to specific components

---

## Anti-Patterns to Avoid

### ❌ Over-Decomposition
**Problem**: Breaking components into too many tiny pieces
**Example**: Separate components for `FirstName`, `LastName` instead of `FullNameInput`
**Fix**: Keep related functionality together if they always appear together

### ❌ Under-Decomposition
**Problem**: Components doing too much
**Example**: Single `Dashboard` component with 800 lines
**Fix**: Extract sections, repeated patterns, and atomic elements

### ❌ Premature Abstraction
**Problem**: Creating reusable component after 1 use "just in case"
**Example**: Extracting `LoginHeaderLogo` used only on login page
**Fix**: Wait for 2nd or 3rd use before extracting (unless it's a common pattern like Button)

### ❌ Wrong Abstraction
**Problem**: Forcing different UI patterns into single component with many props/conditionals
**Example**: `GenericCard` with 15 props handling 5 different card types
**Fix**: Create separate components for substantially different variants

### ❌ Deep Prop Drilling
**Problem**: Passing props through 4+ levels
**Example**: `App` → `Page` → `Section` → `Widget` → `Button` (passing onClick)
**Fix**: Use composition (children), context, or state management

### ❌ Inconsistent Naming
**Problem**: Same concept named differently
**Example**: `UserCard`, `ProductBox`, `CommentPanel` (Card/Box/Panel are synonyms)
**Fix**: Choose one pattern (e.g., always use "Card") and be consistent

---

## Version History

- **1.0.0** (2024-01-15): Initial Component Decomposition Skill
  - Defined purpose and responsibility
  - Created 5 decision-making criteria
  - Documented 5 decomposition patterns
  - Provided 3 detailed examples
  - Established quality checks and success criteria
  - Identified anti-patterns to avoid
