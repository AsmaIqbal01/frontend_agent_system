# Quick Start Guide

Welcome to the **Frontend Agent System**! This guide will help you get started with spec-driven frontend development using Spec-Kit Plus methodology.

## Prerequisites

- Git installed on your machine
- Basic understanding of frontend development
- A code editor (VS Code recommended)
- An AI assistant with access to this repository (Claude, GPT-4, etc.)

---

## Step 1: Write a Feature Specification

All implementation starts with a specification. Create a new spec file in the `/specs` directory.

### Option A: Use the Spec Agent to Generate a Spec

Provide your AI assistant with this prompt:

```
Using the Spec Agent from this repository, create a feature specification for:
[Your feature description]

Target Framework: [React/Next.js/Vue/Svelte]
Styling System: [Tailwind/CSS Modules/Styled Components]
```

**Example:**
```
Using the Spec Agent, create a feature specification for:
A user dashboard with profile editing, activity feed, and settings.

Target Framework: Next.js
Styling System: Tailwind CSS
```

### Option B: Write Your Own Spec

Create a new file following the template:

**File:** `specs/dashboard.feature.spec.md`

```markdown
# Dashboard Feature Specification

**Version:** 1.0.0
**Date:** YYYY-MM-DD
**Target Framework:** Next.js
**Styling System:** Tailwind CSS

## Purpose
[What the feature does and why it exists]

## Requirements
- Functional requirement 1
- Functional requirement 2
- Non-functional requirement 1

## User Flows
### Flow 1: [Flow Name]
1. Step 1
2. Step 2
3. Step 3

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2
```

See `specs/feature.spec.md` for a complete example.

---

## Step 2: Run the Feature Using the Frontend Orchestrator

Once your specification is complete, invoke the **Frontend Orchestrator** to implement it.

### Prompt Template

Provide your AI assistant with this prompt:

```
I want to implement the feature specification at: specs/[your-spec-file].spec.md

Use the Frontend Orchestrator agent from this repository to:
1. Analyze the specification
2. Decompose work into domain-specific tasks
3. Delegate to appropriate sub-agents (UI, UX, State, API, Performance, Accessibility)
4. Validate outputs against the spec
5. Deliver the complete implementation

Follow the constitution at CONSTITUTION.md strictly.
```

**Example:**
```
I want to implement the feature specification at: specs/dashboard.feature.spec.md

Use the Frontend Orchestrator agent from this repository to:
1. Analyze the specification
2. Decompose work into domain-specific tasks
3. Delegate to appropriate sub-agents
4. Validate outputs against the spec
5. Deliver the complete implementation

Follow the constitution at CONSTITUTION.md strictly.
```

---

## Step 3: Understand Agent Delegation

The Frontend Orchestrator automatically delegates to specialized sub-agents:

| Task Type | Delegated To | Example |
|-----------|-------------|---------|
| Component structure, layout | **UI Agent** | Creating div layouts, applying Tailwind classes |
| User interactions, forms | **UX Agent** | Click handlers, form validation, loading states |
| State management | **State Agent** | Global state (Context/Redux), local state hooks |
| API integration | **API Agent** | Fetch calls, error handling, data transformation |
| Performance optimization | **Performance Agent** | Code splitting, lazy loading, bundle optimization |
| Accessibility compliance | **Accessibility Agent** | ARIA labels, keyboard navigation, WCAG AA |
| Output validation | **Validation Agent** | Spec compliance, quality checks |

You don't need to manually delegate—the Orchestrator handles this automatically based on your specification.

---

## Step 4: Apply Skills Automatically

Skills are reusable decision-making tools that agents invoke automatically:

### Available Skills

1. **Component Decomposition** (`skills/component-decomposition.skill.md`)
   - Breaks down complex UI into atomic, reusable components
   - Used by: UI Agent
   - When: Analyzing page specs to identify component hierarchy

2. **State Normalization** (`skills/state-normalization.skill.md`)
   - Transforms nested data into flat, normalized structures
   - Used by: State Agent
   - When: Designing state architecture for complex data

3. **Accessibility Audit** (`skills/accessibility-audit.skill.md`)
   - Performs rapid WCAG 2.1 AA compliance assessment
   - Used by: Accessibility Agent, Validation Agent
   - When: Reviewing implementations for accessibility issues

4. **Layout Consistency** (`skills/layout-consistency.skill.md`)
   - Ensures consistent spacing, sizing using 8-point grid system
   - Used by: UI Agent
   - When: Choosing spacing values and layout patterns

**Skills are invoked automatically—you don't need to call them manually.**

---

## Step 5: Generate Structured Frontend Output

The Frontend Orchestrator delivers complete, production-ready code organized by domain:

### Expected Output Structure

```
src/
├── components/           # UI components (from UI Agent)
│   ├── Button.tsx
│   ├── FormInput.tsx
│   └── Alert.tsx
├── pages/               # Page components (from UI + UX Agents)
│   ├── login.tsx
│   └── dashboard.tsx
├── state/               # State management (from State Agent)
│   ├── authContext.tsx
│   └── userStore.ts
├── api/                 # API integration (from API Agent)
│   ├── authAPI.ts
│   └── userAPI.ts
├── styles/              # Styling (from UI Agent)
│   └── globals.css
└── utils/               # Utilities (from Performance/Accessibility Agents)
    ├── lazyLoad.ts
    └── a11y.ts
```

### Validation Report

The Validation Agent provides a final report:

```
✅ Specification Compliance: 100%
✅ All Acceptance Criteria Met
✅ WCAG 2.1 AA Compliance: Pass
✅ Architectural Consistency: Pass
✅ Code Quality: Production-Ready
```

---

## Step 6: Local Setup and Git Workflow

### Initial Setup

```bash
# Clone the repository
git clone <your-repo-url>
cd frontend_agent_system

# Create a new branch for your feature
git checkout -b feature/dashboard

# Review the generated code in src/
# Test the implementation
```

### Commit and Push Changes

```bash
# Stage all changes
git add .

# Commit with a descriptive message
git commit -m "feat: implement dashboard feature with profile editing, activity feed, and settings

- Generated from specs/dashboard.feature.spec.md
- Delegated to UI, UX, State, and API agents
- Passed validation with 100% spec compliance
- WCAG 2.1 AA compliant

🤖 Generated with Frontend Agent System"

# Push to remote
git push origin feature/dashboard
```

### Create a Pull Request

```bash
# Using GitHub CLI (optional)
gh pr create --title "Dashboard Feature Implementation" \
  --body "Implements dashboard feature per specs/dashboard.feature.spec.md

## Summary
- Profile editing interface
- Real-time activity feed
- User settings management

## Validation
- ✅ 100% spec compliance
- ✅ WCAG 2.1 AA
- ✅ Performance optimized

## Test Plan
- [ ] Profile editing workflow
- [ ] Activity feed updates
- [ ] Settings persistence

🤖 Generated with Frontend Agent System"
```

---

## Best Practices

### 1. **Always Start with Specs**
Never write code without a specification. Use the Spec Agent or write your own.

### 2. **Trust the Delegation Hierarchy**
Let the Frontend Orchestrator handle sub-agent delegation. Don't manually assign tasks unless necessary.

### 3. **Leverage Existing Specs**
Review `specs/` directory for examples:
- `feature.spec.md` - Complete feature specification
- `*.page.spec.md` - Page-level specifications
- `*.component.spec.md` - Component specifications

### 4. **Follow the Constitution**
All implementations must follow `CONSTITUTION.md` principles:
- Spec-first development
- No manual implementation by orchestrators
- Strict delegation to specialized agents
- Framework agnosticism
- Validation required

### 5. **Validate Before Delivery**
Always request validation from the Validation Agent before considering work complete.

### 6. **Document Architectural Decisions**
If deviations from the spec occur, document them with clear justifications.

---

## Common Workflows

### Workflow 1: Implement a Single Page

```
1. Create page spec: specs/contact.page.spec.md
2. Invoke Frontend Orchestrator with the spec
3. Receive implementation (component + state + API)
4. Validate against spec
5. Commit and push
```

### Workflow 2: Implement a Reusable Component

```
1. Create component spec: specs/modal.component.spec.md
2. Invoke Frontend Orchestrator
3. UI Agent creates structure, UX Agent adds interactions
4. Accessibility Agent ensures WCAG compliance
5. Receive complete, accessible component
6. Commit and push
```

### Workflow 3: Implement a Complete Feature

```
1. Create feature spec: specs/authentication.feature.spec.md
2. Invoke Frontend Orchestrator
3. Orchestrator delegates to multiple agents for:
   - Shared components (UI Agent)
   - Multiple pages (UI + UX Agents)
   - Global auth state (State Agent)
   - API integration (API Agent)
   - Accessibility (Accessibility Agent)
4. Validation Agent verifies end-to-end
5. Receive complete feature implementation
6. Commit and push
```

---

## Troubleshooting

### Issue: "Specification Incomplete"
**Solution:** Review your spec and ensure it includes:
- Purpose
- Requirements
- User flows
- Acceptance criteria
- Framework and styling system

### Issue: "Validation Failed"
**Solution:** Check the validation report for specific failures. Common issues:
- Missing acceptance criteria
- Accessibility violations (WCAG)
- Architectural inconsistencies

### Issue: "Agent Delegation Failed"
**Solution:** Ensure your task clearly maps to agent domains:
- UI structure → UI Agent
- User interactions → UX Agent
- State management → State Agent
- API calls → API Agent

---

## Next Steps

1. **Explore Example Specs:** Review files in `specs/` to understand spec formats
2. **Review Agent Definitions:** Read `agents/` to understand agent capabilities
3. **Study Skills:** Examine `skills/` to see decision-making frameworks
4. **Read the Constitution:** Understand governance rules in `CONSTITUTION.md`
5. **Start Small:** Begin with a simple component, then progress to pages and features

---

## Support

- **Issues:** Review `CONSTITUTION.md` for governance rules
- **Examples:** Check `specs/` directory for specification templates
- **Agent Capabilities:** See `agents/` directory for detailed agent documentation

**Happy building with Spec-Kit Plus! 🚀**
