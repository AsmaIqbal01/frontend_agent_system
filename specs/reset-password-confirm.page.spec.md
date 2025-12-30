# Password Reset Confirmation Page Specification

**Page Route:** `/auth/reset-password/[token]` or `/auth/reset-password?token=xyz`
**Parent Feature:** Authentication System (see `feature.spec.md`)
**Access Level:** Public (accessed via email link)
**Version:** 1.0.0

---

## Purpose

Enable users to set a new password after clicking the reset link from their email. Validate the reset token, guide users through password creation with strength requirements, and redirect to login upon success.

---

## Layout Structure

```
┌─────────────────────────────────────────┐
│  Header (Minimal - Logo, optional nav)  │
├─────────────────────────────────────────┤
│                                         │
│   ┌───────────────────────────────┐   │
│   │                               │   │
│   │  Reset Password Card          │   │
│   │                               │   │
│   │  - Page Title                 │   │
│   │  - New Password Input         │   │
│   │  - Password Strength Meter    │   │
│   │  - Confirm Password Input     │   │
│   │  - Submit Button              │   │
│   │  - Link to Request New Reset  │   │
│   │                               │   │
│   └───────────────────────────────┘   │
│                                         │
│  OR (if token invalid/expired):        │
│                                         │
│   ┌───────────────────────────────┐   │
│   │  Error State                  │   │
│   │  - Error Icon                 │   │
│   │  - Error Message              │   │
│   │  - Request New Link Button    │   │
│   │  - Return to Login Link       │   │
│   └───────────────────────────────┘   │
│                                         │
├─────────────────────────────────────────┤
│  Footer (Optional)                      │
└─────────────────────────────────────────┘
```

### Layout Sections

**1. Header**
- Minimal branding
- No navigation menu

**2. Main Content Area**
- Centered card
- Max-width: 480px

**3. Reset Form Card (Valid Token)**
- Page title: "Create New Password"
- Form with password fields
- Password strength indicator
- Submit button

**4. Error State Card (Invalid/Expired Token)**
- Error icon
- Clear error message
- Action button (request new reset link)
- Alternative: Return to login

---

## Required Components

### Core Components

1. **ResetPasswordForm**
   - New password input with visibility toggle
   - Password strength indicator
   - Confirm password input with visibility toggle
   - Submit button
   - Form validation
   - Loading state

2. **PasswordInput** (reusable)
   - Password field
   - Show/hide toggle
   - Error message display

3. **PasswordStrengthMeter**
   - Visual strength indicator
   - Strength label
   - Requirements checklist

4. **Button** (primary action)
   - Submit button: "Reset Password"
   - Loading spinner during submission

5. **Alert/Message**
   - Success message
   - Error message
   - Token validation errors

6. **ErrorState** (token invalid)
   - Error icon
   - Error message
   - Action buttons

7. **Link** (navigation)
   - Return to login
   - Request new reset link

---

## Page-Level State

### Token Validation State
```
{
  token: string
  isValidating: boolean
  isValid: boolean
  validationError: string | null
  errorType: 'expired' | 'invalid' | 'used' | null
}
```

### Form State
```
{
  newPassword: string
  confirmPassword: string
  errors: {
    newPassword?: string
    confirmPassword?: string
    form?: string
  }
  isSubmitting: boolean
  submitSuccess: boolean
  submitError: string | null
}
```

### Password Validation State
```
{
  strength: 'weak' | 'medium' | 'strong'
  score: number
  requirements: {
    minLength: boolean
    hasUppercase: boolean
    hasLowercase: boolean
    hasNumber: boolean
  }
  feedback: string[]
}
```

### UI State
```
{
  showNewPassword: boolean
  showConfirmPassword: boolean
  focusedField: 'newPassword' | 'confirmPassword' | null
}
```

---

## Data-Fetching Strategy

### On Page Load

**Token Extraction:**
1. Extract token from URL (path param or query param)
2. If no token present:
   - Show error: "Invalid reset link"
   - Provide link to request new reset

**Token Validation:**
1. Send GET request to `/api/auth/reset-password/validate?token=xyz`
2. Or include token in state and validate on form submission
3. Handle response:
   - **Valid (200):** Render password reset form
   - **Expired (400):** Show expired token error state
   - **Invalid (400):** Show invalid token error state
   - **Already Used (400):** Show "already used" error state

**Recommended Approach:**
- Validate token on page load (better UX)
- Show loading spinner during validation
- Render appropriate UI based on validation result

### On Form Submission

**API Call:**
1. Validate passwords client-side
2. If valid, POST to `/api/auth/reset-password/confirm` with:
   ```json
   {
     "token": "reset_token_abc123",
     "newPassword": "NewSecurePassword123"
   }
   ```
3. Handle response:
   - **Success (200):** Password reset successful
   - **Error (400):** Token invalid/expired during submission
   - **Error (400):** Password doesn't meet requirements
   - **Network Error:** Connection failed

**State Updates:**
1. Set `isSubmitting: true`
2. On success:
   - Set `submitSuccess: true`
   - Show success message
   - Redirect to login after 2 seconds
3. On error:
   - Set `submitError` with message
   - Set `isSubmitting: false`
   - Clear password fields

---

## UX Behaviors

### Loading States

**Token Validation (Page Load):**
- Show loading spinner in card
- Message: "Validating reset link..."
- Duration: Typically 200ms - 1s
- Hide form until validation complete

**Form Submission:**
- Disable submit button
- Show loading spinner in button
- Disable all inputs
- Message: "Resetting password..."
- Duration: 500ms - 2s

### Empty States

**Not Applicable**
- Page always shows form or error state

### Error States

**Token Validation Errors:**

1. **Expired Token:**
   - Icon: Warning/clock icon
   - Message: "This password reset link has expired."
   - Subtext: "Reset links are valid for 1 hour for security reasons."
   - Action: "Request New Reset Link" button → `/auth/reset-password`
   - Alternative: "Return to Login" link

2. **Invalid Token:**
   - Icon: Error icon
   - Message: "This password reset link is invalid."
   - Subtext: "The link may be incorrect or corrupted."
   - Action: "Request New Reset Link" button
   - Alternative: "Return to Login" link

3. **Already Used Token:**
   - Icon: Info icon
   - Message: "This reset link has already been used."
   - Subtext: "If you didn't change your password, request a new reset link."
   - Action: "Request New Reset Link" button
   - Alternative: "Try Logging In" link

**Form Validation Errors:**
- New Password (weak): "Password does not meet requirements"
- Confirm Password (mismatch): "Passwords do not match"
- Display inline below fields

**Submission Errors:**
- Token expired during submission: Show expired token error
- Password requirements: "Password must meet all requirements"
- Server error: "Unable to reset password. Please try again."
- Network error: "Connection failed. Check your internet connection."

### Success States

**Password Reset Successful:**
- Replace form with success message
- Green checkmark icon
- Message: "Password Successfully Reset"
- Subtext: "You can now log in with your new password."
- Auto-redirect to login page after 2 seconds
- Show countdown: "Redirecting in 3... 2... 1..."
- Provide manual link: "Go to Login"

### Interactive Behaviors

**Password Strength Indicator:**
- Updates in real-time as user types new password
- Visual bar with color coding
- Requirements checklist shows progress
- Strength label updates

**Password Visibility Toggles:**
- Separate toggles for new and confirm password
- Eye icon changes state
- Independent toggle states

**Password Matching:**
- Validate match when confirm password loses focus
- Show green checkmark if match
- Show red X if mismatch
- Update in real-time as user types (optional)

**Keyboard Navigation:**
- Tab order: New Password → Confirm Password → Submit
- Enter key submits form

**Auto-Focus:**
- New password field receives focus after validation complete

**Validation Timing:**
- New password: Real-time strength validation
- Confirm password: Validate match on blur
- Submit: Validate all before API call

---

## Redirect Logic

### Post-Reset Redirect

**Successful Password Reset:**
1. Show success message for 2 seconds
2. Auto-redirect to `/auth/login`
3. Optional: Display success toast on login page
4. Optional: Pre-fill email if available

**Token Validation Failures:**
- No automatic redirect
- User manually navigates via:
  - "Request New Reset Link" → `/auth/reset-password`
  - "Return to Login" → `/auth/login`

---

## Accessibility Requirements

**Keyboard Support:**
- All inputs keyboard accessible
- Form submittable via Enter
- Password toggles accessible via keyboard

**Screen Reader:**
- Page title announced: "Create New Password"
- Token validation status announced
- Password strength announced on change
- Requirements announced as met
- Error states announced
- Success message announced
- Countdown announced (optional)

**ARIA Attributes:**
- `aria-label` on password toggles
- `aria-describedby` linking inputs to requirements
- `aria-invalid` on fields with errors
- `aria-busy` during operations
- Password strength meter: `role="status"`
- Success/error messages: `role="alert"` or `aria-live="polite"`

**Focus Management:**
- Auto-focus new password field when form ready
- Focus on error message if validation fails
- Focus on success message after reset

**Color Contrast:**
- All text meets 4.5:1 ratio
- Password strength colors accessible
- Error/success not conveyed by color alone

---

## Performance Requirements

**Page Load:**
- Initial render: < 1s
- Token validation: < 1s
- Time to interactive: < 2s

**Bundle Size:**
- Page code: < 35KB (includes password strength library)
- Shared components: < 20KB

**Optimization:**
- Lazy load password strength library on field focus
- Code split from main bundle
- Debounce strength calculation

---

## Security Considerations

**Token Handling:**
- Extract token from URL on client
- Never log or expose token
- Validate token server-side before allowing reset
- Single-use tokens only
- Tokens expire after 1 hour
- Invalidate all user sessions after reset (optional)

**Password Requirements:**
- Minimum 8 characters
- Mixed case, numbers required
- Same requirements as registration

**Input Sanitization:**
- Sanitize all inputs to prevent XSS
- Password never exposed in logs

**CSRF Protection:**
- Include CSRF token if using cookies

**Rate Limiting:**
- Limit reset attempts per token
- Lock token after failed attempts

---

## Analytics & Monitoring

**Page View Events:**
- Track page load
- Track token validation result

**User Interaction Events:**
- Password visibility toggles
- Form field interactions
- Password strength changes

**Conversion Events:**
- Password reset attempt
- Password reset success
- Password reset failure (with error type)
- Token validation failures by type

**Security Metrics:**
- Expired token access attempts
- Invalid token access attempts
- Failed password reset attempts

---

## Edge Cases

**No Token in URL:**
- Show error: "No reset token provided"
- Action: Request new reset link
- Alternative: Return to login

**Token Expires During Form Entry:**
- User validates token (valid)
- User takes 1+ hour to submit
- Submission fails: token expired
- Show: Expired error with option to request new link

**Browser Back Button:**
- User successfully resets password
- User clicks back button
- Show: "Link already used" error
- Prevent re-submission

**Token Tampered:**
- User modifies token in URL
- Validation fails: Invalid token
- Show error state

**Very Weak Password:**
- User enters password that meets minimum but is very weak
- Option A: Block submission
- Option B: Show warning, allow submission
- Recommended: Enforce strong passwords

**Network Failure During Submission:**
- Show error message
- Allow retry
- Don't clear password fields (user doesn't have to retype)

---

## Dependencies

**Parent Feature:** Authentication System (`feature.spec.md`)

**Required Components:**
- PasswordInput
- PasswordStrengthMeter
- Button
- Alert
- ErrorState

**Required Libraries:**
- Password strength calculator (zxcvbn)
- Form validation library
- Router (for token extraction)

**Required APIs:**
- GET `/api/auth/reset-password/validate?token=xyz` (optional)
- POST `/api/auth/reset-password/confirm`

---

## Acceptance Criteria

- [ ] Page accessible at `/auth/reset-password/[token]`
- [ ] Token extracted from URL on page load
- [ ] Token validation occurs on page load
- [ ] Valid token renders password reset form
- [ ] Invalid token shows error state with recovery options
- [ ] Expired token shows appropriate error message
- [ ] New password and confirm password fields present
- [ ] Password strength meter displays and updates in real-time
- [ ] Password requirements checklist shows progress
- [ ] Confirm password validates match on blur
- [ ] Password visibility toggles work independently
- [ ] Form validates all fields before submission
- [ ] Submit button disabled until passwords valid and match
- [ ] Submit button shows loading state during API call
- [ ] Successful reset shows success message
- [ ] Successful reset redirects to login after 2 seconds
- [ ] Submission errors display appropriately
- [ ] Token expired during submission shows proper error
- [ ] "Request New Link" navigates to reset request page
- [ ] "Return to Login" navigates to login page
- [ ] Page is keyboard navigable
- [ ] Screen reader announces all state changes
- [ ] Password strength changes announced
- [ ] Page meets WCAG AA requirements
- [ ] Token validation completes within 1 second
- [ ] Password reset completes within 2 seconds

---

**Related Specifications:**
- Feature: `feature.spec.md` (Authentication System)
- Page: `reset-password-request.page.spec.md` (Request Reset Page)
- Page: `login.page.spec.md` (Login Page)
