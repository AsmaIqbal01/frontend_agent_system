# Shopping Cart Page Specification

**Page Route:** `/cart`
**Access Level:** Public (accessible to all users, persists across sessions)
**Version:** 1.0.0
**Use Case:** E-commerce / Product Shopping

---

## Purpose

Display items added to the shopping cart, allow users to review selections, modify quantities, remove items, apply discount codes, view pricing breakdown, and proceed to checkout. Serve as the intermediate step between browsing and purchasing.

---

## Layout Structure

```
┌─────────────────────────────────────────────────────┐
│  Header / Navigation                                │
│  (Logo, Nav, Cart Badge, Account)                   │
├─────────────────────────────────────────────────────┤
│                                                     │
│  Page Header                                        │
│  (Title: "Shopping Cart", Item Count)               │
│                                                     │
├──────────────────────────────┬──────────────────────┤
│                              │                      │
│  Cart Items List             │  Order Summary       │
│                              │                      │
│  ┌────────────────────────┐ │  - Subtotal          │
│  │ Cart Item 1            │ │  - Shipping          │
│  │ - Image, Name, Price   │ │  - Tax               │
│  │ - Quantity controls    │ │  - Discount          │
│  │ - Remove button        │ │  - Total             │
│  └────────────────────────┘ │                      │
│                              │  - Promo Code        │
│  ┌────────────────────────┐ │  - Checkout Button   │
│  │ Cart Item 2            │ │                      │
│  └────────────────────────┘ │                      │
│                              │                      │
│  Continue Shopping Link      │                      │
│                              │                      │
├──────────────────────────────┴──────────────────────┤
│  Footer                                             │
└─────────────────────────────────────────────────────┘
```

### Layout Sections

**1. Header/Navigation**
- Standard site header
- Cart badge showing item count
- User account menu

**2. Page Header**
- Title: "Shopping Cart" or "Your Cart"
- Item count: "X items" or "X products in your cart"
- Breadcrumb (optional): Home > Cart

**3. Two-Column Layout (Responsive)**

**Left Column (Main Content):**
- Cart items list
- Each cart item shows:
  - Product image (thumbnail)
  - Product name (link to product page)
  - Product variant info (size, color, etc.)
  - Unit price
  - Quantity selector
  - Subtotal (quantity × price)
  - Remove button
- Empty cart state (if no items)
- "Continue Shopping" link

**Right Column (Sidebar):**
- Order summary card
- Subtotal breakdown
- Shipping estimation
- Tax estimation
- Discount/promo code input
- Total
- Checkout button
- Security badges/trust indicators

**4. Footer**
- Standard site footer

---

## Required Components

### Cart Item Components

1. **CartItemList**
   - Container for all cart items
   - Iterates over cart items
   - Shows empty state if cart is empty

2. **CartItem**
   - Product image (thumbnail, linked)
   - Product name (linked to product page)
   - Variant information (size, color, etc.)
   - Unit price display
   - Quantity selector
   - Item subtotal
   - Remove button
   - Stock status indicator (optional)

3. **QuantitySelector**
   - Decrement button (-)
   - Quantity input/display
   - Increment button (+)
   - Min: 1, Max: Stock availability
   - Disabled state during updates

4. **RemoveButton**
   - Icon button or text link
   - Confirmation modal (optional)
   - Loading state during removal

### Summary Components

5. **OrderSummary**
   - Subtotal display
   - Shipping display
   - Tax display
   - Discount display (if applied)
   - Total display (prominent)
   - All amounts formatted as currency

6. **PromoCodeInput**
   - Input field for promo/discount code
   - Apply button
   - Success message when valid
   - Error message when invalid
   - Display discount amount if applied
   - Remove promo code option

7. **CheckoutButton**
   - Primary CTA button
   - Text: "Proceed to Checkout" or "Checkout"
   - Disabled if cart empty
   - Loading state (if needed)
   - Shows item count or total (optional)

### State Components

8. **EmptyCartState**
   - Empty cart icon/illustration
   - Message: "Your cart is empty"
   - Suggestions: "Continue shopping" button
   - Optional: Recently viewed items

9. **LoadingState**
   - Skeleton loaders for cart items
   - Loading spinner

10. **ErrorState**
    - Error message display
    - Retry button

---

## Page-Level State

### Cart State
```
{
  items: Array<{
    id: string
    productId: string
    name: string
    image: string
    price: number
    quantity: number
    variant?: {
      size?: string
      color?: string
      [key: string]: any
    }
    stock?: number
    maxQuantity?: number
  }>
  itemCount: number
  subtotal: number
  shipping: number
  tax: number
  discount: number
  total: number
  promoCode: string | null
  promoCodeDiscount: number
}
```

### UI State
```
{
  isLoading: boolean
  loadingItems: Set<string>     // Items being updated
  removingItems: Set<string>    // Items being removed
  updatingQuantity: {
    [itemId: string]: boolean
  }
  applyingPromoCode: boolean
  errors: {
    [itemId: string]: string
    promoCode?: string
    general?: string
  }
}
```

### Optional: User State (if authenticated)
```
{
  isAuthenticated: boolean
  userId: string | null
}
```

---

## Data-Fetching Strategy

### On Page Load

**Cart Data Retrieval:**

**Option A: Local Storage (Guest Users):**
1. Read cart from localStorage on client
2. Parse cart items
3. Validate items (check if still available)
4. Calculate totals

**Option B: API (Authenticated Users):**
1. Fetch cart from API on page load
2. GET `/api/cart` with authentication
3. Response includes items, quantities, prices
4. Calculate or receive totals from API

**Option C: Hybrid:**
1. Check authentication status
2. If authenticated: Fetch from API
3. If guest: Read from localStorage
4. Merge carts if user logs in (separate flow)

**Price/Stock Validation:**
- Validate current prices (may have changed since adding to cart)
- Check stock availability
- Update cart if items unavailable or out of stock
- Show warnings if prices changed

### On Cart Modifications

**Update Quantity:**
1. User changes quantity
2. Optimistic update (update UI immediately)
3. Send API request: PATCH `/api/cart/items/${itemId}` with new quantity
4. On success: Confirm update, recalculate totals
5. On failure: Revert to previous quantity, show error

**Remove Item:**
1. User clicks remove
2. Optional: Show confirmation modal
3. Optimistic removal (remove from UI)
4. Send API request: DELETE `/api/cart/items/${itemId}`
5. On success: Update totals
6. On failure: Restore item, show error

**Apply Promo Code:**
1. User enters promo code and clicks apply
2. Send API request: POST `/api/cart/promo` with code
3. On success:
   - Show discount amount
   - Update totals
   - Display success message
4. On failure:
   - Show error: "Invalid promo code" or "Expired code"

---

## UX Behaviors

### Loading States

**Initial Page Load:**
- Show skeleton loaders for cart items
- Show placeholder for order summary
- Duration: < 1s typically

**Quantity Update:**
- Disable quantity selector for that item
- Show mini spinner next to quantity
- Disable remove button for that item
- Duration: 200-500ms

**Item Removal:**
- Show loading spinner on remove button
- Fade out cart item (optional)
- Duration: 200-500ms
- Animate other items moving up

**Promo Code Application:**
- Disable apply button
- Show loading spinner in button
- Disable promo code input
- Duration: 500ms - 1s

### Empty States

**Empty Cart:**
- Hide cart items list
- Hide order summary
- Show empty cart illustration
- Message: "Your shopping cart is empty"
- Suggestions:
  - "Continue Shopping" button → Browse products
  - Recently viewed items (optional)
  - Popular products (optional)

**No Shipping Available:**
- Show warning in order summary
- Message: "Shipping not available to your location"
- Provide contact support option

### Error States

**Item Update Errors:**
- Quantity exceeds stock: "Only X items available"
- Item no longer available: "This item is no longer available" + auto-remove
- Network error: "Unable to update. Please try again."
- Display inline below the cart item

**Promo Code Errors:**
- Invalid code: "This promo code is invalid"
- Expired code: "This promo code has expired"
- Minimum purchase: "Add $X more to use this code"
- Already applied: "Promo code already applied"
- Display below promo code input

**General Errors:**
- API failure: "Unable to load cart. Please refresh."
- Show retry button

### Success States

**Quantity Updated:**
- Brief success indicator (green checkmark)
- Fade in/out animation
- Update totals smoothly

**Item Removed:**
- Success message (toast): "Item removed from cart"
- Undo option (optional, 5-second window)
- Update cart count in header

**Promo Code Applied:**
- Success message: "Promo code applied!"
- Show discount amount in green
- Display promo code with remove option
- Update totals to reflect discount

### Interactive Behaviors

**Quantity Selector:**
- Click increment: Increase quantity by 1
- Click decrement: Decrease quantity by 1 (minimum 1)
- Direct input: Allow typing quantity
- Blur: Validate and update
- Constraints:
  - Minimum: 1
  - Maximum: Stock available (or defined limit like 10)
- If quantity > stock: Show error, set to max available

**Remove Item:**
- Click remove button
- Optional: Show confirmation modal
  - "Remove [Product Name] from cart?"
  - "Cancel" / "Remove" buttons
- On confirm: Remove item with animation
- Optional: Undo action (toast with undo button)

**Product Image/Name Click:**
- Navigate to product detail page
- Open in same window (default) or new tab (user preference)

**Continue Shopping:**
- Navigate back to products page or homepage
- Preserve cart state

**Promo Code:**
- Enter code in input field
- Click "Apply" button or press Enter
- Code validated on server
- Success: Apply discount
- Error: Show error message

**Checkout Button:**
- Navigate to `/checkout` page
- Pass cart data to checkout flow
- If not authenticated: Prompt login/guest checkout

**Responsive Behavior:**
- Desktop (> 1024px): Two-column layout
- Tablet (640-1024px): Two-column with adjusted spacing
- Mobile (< 640px): Single column, order summary below cart items

---

## Redirect Logic

**Empty Cart:**
- No automatic redirect
- User can click "Continue Shopping" manually

**Checkout Button Click:**
- If authenticated: Redirect to `/checkout`
- If not authenticated:
  - Option A: Redirect to `/auth/login?redirectTo=/checkout`
  - Option B: Allow guest checkout, redirect to `/checkout`
  - Preserve cart data across redirect

**Product Removed (all items):**
- Cart becomes empty
- Show empty cart state
- No automatic redirect

---

## Accessibility Requirements

**Keyboard Navigation:**
- All interactive elements keyboard accessible
- Tab order: Cart items → Quantity selectors → Remove buttons → Promo input → Checkout
- Quantity: Increment/decrement with arrow keys
- Remove: Activate with Enter/Space

**Screen Reader:**
- Page title announced: "Shopping Cart, X items"
- Each cart item announced with product name, price, quantity
- Quantity changes announced: "Quantity updated to X"
- Item removal announced: "Product removed from cart"
- Promo code application announced
- Total updates announced

**ARIA Attributes:**
- Cart item list: `role="list"`, items: `role="listitem"`
- Quantity selector: `aria-label="Quantity for [Product Name]"`
- Remove button: `aria-label="Remove [Product Name] from cart"`
- Loading states: `aria-busy="true"`, `aria-live="polite"`
- Order summary updates: `aria-live="polite"`
- Empty cart: `role="status"`

**Focus Management:**
- After item removal: Focus on next item or "Continue Shopping"
- After promo applied: Focus on order summary or checkout button
- After error: Focus on error message

**Color Contrast:**
- All text meets 4.5:1 ratio
- Price and total prominent and readable
- Error messages high contrast

---

## Performance Requirements

**Page Load:**
- First Contentful Paint: < 1.5s
- Time to Interactive: < 2.5s
- Cart data loaded: < 1s

**Bundle Size:**
- Page code: < 60KB
- Images optimized (thumbnails < 20KB each)

**Optimization:**
- Server-side rendering for initial cart state
- Image lazy loading (below fold items)
- Debounce quantity input (wait 500ms before API call)
- Optimistic updates for better perceived performance
- Cache cart data (5-minute TTL)

**Real-Time Updates:**
- Poll for cart updates every 30 seconds (if shared across devices)
- Or use WebSockets for real-time sync

---

## Security Considerations

**Cart Tampering Prevention:**
- Validate all prices on server
- Never trust client-side pricing
- Recalculate totals server-side
- Check stock availability on server

**Authentication:**
- Secure cart API endpoints
- Validate user owns cart (if authenticated)
- Use session tokens or JWT

**Input Validation:**
- Validate quantity (positive integer, within limits)
- Sanitize promo codes
- Prevent SQL injection in promo code lookup

**Rate Limiting:**
- Limit cart updates per user/IP
- Prevent abuse of promo code checks

**CSRF Protection:**
- Include CSRF tokens on cart modification requests

---

## Analytics & Monitoring

**Page View Events:**
- Track cart page view
- Track cart value (total amount)
- Track item count

**User Interaction Events:**
- Quantity changes (increase/decrease)
- Item removals
- Promo code applications (success/failure)
- Checkout button clicks
- Continue shopping clicks
- Product clicks from cart

**Conversion Events:**
- Cart abandonment (leave page without checkout)
- Proceed to checkout
- Average cart value
- Items per cart

**Cart Abandonment Tracking:**
- Time spent on cart page
- Exit points (where users leave)
- Recovery: Email reminders (if authenticated)

**Performance Metrics:**
- Page load time
- API response times
- Error rates by type

---

## Edge Cases

**Item Out of Stock:**
- Show warning: "This item is no longer available"
- Disable quantity changes
- Provide remove option
- Suggest alternatives (optional)

**Price Changed:**
- Show notification: "Price updated since adding to cart"
- Display old price (strikethrough) and new price
- Auto-update cart total

**Quantity Exceeds Stock:**
- Automatically adjust to max available
- Show message: "Only X items available, quantity adjusted"

**Session Expired (Authenticated):**
- Prompt re-login
- Preserve cart after login

**Cart Sync (Guest → Authenticated):**
- User adds items as guest
- User logs in
- Merge guest cart with user's existing cart
- Handle duplicates (combine quantities or keep separate)

**Very Large Cart:**
- Many items (50+): Paginate or use virtual scrolling
- Performance: Lazy render items below fold

**Invalid Promo Code:**
- Show specific error:
  - "Code expired"
  - "Minimum purchase $X required"
  - "Not valid for items in your cart"

**Checkout Button Disabled:**
- Cart empty: Disable with tooltip "Add items to checkout"
- Items unavailable: Disable with message "Remove unavailable items"

**Network Failure:**
- Show offline message
- Queue updates (when connection restored)
- Or show error with retry

---

## Dependencies

**Required Components:**
- CartItemList
- CartItem
- QuantitySelector
- OrderSummary
- PromoCodeInput
- CheckoutButton
- EmptyCartState

**Required APIs:**
- GET `/api/cart` - Fetch cart
- PATCH `/api/cart/items/${itemId}` - Update quantity
- DELETE `/api/cart/items/${itemId}` - Remove item
- POST `/api/cart/promo` - Apply promo code
- DELETE `/api/cart/promo` - Remove promo code

**Optional Services:**
- Tax calculation service
- Shipping calculation service
- Inventory management system

**State Management:**
- Global cart state (Context, Redux, Zustand)
- Persisted in localStorage (guest users)
- Synced with backend (authenticated users)

---

## Acceptance Criteria

- [ ] Page renders at `/cart` route
- [ ] Cart items display with image, name, price, quantity
- [ ] Quantity selector allows increment/decrement
- [ ] Quantity updates persist and recalculate totals
- [ ] Remove button removes item from cart
- [ ] Removed items update cart count and totals
- [ ] Order summary shows subtotal, shipping, tax, total
- [ ] Promo code input accepts and validates codes
- [ ] Valid promo code applies discount and updates total
- [ ] Invalid promo code shows appropriate error
- [ ] Checkout button navigates to checkout page
- [ ] Checkout button disabled when cart is empty
- [ ] Empty cart state displays when no items
- [ ] "Continue Shopping" link navigates to products
- [ ] Cart item images link to product pages
- [ ] Cart item names link to product pages
- [ ] Loading states show during API operations
- [ ] Optimistic updates for quantity changes
- [ ] Error messages display for failed operations
- [ ] Stock availability validated and enforced
- [ ] Prices validated against current server prices
- [ ] Cart count in header updates on changes
- [ ] Page is keyboard navigable
- [ ] Screen reader announces all cart operations
- [ ] Cart updates announced to assistive tech
- [ ] Page meets WCAG AA requirements
- [ ] Page loads within 2.5 seconds
- [ ] Cart operations complete within 1 second
- [ ] Mobile responsive (works at 320px width)
- [ ] Two-column layout on desktop
- [ ] Single-column layout on mobile

---

**Related Specifications:**
- Page: `/checkout` (Checkout flow)
- Page: `/products` (Product listing)
- Component: CartItem
- Component: QuantitySelector
- Component: OrderSummary
- API: Cart Management APIs
