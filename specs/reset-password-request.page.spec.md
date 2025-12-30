# Password Reset Request Page Specification

**Page Route:** `/auth/reset-password`
**Parent Feature:** Authentication System (see `feature.spec.md`)
**Access Level:** Public
**Version:** 1.0.0

---

## Purpose

Enable users who have forgotten their password to request a password reset link via email. Provide clear instructions and feedback while maintaining security by not revealing whether an email exists in the system.

---

## Layout Structure

```
┌─────────────────────────────────────────┐
│  Header (Minimal - Logo, optional nav)  │
├─────────────────────────────────────────┤
│                                         │
│   ┌───────────────────────────────┐   │
│   │                               │   │
│   │ Password Reset Card (Centered)│   │
│   │                               │   │
│   │  - Page Title                 │   │
│   │  - Instructions Text          │   │
│   │  - Email Input                │   │
│   │  - Submit Button              │   │
│   │  - Back to Login Link         │   │
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

**2. Main Content Area**
- Vertically and horizontally centered card
- Clean background
- Card max-width: 480px
- Card padding: Responsive

**3. Reset Request Card**
- Page title: "Reset Password" or "Forgot Password?"
- Instructional text explaining the process
- Form section (single email input)
- Alternative actions (back to login)

**4. Footer (Optional)**
- Help/Support link
- Copyright notice

---

## Required Components

### Core Components

1. **ResetRequestForm**
   - Email input field
   - Submit button
   - Form validation
   - Loading state during submission
   - Success/error message display

2. **FormInput** (reusable)
   - Label
   - Input field
   - Error message display
   - Icon (email icon)

3. **Button** (primary action)
   - Submit button with loading spinner
   - Text: "Send Reset Link"

4. **Alert/Message** (feedback)
   - Success message after submission
   - Error message for network issues
   - Info message with instructions

5. **Link** (navigation)
   - Back to login link
   - Help/contact support link

### Layout Components

6. **AuthLayout** (wrapper)
   - Consistent layout for auth pages
   - Shared header/footer

---

## Page-Level State

### Form State
```
{
  email: string
  errors: {
    email?: string
    form?: string
  }
  isSubmitting: boolean
  submitSuccess: boolean
  submitError: string | null
}
```

### UI State
```
{
  showSuccessMessage: boolean
  focusedField: 'email' | null
}
```

---

## Data-Fetching Strategy

### On Page Load

**No Data Fetching Required**
- Page renders form immediately
- No authentication check needed (page is public)
- No user data required

### On Form Submission

**API Call:**
1. Validate email format client-side
2. If valid, POST to `/api/auth/reset-password/request` with:
   ```json
   {
     "email": "user@example.com"
   }
   ```
3. Handle response:
   - **Success (200):** Always returns success (security best practice)
   - **Error (network):** Display connection error
   - **No 404 returned:** Prevents email enumeration

**Response Handling:**
- API always returns success whether email exists or not
- Display same success message regardless
- This prevents attackers from discovering valid emails

**State Updates:**
1. Set `isSubmitting: true` before API call
2. On completion (success or not):
   - Set `submitSuccess: true`
   - Show success message
   - Clear form or keep email (design decision)
   - Set `isSubmitting: false`
3. On network error only:
   - Show generic error: "Unable to process request"
   - Set `isSubmitting: false`

---

## UX Behaviors

### Loading States

**Form Submission:**
- Disable submit button
- Show loading spinner in button
- Disable email input
- Visual feedback: "Sending reset link..."
- Duration: Typically 500ms - 2s

### Empty States

**Not Applicable**
- Form always rendered

### Error States

**Field Validation:**
- Email (invalid format): "Please enter a valid email address"
- Email (empty): "Email is required"
- Display inline below email field
- Red text with error icon

**Network Errors:**
- Display at top of form
- Message: "Unable to send reset link. Please try again."
- Provide retry option (button stays enabled)

**No Account-Specific Errors:**
- Never display "Email not found" (security)
- Always show success message regardless of email existence

### Success States

**Reset Link Sent (Always Displayed):**
- Replace form with success message OR show message above form
- Message content:
  ```
  Reset Link Sent

  If an account exists with this email address, you will receive
  a password reset link shortly. Please check your inbox and spam folder.

  The link will expire in 1 hour.
  ```
- Show green checkmark icon
- Provide option: "Didn't receive email? Try again"
- Provide option: "Return to login"

### Interactive Behaviors

**Email Input:**
- Auto-focus on page load
- Validate format on blur
- Show green checkmark if valid
- Placeholder: "your@email.com"

**Keyboard Navigation:**
- Tab order: Email → Submit → Links
- Enter key submits form

**Resend Flow:**
- If user didn't receive email, allow clicking "Try again"
- Reset form to initial state
- Allow re-submission

**Instructional Text:**
- Display clear instructions above form:
  ```
  Enter your email address and we'll send you a link to reset your password.
  ```

---

## Redirect Logic

**No Automatic Redirects**
- User stays on page after submission
- Success message displayed in place
- User manually navigates via links:
  - "Return to login" → `/auth/login`
  - "Didn't receive email? Try again" → Clear success message, show form again

---

## Accessibility Requirements

**Keyboard Support:**
- Email input focusable
- Form submittable via Enter
- All links keyboard accessible

**Screen Reader:**
- Page title announced: "Reset Password"
- Instructions read on page load
- Success message announced via `aria-live="polite"`
- Form status announced during submission

**ARIA Attributes:**
- `aria-describedby` linking email input to instructions
- `aria-invalid="true"` on email field with error
- `aria-busy="true"` during submission
- Success message has `role="status"`

**Focus Management:**
- Email field auto-focused on load
- Focus moved to success message when displayed (optional)
- Focus on error message if validation fails

**Color Contrast:**
- All text meets 4.5:1 ratio
- Success/error states not conveyed by color alone

---

## Performance Requirements

**Page Load:**
- Initial render: < 1s
- Time to interactive: < 1.5s

**Bundle Size:**
- Page-specific code: < 20KB
- Shared components: < 15KB

**Optimization:**
- Minimal dependencies (only email validation)
- Code split from main bundle

---

## Security Considerations

**Email Enumeration Prevention:**
- CRITICAL: Always return success response
- Never reveal if email exists or not
- Same response time regardless (no timing attacks)
- Same message whether account exists or not

**Input Sanitization:**
- Sanitize email input to prevent XSS
- Validate email format before API call

**Rate Limiting:**
- Client-side: Disable rapid re-submissions (1 second cooldown)
- Server-side: Limit reset requests per IP/email
- Display: "Too many requests. Please try again later."

**Token Security (Backend):**
- Generate cryptographically secure reset tokens
- Tokens expire after 1 hour
- Tokens are single-use only
- Tokens invalidated after use or password change

---

## Analytics & Monitoring

**Page View Events:**
- Track page load
- Track referrer (likely login page)

**User Interaction Events:**
- Form submission
- "Try again" clicks
- "Return to login" clicks

**Conversion Events:**
- Reset request submitted
- Email validation errors
- Network errors

**Security Metrics:**
- Rate of reset requests per user/IP
- Unusual patterns (many different emails from one IP)

---

## Edge Cases

**Empty Email:**
- Show validation error
- Prevent form submission

**Invalid Email Format:**
- Show format validation error
- Suggest correct format example

**Multiple Rapid Submissions:**
- Client-side: Show message "Please wait before trying again"
- Server-side: Rate limit enforced

**User Repeatedly Doesn't Receive Email:**
- Success message includes: "Check spam folder"
- Provide support/help link
- Suggest checking email address spelling

**Email Not in System:**
- Still show success message (don't reveal)
- No email sent (backend handles this silently)
- User waits, doesn't receive email, may contact support

---

## Dependencies

**Parent Feature:** Authentication System (`feature.spec.md`)

**Required Components:**
- FormInput
- Button
- Alert

**Required APIs:**
- POST `/api/auth/reset-password/request`

**Related Pages:**
- Reset password form page (where user goes after clicking email link)

---

## Acceptance Criteria

- [ ] Page renders at `/auth/reset-password` route
- [ ] Email input field is present and auto-focused
- [ ] Instructions clearly explain the process
- [ ] Form validates email format before submission
- [ ] Empty email shows validation error
- [ ] Invalid email format shows validation error
- [ ] Submit button shows loading state during API call
- [ ] Success message displays after submission
- [ ] Success message is same regardless of email existence
- [ ] Success message includes instruction to check spam
- [ ] Success message mentions 1-hour expiration
- [ ] "Return to login" link navigates to login page
- [ ] "Try again" option allows re-submission
- [ ] Network errors display appropriate message
- [ ] Page is keyboard navigable
- [ ] Screen reader announces all state changes
- [ ] Page meets WCAG AA requirements
- [ ] Form submission completes within 2 seconds
- [ ] No email enumeration vulnerability exists

---

**Related Specifications:**
- Feature: `feature.spec.md` (Authentication System)
- Page: `login.page.spec.md` (Login Page)
- Page: `reset-password-confirm.page.spec.md` (Reset Form Page)
