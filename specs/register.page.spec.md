# Registration Page Specification

**Page Route:** `/auth/register`
**Parent Feature:** Authentication System (see `feature.spec.md`)
**Access Level:** Public (unauthenticated users only)
**Version:** 1.0.0

---

## Purpose

Enable new users to create an account by providing email, password, and accepting terms of service. Guide users through account creation with clear validation feedback and redirect to appropriate destination upon successful registration.

---

## Layout Structure

```
┌─────────────────────────────────────────┐
│  Header (Minimal - Logo, optional nav)  │
├─────────────────────────────────────────┤
│                                         │
│   ┌───────────────────────────────┐   │
│   │                               │   │
│   │  Registration Card (Centered) │   │
│   │                               │   │
│   │  - Page Title                 │   │
│   │  - Email Input                │   │
│   │  - Password Input             │   │
│   │  - Password Strength Meter    │   │
│   │  - Confirm Password Input     │   │
│   │  - Terms Checkbox             │   │
│   │  - Submit Button              │   │
│   │  - Login Link                 │   │
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
- Clean background
- Card max-width: 480px
- Card padding: Responsive (24px mobile, 32px desktop)

**3. Registration Card**
- Page title: "Create Account" or "Sign Up"
- Optional subtitle: "Join us today"
- Form section
- Alternative action link (existing account → login)

**4. Footer (Optional)**
- Terms of Service link
- Privacy Policy link
- Copyright notice

---

## Required Components

### Core Components

1. **RegisterForm**
   - Email input field
   - Password input field with visibility toggle
   - Password strength indicator
   - Confirm password input field
   - Terms of service checkbox
   - Submit button
   - Form validation
   - Loading state during submission

2. **FormInput** (reusable)
   - Label
   - Input field
   - Error message display
   - Success indicator (green checkmark)

3. **PasswordInput** (specialized)
   - Password field
   - Show/hide password toggle
   - Error message display

4. **PasswordStrengthMeter**
   - Visual strength indicator (bar or segments)
   - Strength label (Weak/Medium/Strong)
   - Color coding (red/yellow/green)
   - Password requirement checklist

5. **Checkbox** (terms acceptance)
   - Checkbox input
   - Label with embedded links
   - Error state if not checked

6. **Button** (primary action)
   - Submit button with loading spinner
   - Disabled state during submission or if terms not accepted

7. **Alert/Message** (feedback)
   - Error message display (form-level errors)
   - Success message display

### Optional Components

8. **PasswordRequirements** (helper)
   - List of password criteria
   - Visual indicators (checkmark/X) for each met/unmet requirement
   - Auto-updates as user types

9. **SocialSignupButtons** (future enhancement)
   - OAuth provider buttons
   - Positioned below main form with divider

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
  confirmPassword: string
  acceptedTerms: boolean
  errors: {
    email?: string
    password?: string
    confirmPassword?: string
    terms?: string
    form?: string
  }
  isSubmitting: boolean
  submitError: string | null
}
```

### Password Validation State
```
{
  strength: 'weak' | 'medium' | 'strong'
  score: number (0-4)
  requirements: {
    minLength: boolean      // 8+ characters
    hasUppercase: boolean   // A-Z
    hasLowercase: boolean   // a-z
    hasNumber: boolean      // 0-9
    hasSpecial: boolean     // !@#$% etc (optional)
  }
  feedback: string[]        // Suggestions from strength library
}
```

### UI State
```
{
  showPassword: boolean
  showConfirmPassword: boolean
  focusedField: 'email' | 'password' | 'confirmPassword' | null
}
```

---

## Data-Fetching Strategy

### On Page Load

**Client-Side Check:**
1. Check if user is already authenticated
2. If authenticated:
   - Redirect to dashboard
   - Display: "You already have an account"
3. If not authenticated:
   - Render registration form
   - No data fetching required

**No Server-Side Data Fetching**
- Registration page has no data dependencies
- All data comes from user input

### On Form Submission

**API Call:**
1. Validate all form inputs client-side
2. If valid, POST to `/api/auth/register` with:
   ```json
   {
     "email": "user@example.com",
     "password": "SecurePass123"
   }
   ```
3. Handle response:
   - **Success (201):** Store token (if auto-login), redirect
   - **Error (409):** "User already exists" → suggest login
   - **Error (400):** Display validation error
   - **Network Error:** Display connection error

**State Updates:**
1. Set `isSubmitting: true` before API call
2. On success:
   - Option A (Auto-login): Store token, update auth state, redirect to dashboard
   - Option B (Manual login): Redirect to login with success message
3. On error:
   - Set `submitError` with error message
   - Set `isSubmitting: false`
   - Clear password fields (security)

---

## UX Behaviors

### Loading States

**Initial Page Load:**
- Show auth check spinner (if checking existing session)
- Duration: < 200ms
- Fallback: Show form if check takes too long

**Form Submission:**
- Disable submit button
- Show loading spinner in button
- Disable all form inputs
- Visual feedback: "Creating account..."
- Duration: Typically 500ms - 2s

**Auto-Redirect:**
- If already authenticated, brief loading state
- Message: "You already have an account. Redirecting..."
- Redirect after 500ms

### Empty States

**Not Applicable**
- Registration form always rendered

### Error States

**Field Validation Errors:**
- Display inline below each field
- Red text with error icon
- Specific errors:
  - Email (invalid format): "Please enter a valid email address"
  - Email (empty): "Email is required"
  - Password (too weak): "Password does not meet requirements"
  - Confirm password (mismatch): "Passwords do not match"
  - Terms (not accepted): "You must accept the terms of service"

**Registration Errors:**
- Display at top of form
- Red background alert
- Error messages:
  - Email exists: "An account with this email already exists. Try logging in instead."
  - Server error: "Unable to create account. Please try again."
  - Network error: "Connection failed. Check your internet connection."

### Success States

**Successful Registration:**
- Option A (Auto-login):
  - Success message: "Account created! Redirecting..."
  - Redirect to dashboard
- Option B (Manual login):
  - Success message: "Account created! Please log in."
  - Redirect to login page

### Interactive Behaviors

**Password Strength Indicator:**
- Updates in real-time as user types
- Visual bar fills based on strength
- Color changes: red (weak) → yellow (medium) → green (strong)
- Label updates: "Weak" / "Fair" / "Good" / "Strong"
- Requirements checklist shows which criteria are met

**Password Visibility Toggles:**
- Separate toggle for password and confirm password fields
- Eye icon changes when password visible
- Tooltip: "Show password" / "Hide password"

**Password Matching:**
- Real-time validation when confirm password field loses focus
- Show green checkmark if passwords match
- Show red X if passwords don't match
- Error message appears immediately on mismatch

**Terms Checkbox:**
- Required before form can be submitted
- Submit button disabled until checked
- Links in label open in new tab (Terms, Privacy Policy)
- Label: "I agree to the Terms of Service and Privacy Policy"

**Keyboard Navigation:**
- Tab order: Email → Password → Confirm Password → Terms → Submit → Links
- Enter key submits form (if valid)
- Escape clears focused field (optional)

**Auto-Focus:**
- Email field receives focus on page load

**Validation Timing:**
- Email: Validate format on blur
- Password: Validate strength on input (real-time)
- Confirm Password: Validate match on blur
- Terms: Validate on submit or checkbox change

---

## Redirect Logic

### Pre-Registration Redirect

**Not Applicable**
- Users typically navigate directly to registration
- No protected route redirect scenario

### Post-Registration Redirect

**Auto-Login Enabled:**
1. Store authentication token
2. Update global auth state
3. Redirect to `/dashboard` or onboarding flow

**Manual Login Required:**
1. Redirect to `/auth/login`
2. Display success message: "Account created! Please log in."
3. Optionally pre-fill email in login form

---

## Accessibility Requirements

**Keyboard Support:**
- All inputs focusable via Tab
- Form submittable via Enter
- Checkbox toggleable via Space

**Screen Reader:**
- Page title announced: "Create Account" or "Sign Up"
- Form inputs have associated labels
- Password requirements announced as met
- Error messages announced via `aria-live="polite"`
- Loading states announced
- Password strength announced on change

**ARIA Attributes:**
- `aria-label` on password toggle: "Show password"
- `aria-describedby` linking inputs to errors and hints
- `aria-invalid="true"` on fields with errors
- `aria-required="true"` on required fields
- `aria-busy="true"` during submission
- Password strength meter has `role="status"`

**Focus Management:**
- Visible focus indicators
- Focus moved to first error on validation failure

**Color Contrast:**
- Text: 4.5:1 minimum
- Password strength colors meet contrast requirements
- Error states not conveyed by color alone

---

## Performance Requirements

**Page Load:**
- Initial render: < 1s
- Time to interactive: < 2s

**Bundle Size:**
- Page-specific code: < 35KB (includes password strength library)
- Shared auth components: < 20KB

**Optimization:**
- Lazy load password strength library (zxcvbn) on password field focus
- Code split from main bundle
- Debounce password strength calculation (300ms)

---

## Security Considerations

**Input Sanitization:**
- Sanitize email to prevent XSS
- Password never logged or exposed

**Password Requirements:**
- Minimum 8 characters
- At least one uppercase letter
- At least one lowercase letter
- At least one number
- Optional: Special character

**Password Strength:**
- Use library (zxcvbn) for accurate strength measurement
- Reject commonly used passwords
- Prevent password hints in error messages

**CSRF Protection:**
- Include CSRF token if using cookie-based sessions

**Rate Limiting:**
- Client-side: Prevent rapid repeated submissions
- Server-side: Limit registration attempts per IP

**Email Validation:**
- Client-side format validation
- Server-side: Check for existing user
- Optional: Email verification (out of scope for this page)

---

## Analytics & Monitoring

**Page View Events:**
- Track page load
- Track referrer

**User Interaction Events:**
- Form field interactions
- Password visibility toggle
- Terms checkbox toggle
- Password strength changes

**Conversion Events:**
- Registration attempt (form submission)
- Registration success
- Registration failure (with error type)
- Link clicks (login, terms, privacy)

**Performance Metrics:**
- Page load time
- Form submission response time
- Error rate by type
- Drop-off point (which field users abandon)

---

## Edge Cases

**Already Authenticated:**
- User navigates to `/auth/register` while logged in
- Action: Auto-redirect to dashboard
- Display: "You already have an account"

**Email Already Exists:**
- API returns 409 conflict
- Display: "Account with this email exists"
- Provide: Link to login page
- Provide: Link to password reset (in case user forgot)

**Weak Password Acceptance:**
- User submits with weak password that meets minimum requirements
- Option A: Block submission, require stronger password
- Option B: Show warning, allow submission
- Recommended: Option A for better security

**Browser Autofill:**
- Browser autofills password
- Trigger: Password strength calculation
- Update: Requirements checklist and meter

**Very Long Email/Password:**
- Input exceeds reasonable length
- Action: Truncate or show error
- Limit: Email 254 chars, Password 128 chars

**Special Characters in Password:**
- Allow all special characters
- Ensure proper encoding in API request
- No client-side filtering

---

## Dependencies

**Parent Feature:** Authentication System (`feature.spec.md`)

**Required Components:**
- FormInput
- PasswordInput
- PasswordStrengthMeter
- Checkbox
- Button
- Alert

**Required Libraries:**
- Password strength calculator (zxcvbn or similar)
- Form validation library

**Required APIs:**
- POST `/api/auth/register`

---

## Acceptance Criteria

- [ ] Page renders at `/auth/register` route
- [ ] Email, password, and confirm password inputs are present
- [ ] Password strength meter displays and updates in real-time
- [ ] Password requirements checklist shows met/unmet criteria
- [ ] Confirm password validates match on blur
- [ ] Terms of service checkbox is required
- [ ] Submit button disabled until all validations pass
- [ ] Form validates all fields before submission
- [ ] Password visibility toggles work for both password fields
- [ ] Inline errors display on validation failure
- [ ] Successful registration creates account
- [ ] Duplicate email shows appropriate error with login link
- [ ] Submit button shows loading state during API call
- [ ] Post-registration redirect works correctly
- [ ] "Already have account" link navigates to login
- [ ] Already authenticated users auto-redirect
- [ ] Page is keyboard navigable
- [ ] Screen reader announces password strength changes
- [ ] Password requirements announced as user types
- [ ] Page meets WCAG AA requirements
- [ ] Password strength library loads within 500ms of field focus

---

**Related Specifications:**
- Feature: `feature.spec.md` (Authentication System)
- Page: `login.page.spec.md` (Login Page)
- Page: `reset-password.page.spec.md` (Password Reset)
