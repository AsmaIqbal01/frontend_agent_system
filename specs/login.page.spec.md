# Login Page Specification

**Page Route:** `/auth/login`
**Parent Feature:** Authentication System (see `feature.spec.md`)
**Access Level:** Public (unauthenticated users only)
**Version:** 1.0.0

---

## Purpose

Enable users to authenticate and access protected areas of the application by providing their email and password credentials. Redirect authenticated users to their intended destination or default dashboard.

---

## Layout Structure

```
┌─────────────────────────────────────────┐
│  Header (Minimal - Logo, optional nav)  │
├─────────────────────────────────────────┤
│                                         │
│   ┌───────────────────────────────┐   │
│   │                               │   │
│   │     Login Card (Centered)     │   │
│   │                               │   │
│   │  - Page Title                 │   │
│   │  - Email Input                │   │
│   │  - Password Input             │   │
│   │  - Remember Me Checkbox       │   │
│   │  - Submit Button              │   │
│   │  - Forgot Password Link       │   │
│   │  - Register Link              │   │
│   │                               │   │
│   └───────────────────────────────┘   │
│                                         │
│                                         │
├─────────────────────────────────────────┤
│  Footer (Optional - Links, Copyright)   │
└─────────────────────────────────────────┘
```

### Layout Sections

**1. Header**
- Minimal branding (logo, app name)
- Optional: Link to home/landing page
- No authenticated user menu

**2. Main Content Area**
- Vertically and horizontally centered card
- Clean background (solid color or subtle pattern)
- Card max-width: 480px
- Card padding: Responsive (24px mobile, 32px desktop)

**3. Login Card**
- Page title: "Log In" or "Welcome Back"
- Optional subtitle/description
- Form section
- Divider or spacing
- Alternative action links (register, forgot password)

**4. Footer (Optional)**
- Terms of Service link
- Privacy Policy link
- Copyright notice
- Help/Support link

---

## Required Components

### Core Components

1. **LoginForm**
   - Email input field
   - Password input field with visibility toggle
   - Remember me checkbox
   - Submit button
   - Form validation
   - Loading state during submission

2. **FormInput** (reusable)
   - Label
   - Input field
   - Error message display
   - Icon support (email icon, lock icon)

3. **PasswordInput** (specialized input)
   - Password field
   - Show/hide password toggle button
   - Error message display

4. **Button** (primary action)
   - Submit button with loading spinner
   - Disabled state during form submission

5. **Link** (navigation)
   - Forgot password link
   - Register/sign up link
   - Return to home link

6. **Alert/Message** (feedback)
   - Error message display (form-level errors)
   - Success message display (if needed)

### Optional Components

7. **SocialLoginButtons** (future enhancement)
   - OAuth provider buttons (Google, GitHub, etc.)
   - Positioned below main form with divider

8. **AuthLayout** (wrapper)
   - Consistent layout for all auth pages
   - Shared header/footer
   - Background styling

---

## Page-Level State

### Authentication State
```
{
  isAuthenticated: boolean
  isLoading: boolean
  user: User | null
}
```

### Form State
```
{
  email: string
  password: string
  rememberMe: boolean
  errors: {
    email?: string
    password?: string
    form?: string
  }
  isSubmitting: boolean
  submitError: string | null
}
```

### Navigation State
```
{
  redirectTo: string | null  // Return URL after successful login
  from: string | null        // Previous page (for analytics)
}
```

### UI State
```
{
  showPassword: boolean
  focusedField: 'email' | 'password' | null
}
```

---

## Data-Fetching Strategy

### On Page Load

**Client-Side Check:**
1. Check if user is already authenticated (read from auth context/state)
2. If authenticated:
   - Redirect to `redirectTo` URL or default dashboard
   - No need to render login form
3. If not authenticated:
   - Render login form
   - Extract `redirectTo` from URL query params (e.g., `?redirectTo=/dashboard`)

**No Server-Side Data Fetching Required**
- Login page has no data dependencies
- All data comes from user input
- Authentication check is state-based, not API-based on load

### On Form Submission

**API Call:**
1. Validate form inputs client-side
2. If valid, POST to `/api/auth/login` with:
   ```json
   {
     "email": "user@example.com",
     "password": "password123",
     "rememberMe": false
   }
   ```
3. Handle response:
   - **Success (200):** Store token, update auth state, redirect
   - **Error (401):** Display "Invalid email or password"
   - **Error (429):** Display rate limit message with retry time
   - **Network Error:** Display "Connection failed. Please try again."

**State Updates:**
1. Set `isSubmitting: true` before API call
2. On success:
   - Store auth token (cookie or storage)
   - Update global auth state with user object
   - Navigate to `redirectTo` or `/dashboard`
3. On error:
   - Set `submitError` with error message
   - Set `isSubmitting: false`
   - Clear password field (security best practice)

---

## UX Behaviors

### Loading States

**Initial Page Load:**
- Show auth check loading spinner (if checking existing session)
- Duration: < 200ms typically
- Fallback: Show login form if check takes too long

**Form Submission:**
- Disable submit button
- Show loading spinner inside button
- Disable all form inputs
- Visual feedback: "Logging in..."
- Duration: Typically 500ms - 2s

**Auto-Redirect:**
- If already authenticated, show brief loading state
- Display message: "You're already logged in. Redirecting..."
- Redirect after 500ms delay

### Empty States

**Not Applicable**
- Login page has no empty state (form always rendered)

### Error States

**Form Validation Errors:**
- Display inline below each field
- Red text with error icon
- Errors shown on:
  - Field blur (real-time validation)
  - Form submission attempt
- Specific errors:
  - Email: "Please enter a valid email address"
  - Password: "Password is required"

**Authentication Errors:**
- Display at top of form (above inputs)
- Red background alert box
- Error messages:
  - Invalid credentials: "Invalid email or password"
  - Rate limited: "Too many attempts. Try again in X minutes."
  - Account locked: "Account temporarily locked. Reset your password or contact support."
  - Server error: "Something went wrong. Please try again."

**Network Errors:**
- Display in alert box
- Message: "Unable to connect. Check your internet connection."
- Provide retry button

### Success States

**Successful Login:**
- Optional: Brief success message (green checkmark)
- Message: "Login successful! Redirecting..."
- Immediate redirect (< 500ms)
- Clear form (security)

### Interactive Behaviors

**Password Visibility Toggle:**
- Default: Password hidden (type="password")
- Click eye icon: Toggle to visible (type="text")
- Icon changes: eye-slash when visible
- Tooltip: "Show password" / "Hide password"

**Remember Me:**
- Checkbox defaults to unchecked
- Label: "Keep me logged in"
- Tooltip/help text: "Stay signed in for 30 days"

**Keyboard Navigation:**
- Tab order: Email → Password → Remember Me → Submit → Links
- Enter key submits form from any input field
- Escape key clears focused field (optional)

**Auto-Focus:**
- Email field receives focus on page load
- Improves UX for keyboard users

**Link Behaviors:**
- "Forgot password?" → Navigate to `/auth/reset-password`
- "Don't have an account? Sign up" → Navigate to `/auth/register`
- "Return to home" → Navigate to `/` (landing page)

**Validation Timing:**
- Email: Validate on blur
- Password: Validate on submission only (avoid showing "required" immediately)
- Show success indicators (green checkmark) for valid fields

---

## Redirect Logic

### Pre-Login Redirect Capture

**Scenario:** User tries to access protected route while not authenticated

1. Protected route middleware catches unauthenticated request
2. Middleware redirects to `/auth/login?redirectTo=/protected-page`
3. Login page stores `redirectTo` in state
4. After successful login, redirect to stored URL

### Post-Login Redirect Priority

1. **Query param:** `redirectTo` from URL
2. **Default:** `/dashboard` or application default authenticated page
3. **Fallback:** `/` (home/landing)

### Redirect Validation

- Sanitize `redirectTo` to prevent open redirect vulnerabilities
- Only allow internal routes (same domain)
- Reject external URLs or suspicious patterns

---

## Accessibility Requirements

**Keyboard Support:**
- All inputs focusable via Tab
- Form submittable via Enter
- Skip link to main content

**Screen Reader:**
- Page title announced: "Log In"
- Form inputs have associated labels
- Error messages announced via `aria-live="polite"`
- Loading states announced: "Logging in, please wait"
- Submit button label: "Log In" (not just icon)

**ARIA Attributes:**
- `aria-label` on password toggle button: "Show password"
- `aria-describedby` linking inputs to error messages
- `aria-invalid="true"` on fields with errors
- `aria-busy="true"` on form during submission

**Focus Management:**
- Visible focus indicators (2px outline)
- Focus trapped in modal if used
- Focus moved to error message if validation fails

**Color Contrast:**
- Text: 4.5:1 minimum
- Error states: Not conveyed by color alone (icon + text)

---

## Performance Requirements

**Page Load:**
- Initial render: < 1s
- Time to interactive: < 2s
- No layout shifts during load

**Bundle Size:**
- Page-specific code: < 30KB
- Shared auth components: < 20KB

**Optimization:**
- Code split auth pages from main bundle
- Lazy load password visibility icon
- Preconnect to auth API domain

---

## Security Considerations

**Input Sanitization:**
- Sanitize email input to prevent XSS
- Password not logged or exposed in console

**Rate Limiting:**
- Client-side: Disable submit for 1 second after failed attempt
- Server-side: Enforce rate limits (handled by API)

**CSRF Protection:**
- Include CSRF token in form submission (if using cookies)

**Password Handling:**
- Never store password in state longer than submission
- Clear password field after error
- Use `autocomplete="current-password"` attribute

**Redirect Validation:**
- Validate `redirectTo` parameter to prevent open redirects
- Whitelist allowed redirect domains

---

## Analytics & Monitoring

**Page View Events:**
- Track page load
- Track referrer (where user came from)
- Track presence of `redirectTo` param

**User Interaction Events:**
- Form field interactions (focus, blur)
- Password visibility toggle
- Remember me checkbox toggle

**Conversion Events:**
- Login attempt (form submission)
- Login success
- Login failure (with error type)
- Link clicks (forgot password, register)

**Performance Metrics:**
- Page load time
- Form submission response time
- Error rate by type

---

## Edge Cases

**Already Authenticated:**
- User navigates to `/auth/login` while logged in
- Action: Auto-redirect to dashboard
- Display: Brief message "Already logged in"

**Expired Session:**
- User session expires while on protected page
- Redirect to login with `redirectTo` set
- Display: Message "Session expired. Please log in again."

**Invalid Redirect URL:**
- `redirectTo` contains external URL
- Action: Ignore param, use default redirect
- Security: Prevent open redirect attack

**Slow Network:**
- API call takes > 5 seconds
- Display: Extended loading state
- Provide: Cancel button or timeout message

**Browser Autofill:**
- Browser autofills email/password
- Trigger validation after autofill
- Ensure submit button enabled

---

## Dependencies

**Parent Feature:** Authentication System (`feature.spec.md`)

**Required Components:**
- FormInput
- PasswordInput
- Button
- Link
- Alert

**Required State:**
- Global auth context/state
- Form validation library

**Required APIs:**
- POST `/api/auth/login`

---

## Acceptance Criteria

- [ ] Page renders at `/auth/login` route
- [ ] Email and password inputs are present
- [ ] Remember me checkbox is functional
- [ ] Form validates email format
- [ ] Form requires both fields before submission
- [ ] Inline errors display on validation failure
- [ ] Password visibility toggle works correctly
- [ ] Submit button shows loading state during API call
- [ ] Successful login redirects to appropriate page
- [ ] Failed login shows error message without exposing which field is wrong
- [ ] "Forgot password" link navigates to reset page
- [ ] "Sign up" link navigates to registration page
- [ ] Already authenticated users auto-redirect to dashboard
- [ ] `redirectTo` query param is respected and validated
- [ ] Page is keyboard navigable
- [ ] Screen reader announces all state changes
- [ ] Page meets WCAG AA contrast requirements
- [ ] Page loads in < 1.5s on 3G network
- [ ] Form submission completes in < 2s under normal conditions

---

**Related Specifications:**
- Feature: `feature.spec.md` (Authentication System)
- Page: `register.page.spec.md` (Registration Page)
- Page: `reset-password.page.spec.md` (Password Reset)
