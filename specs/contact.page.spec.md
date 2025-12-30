# Contact Page Specification

**Page Route:** `/contact`
**Access Level:** Public
**Version:** 1.0.0

---

## Purpose

Provide users with a way to communicate with the organization, submit inquiries, report issues, or request support. Collect contact information and messages through a form and deliver them to the appropriate team or system.

---

## Layout Structure

```
┌─────────────────────────────────────────┐
│  Header / Navigation                    │
├─────────────────────────────────────────┤
│                                         │
│  Page Header                            │
│  (Title, Subtitle)                      │
│                                         │
├─────────────────────────────────────────┤
│                                         │
│  ┌─────────────────┬─────────────────┐ │
│  │                 │                 │ │
│  │  Contact Form   │  Contact Info   │ │
│  │                 │                 │ │
│  │  - Name         │  - Email        │ │
│  │  - Email        │  - Phone        │ │
│  │  - Subject      │  - Address      │ │
│  │  - Message      │  - Hours        │ │
│  │  - Submit       │  - Social       │ │
│  │                 │                 │ │
│  └─────────────────┴─────────────────┘ │
│                                         │
├─────────────────────────────────────────┤
│  Footer                                 │
└─────────────────────────────────────────┘
```

### Layout Sections

**1. Header/Navigation**
- Standard site header
- Navigation menu
- Logo

**2. Page Header**
- Page title: "Contact Us" or "Get in Touch"
- Subtitle: Friendly message or value prop
  - "We'd love to hear from you"
  - "Have questions? We're here to help"

**3. Two-Column Layout (Responsive)**
- **Left Column:** Contact form
- **Right Column:** Contact information and details

**4. Contact Form Section**
- Form title (optional)
- Input fields
- Submit button
- Success/error messages

**5. Contact Information Section**
- Company/organization info
- Multiple contact methods:
  - Email address(es)
  - Phone number(s)
  - Physical address (if applicable)
  - Business hours
- Social media links
- Optional: Embedded map

**6. Footer**
- Standard site footer

---

## Required Components

### Form Components

1. **ContactForm**
   - Name input
   - Email input
   - Subject dropdown or input
   - Message textarea
   - Submit button
   - Form validation
   - Loading state
   - Success/error message display

2. **FormInput** (reusable)
   - Text inputs (name, email, subject)
   - Label
   - Error message display
   - Required indicator

3. **Textarea**
   - Multi-line message input
   - Character counter (optional)
   - Auto-resize (optional)
   - Label and error display

4. **Select** (dropdown)
   - Subject categories dropdown
   - Label and error display

5. **Button**
   - Submit button with loading state

6. **Alert/Message**
   - Success confirmation
   - Error messages

### Info Components

7. **ContactInfoCard**
   - Icon
   - Label (Email, Phone, Address, etc.)
   - Value (the actual contact detail)
   - Optional: Click-to-action (mailto, tel)

8. **SocialLinks**
   - Social media icons
   - Links to social profiles

9. **Map** (Optional)
   - Embedded map (Google Maps, OpenStreetMap)
   - Shows physical location

---

## Page-Level State

### Form State
```
{
  name: string
  email: string
  subject: string
  message: string
  errors: {
    name?: string
    email?: string
    subject?: string
    message?: string
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
  focusedField: string | null
  characterCount: number  // For message field
}
```

### Optional: Subject Categories State
```
{
  subjects: Array<{id: string, label: string}>
}
```

---

## Data-Fetching Strategy

### On Page Load

**Static Content:**
- Contact information rendered from static config
- No API calls required
- Page can be statically generated (SSG)

**Optional: Dynamic Contact Info:**
- If business hours or contact details come from CMS:
  - Fetch on server-side (getStaticProps or getServerSideProps)
  - Or fetch client-side with loading skeleton

**Subject Categories:**
- Hardcoded options:
  - "General Inquiry"
  - "Support Request"
  - "Sales Question"
  - "Partnership Opportunity"
  - "Bug Report"
  - "Other"
- Or fetch from API/config

### On Form Submission

**API Call:**
1. Validate form client-side
2. If valid, POST to `/api/contact` or email service API:
   ```json
   {
     "name": "John Doe",
     "email": "john@example.com",
     "subject": "Support Request",
     "message": "I need help with..."
   }
   ```
3. Handle response:
   - **Success (200):** Message sent successfully
   - **Error (400):** Validation error
   - **Error (500):** Server error
   - **Network Error:** Connection failed

**Backend Processing:**
- Send email to support team
- Store in database (ticket system)
- Trigger notification (Slack, etc.)
- Send confirmation email to user

**State Updates:**
1. Set `isSubmitting: true`
2. On success:
   - Set `submitSuccess: true`
   - Show success message
   - Clear form fields
   - Reset to initial state after 5 seconds
3. On error:
   - Set `submitError` with message
   - Keep form data (user doesn't have to retype)
   - Set `isSubmitting: false`

---

## UX Behaviors

### Loading States

**Page Load:**
- Static content, minimal loading
- If map embedded: Show placeholder until loaded

**Form Submission:**
- Disable submit button
- Show loading spinner in button
- Disable all form inputs
- Visual feedback: "Sending message..."
- Duration: Typically 1-3 seconds

### Empty States

**Not Applicable**
- Form always rendered empty initially

### Error States

**Form Validation Errors:**
- Name (empty): "Name is required"
- Email (invalid): "Please enter a valid email address"
- Email (empty): "Email is required"
- Subject (empty): "Please select a subject"
- Message (empty): "Message is required"
- Message (too short): "Please provide more details (minimum 10 characters)"
- Display inline below each field
- Red text with error icon

**Submission Errors:**
- Display at top of form
- Red background alert
- Messages:
  - Server error: "Unable to send message. Please try again or email us directly at support@example.com"
  - Network error: "Connection failed. Check your internet connection and try again."
  - Rate limit: "Too many messages sent. Please wait and try again."

### Success States

**Message Sent Successfully:**
- Display success message at top of form or replace form
- Green background alert with checkmark
- Message:
  ```
  Message Sent Successfully!

  Thank you for contacting us. We've received your message and will
  respond within 24 hours.
  ```
- Clear all form fields
- Auto-dismiss success message after 5 seconds (or keep visible)
- Return focus to form (or top of page)

### Interactive Behaviors

**Character Counter:**
- Show character count for message field
- Update in real-time as user types
- Optional: Set maximum character limit (e.g., 1000)
- Display: "0 / 1000 characters"

**Auto-Resize Textarea:**
- Message textarea grows as user types
- Minimum height: 6 rows
- Maximum height: 20 rows
- Scroll if exceeds maximum

**Email/Phone Links:**
- Email in contact info: `mailto:` link
- Phone number: `tel:` link
- Click opens default email client or dialer

**Map Interaction (if embedded):**
- Click: Opens full map in new tab (Google Maps)
- Zoom and pan enabled
- Marker shows location

**Keyboard Navigation:**
- Tab order: Name → Email → Subject → Message → Submit
- Enter in text fields: Move to next field (not submit)
- Submit only on button click or Ctrl+Enter in message

**Validation Timing:**
- Name: Validate on blur
- Email: Validate format on blur
- Subject: Validate on blur or change
- Message: Validate on blur
- All fields: Validate on submit

---

## Redirect Logic

**No Redirects:**
- User stays on contact page after submission
- Success message displayed inline
- User can submit another message if needed

**Optional: Thank You Page:**
- After successful submission, redirect to `/contact/thank-you`
- Thank you page shows confirmation message
- Link back to home or other pages

---

## Accessibility Requirements

**Keyboard Navigation:**
- All form fields keyboard accessible
- Tab through inputs in logical order
- Submit via Enter (when focused on button) or Ctrl+Enter (in textarea)

**Screen Reader:**
- Page title announced: "Contact Us"
- All inputs have associated labels
- Required fields announced
- Error messages announced via `aria-live="polite"`
- Success message announced
- Character counter announced (non-intrusively)

**ARIA Attributes:**
- `aria-required="true"` on required fields
- `aria-invalid="true"` on fields with errors
- `aria-describedby` linking inputs to errors and hints
- `aria-busy="true"` during submission
- Success/error messages: `role="alert"` or `aria-live="polite"`

**Focus Management:**
- Auto-focus name field on page load (optional)
- Focus on first error if validation fails
- Focus on success message after successful submission

**Color Contrast:**
- All text meets 4.5:1 ratio
- Form field borders visible
- Error/success states not color-only

**Form Labels:**
- All inputs have visible labels
- Placeholder text not used as sole label
- Required fields marked visually (asterisk) and semantically

---

## Performance Requirements

**Page Load:**
- First Contentful Paint: < 1.5s
- Time to Interactive: < 2.5s

**Bundle Size:**
- Page-specific code: < 40KB
- Map embed: Lazy load (load when in viewport)

**Optimization:**
- Server-side rendering or static generation
- Lazy load map component
- Optimize images (if any)
- Minimal JavaScript for basic form

---

## Security Considerations

**Input Sanitization:**
- Sanitize all form inputs to prevent XSS
- Validate email format
- Limit message length (prevent abuse)

**Spam Prevention:**
- CAPTCHA or honeypot field (invisible to users)
- Rate limiting on server (e.g., 3 submissions per hour per IP)
- Validate origin of submission (CSRF token)

**Data Privacy:**
- Don't log sensitive info
- Secure transmission (HTTPS)
- Privacy policy link visible
- GDPR compliance if applicable (data processing notice)

**Email Injection Prevention:**
- Validate email format strictly
- Prevent header injection in email sending

---

## Analytics & Monitoring

**Page View Events:**
- Track page load
- Track referrer

**User Interaction Events:**
- Form field interactions (focus, blur)
- Subject selection
- Form submission attempts
- Email/phone link clicks
- Map interactions

**Conversion Events:**
- Form submission (attempt)
- Form submission success
- Form submission failure (with error type)

**Performance Metrics:**
- Page load time
- Form submission response time
- Error rate
- Spam/bot submission rate

---

## Edge Cases

**Very Long Message:**
- Enforce maximum character limit (e.g., 1000 or 2000 chars)
- Show character counter
- Prevent submission if exceeded

**Empty Form Submission:**
- Validate all required fields
- Show all error messages at once
- Don't submit to server

**Rapid Repeated Submissions:**
- Client-side: Disable submit button for 3 seconds after successful submission
- Server-side: Rate limit by IP and/or email

**Invalid Email Format:**
- Comprehensive email validation
- Suggest corrections for common typos (optional)

**User Closes Page During Submission:**
- Show browser confirmation: "Message not sent. Leave anyway?"
- Or allow background submission to complete

**Bot/Spam Submissions:**
- Honeypot field (hidden, should remain empty)
- CAPTCHA for excessive submissions
- Server-side validation and filtering

**No JavaScript:**
- Form should still work (standard HTML form submission)
- Server-side rendering ensures basic functionality
- Progressive enhancement for character counter, auto-resize

---

## Dependencies

**Required Components:**
- FormInput
- Textarea
- Select (dropdown)
- Button
- Alert
- ContactInfoCard

**Optional Components:**
- Map embed component
- CAPTCHA component

**Required APIs:**
- POST `/api/contact` (or email service API)

**Optional Services:**
- Google Maps API (if embedding map)
- CAPTCHA service (reCAPTCHA, hCaptcha)
- Email service (SendGrid, AWS SES, Resend)

---

## Acceptance Criteria

- [ ] Page renders at `/contact` route
- [ ] Contact form displays with all required fields
- [ ] Contact information displayed in sidebar/section
- [ ] Name, email, subject, and message fields present
- [ ] All fields have visible labels
- [ ] Required fields marked with asterisk
- [ ] Form validates empty required fields
- [ ] Email field validates format
- [ ] Message field has minimum length validation
- [ ] Character counter displays and updates in real-time
- [ ] Submit button disabled during submission
- [ ] Submit button shows loading state
- [ ] Successful submission shows success message
- [ ] Successful submission clears form fields
- [ ] Form errors display inline below fields
- [ ] Server errors display at top of form
- [ ] Email links in contact info open mailto
- [ ] Phone links open tel (on mobile)
- [ ] Page is keyboard navigable
- [ ] All form fields accessible to screen readers
- [ ] Error and success messages announced
- [ ] Page meets WCAG AA requirements
- [ ] Form submission completes within 3 seconds
- [ ] Page loads within 2.5 seconds
- [ ] Spam prevention implemented (honeypot or CAPTCHA)
- [ ] HTTPS enforced for form submission

---

**Related Specifications:**
- Component: ContactForm
- Component: FormInput
- Component: ContactInfoCard
- API: POST `/api/contact`
