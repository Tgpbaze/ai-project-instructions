
### 16. `PROMPTS.md` - AI Prompt Engineering Guide
```markdown
# PROMPTS.md - AI Prompt Engineering Guide

## Prompt Structure Template

### Universal Prompt Template

ROLE: [Specify the AI's role]
TASK: [Clearly state what you want]
CONTEXT: [Provide necessary background]
CONSTRAINTS: [Set boundaries and rules]
FORMAT: [Specify output format]
EXAMPLES: [Provide examples if needed]
Role: You are a [expert role] specializing in [specific domain].

Task: [Clear, actionable description of the task]

Context:

Project: [Project name and type]

Tech Stack: [List technologies]

Current State: [What exists]

Goal: [What needs to be achieved]

Constraints:

Must use [specific framework/library]

Must follow [style guide/linting rules]

Must be [performance/security] optimized

Must handle [edge cases/specific scenarios]

Must work with [browser versions/devices]

Output Format:
[Specify format - code, markdown, JSON, etc.]

Example:
[Provide an example of desired output]


### Prompt Categories

#### 1. Code Generation Prompts
```markdown
# Code Generation Prompt Template

**Role:** You are a senior software engineer with 10+ years of experience in web development.

**Task:** Create a [type of component/function/service] that [specific functionality].

**Context:**
- Technology: [React/Next.js/Vue/Node.js]
- Framework: [Tailwind/MUI/Bootstrap]
- State Management: [Redux/Context/Query]
- Data: [What data does it need to handle]

**Constraints:**
- Use TypeScript with proper types
- Include error handling
- Follow the project's naming conventions
- Optimize for performance
- Include JSDoc comments
- Make it accessible (WCAG AA)

**Example Expected Output:**
```typescript
// File: [filename].tsx
// Description: [brief description]

Validation Requirements:

Must pass linting

Must have unit tests

Must handle edge cases


#### 2. Bug Fixing Prompts
```markdown
# Bug Fixing Prompt Template

**Role:** You are an expert debugging specialist.

**Task:** Fix the following bug in the provided code.

**Context:**
- Component: [component name]
- Environment: [browser/node/production]
- Error: [error message and stack trace]
- Expected behavior: [what should happen]

**Code with Bug:**
```typescript
// Paste buggy code here

Troubleshooting Done:

Checked console logs

Verified API responses

Tested in different browsers

Constraints:

Fix the root cause, not just the symptom

Add appropriate error handling

Don't change functionality

Ensure backwards compatibility


#### 3. Architecture Design Prompts
```markdown
# Architecture Design Prompt Template

**Role:** You are a solution architect with expertise in scalable web applications.

**Task:** Design an architecture for [project description].

**Context:**
- Business Requirements: [functional requirements]
- Technical Requirements: [non-functional requirements]
- Constraints: [budget, timeline, team size]
- Scale: [expected users, data volume]

**Requirements:**
- Must handle [X] concurrent users
- Must respond in < [Y] ms
- Must be deployable to [cloud provider]
- Must integrate with [third-party services]

**Considerations:**
- Performance and scalability
- Security and compliance
- Maintainability and extensibility
- Cost optimization

**Output:**
Provide:
1. Architecture diagram (Mermaid)
2. Technology stack recommendations
3. Data flow diagram
4. Security considerations
5. Deployment strategy
6. Monitoring and logging plan

# Code Review Prompt Template

**Role:** You are a senior code reviewer focusing on quality, performance, and security.

**Task:** Review the following code for issues and improvements.

**Context:**
- Project: [project name and version]
- Standards: [coding standards document]
- Purpose: [what this code should do]

**Code to Review:**
```typescript
// Paste code here

Focus Areas:

Code quality and readability

Performance and optimization

Security vulnerabilities

Error handling

Edge cases

Testing coverage

Documentation

Review Format:

# Critical Issues
- [issue description] (line X)
  - Fix: [suggested fix]

# Major Issues
- [issue description] (line Y)
  - Fix: [suggested fix]

# Minor Issues
- [issue description] (line Z)
  - Fix: [suggested fix]

# Suggestions
- [improvement suggestion]
- [performance optimization]
- [security enhancement]

Example:

"The function doesn't handle null inputs"

"This query will cause an N+1 problem"

"API keys are hardcoded, use environment variables"


#### 5. Documentation Prompts
```markdown
# Documentation Prompt Template

**Role:** You are a technical writer specializing in developer documentation.

**Task:** Create [documentation type] for [project/feature].

**Context:**
- Target Audience: [developers/end users/operators]
- Skill Level: [beginner/intermediate/expert]
- Use Case: [when and why this is needed]

**Documentation Structure:**
1. Overview
2. Installation/Setup
3. Configuration
4. Usage Examples
5. API Reference
6. Troubleshooting
7. FAQ

**Tone:**
- [ ] Formal and technical
- [ ] Conversational
- [ ] Concise and direct
- [ ] Step-by-step tutorial

**Required Sections:**
- [ ] Getting Started
- [ ] Configuration Options
- [ ] Common Use Cases
- [ ] Error Messages
- [ ] Performance Tips

**Example:**
[Provide example of desired tone and structure]

Specialized Prompts
Web Development Prompts
React Component Generation

**Role:** Senior React Developer with TypeScript expertise.

**Task:** Create a [Component Name] component that [specific functionality].

**Component Specifications:**
- Props interface required
- Include loading and error states
- Use React Hooks
- Implement proper memoization
- Include accessibility attributes
- Responsive design (mobile-first)

**Tech Stack:**
- React 18+
- TypeScript
- TailwindCSS (or MUI)
- React Query for data fetching
- React Hook Form for forms

**State Management:**
- Local state: [useState/useReducer]
- Server state: [React Query]
- Global state: [Context/Zustand]

**Implementation:**
```typescript
import React, { useState, useEffect } from 'react';
// Complete component code here

**Role:** Senior React Developer with TypeScript expertise.

**Task:** Create a [Component Name] component that [specific functionality].

**Component Specifications:**
- Props interface required
- Include loading and error states
- Use React Hooks
- Implement proper memoization
- Include accessibility attributes
- Responsive design (mobile-first)

**Tech Stack:**
- React 18+
- TypeScript
- TailwindCSS (or MUI)
- React Query for data fetching
- React Hook Form for forms

**State Management:**
- Local state: [useState/useReducer]
- Server state: [React Query]
- Global state: [Context/Zustand]

**Implementation:**
```typescript
import React, { useState, useEffect } from 'react';
// Complete component code here

Testing Requirements:

Unit tests (React Testing Library)

Test loading states

Test error states

Test user interactions

Test accessibility


#### API Endpoint Generation
```markdown
**Role:** Backend API Developer specializing in RESTful services.

**Task:** Create a RESTful API endpoint for [resource name].

**Endpoint Specifications:**
- Method: [GET/POST/PUT/DELETE]
- Path: /api/v1/[resource]
- Authentication: [JWT/OAuth/API Key]
- Rate Limiting: [X requests/minute]

**Request:**
- Headers: [required headers]
- Body/Params: [schema]
- Validation: [rules]

**Response:**
- Success: [schema, status code]
- Error: [schema, status codes]

**Implementation:**
```javascript
// Endpoint implementation with validation

Security:

Input validation

SQL injection prevention

XSS prevention

CSRF protection

Performance:

Database indexes

Caching strategy

Pagination support

Query optimization


### DevOps Prompts

#### Docker Setup Prompt
```markdown
**Role:** DevOps Engineer with expertise in containerization.

**Task:** Create a Docker setup for [application type].

**Application Details:**
- Type: [Node.js/Python/Java]
- Framework: [Express/Django/Spring]
- Dependencies: [list key dependencies]
- Database: [PostgreSQL/MongoDB]

**Requirements:**
- Multi-stage Dockerfile
- Production and development configurations
- Environment variables
- Volume mounting for development
- Healthcheck endpoint
- Non-root user for security

**Output:**
1. Dockerfile (production and dev)
2. docker-compose.yml
3. .dockerignore
4. Environment variables list
5. Deployment instructions

**Implementation:**
```dockerfile
# Dockerfile
# Complete configuration here

Additional:

CI/CD integration considerations

Security hardening

Monitoring setup

Backup strategy


### CI/CD Pipeline Prompts
```markdown
**Role:** DevOps Engineer specializing in CI/CD.

**Task:** Create a CI/CD pipeline for [project].

**Repository:** [GitHub/GitLab/Bitbucket]
**Platform:** [GitHub Actions/GitLab CI/Jenkins]
**Deployment:** [AWS/GCP/Azure/On-prem]

**Pipeline Stages:**
1. Build: [lint, test, compile]
2. Test: [unit, integration, security]
3. Build Image: [Docker build]
4. Deploy: [staging/production]

**Requirements:**
- Environment-specific configurations
- Secrets management
- Rollback capability
- Notification system
- Approval gates for production

**Implementation:**
```yaml
# .github/workflows/ci-cd.yml
# Complete pipeline configuration

Monitoring:

Deployment success rate

Performance regression detection

Security scanning results

Build time tracking


## Prompt Optimization Tips

### Prompt Engineering Best Practices

1. **Be Specific**: "Create a React component" vs "Create a React component that displays user profiles with avatar, name, email, and a follow button with loading states"

2. **Provide Examples**: Show what you want, not just describe it
   ```markdown
   **Example Input:** { name: "John" }
   **Expected Output:** { id: 1, name: "John", created: "2024-01-01" }

   Set Constraints: "Must use TypeScript" vs "It would be nice to use TypeScript"

Define Success: "The component should render in under 100ms and handle empty states gracefully"

Request Step-by-Step: "First, explain your approach. Then, provide the implementation. Finally, suggest tests."

# PROMPT TEMPLATE: CREATE A NEW FEATURE

**Role:** [Senior Developer/Specialist]
**Task:** [Feature Description]
**Context:** [Project Info, Tech Stack]
**Requirements:** [Functional & Non-functional]
**Constraints:** [Limitations & Rules]
**Acceptance Criteria:**
- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3
**Output:** [Code/Documentation/Diagram]

---

# PROMPT TEMPLATE: OPTIMIZE PERFORMANCE

**Role:** Performance Engineer
**Task:** Optimize [component/service] performance
**Current Performance:**
- Metric: [value]
- Goal: [target]
**Profiling Results:**
- [Bottleneck 1]
- [Bottleneck 2]
**Constraints:** [Cannot change X, Must maintain Y]
**Optimization Options:**
1. Option 1 - [pros/cons]
2. Option 2 - [pros/cons]
**Output:** Implementation plan with code changes

---

# PROMPT TEMPLATE: SECURITY AUDIT

**Role:** Security Expert
**Task:** Audit [feature/service] for vulnerabilities
**Scope:** [What to audit]
**Security Standards:** [OWASP Top 10, SOC2, GDPR]
**Known Issues:** [List known concerns]
**Audit Areas:**
- [ ] Authentication & Authorization
- [ ] Input Validation
- [ ] Data Protection
- [ ] API Security
- [ ] Dependency Security
**Output:** Vulnerability report with fixes

---

# PROMPT TEMPLATE: DEPLOYMENT PLANNING

**Role:** DevOps Engineer
**Task:** Plan deployment for [application]
**Environment:** [Production/Staging]
**Requirements:** [Zero downtime, Rollback plan]
**Infrastructure:** [Cloud provider, Resources]
**Risk Assessment:**
- Risk 1: [description, probability, impact]
- Risk 2: [description, probability, impact]
**Rollback Plan:**
1. Step 1
2. Step 2
**Monitoring:** [Metrics to watch, Alerts]
**Success Criteria:** [How to know deployment succeeded]

AI Interaction Best Practices
State Assumptions Clearly

"I assume you know the project structure..."

"Assuming Node.js 18+ is available..."

Provide File Structure

src/
├── components/
│   └── UserProfile.tsx  # Need to modify
├── hooks/
│   └── useUser.ts       # Need to create
└── types/
    └── user.ts          # Need to update

    Share Error Messages

json
{ "error": "Cannot read property 'map' of undefined", "location": "UserList.tsx:15" }
Specify Desired Output

typescript
// I want the API response to have this shape
interface APIResponse {
  data: {
    id: string;
    name: string;
  };
  meta: {
    timestamp: string;
    version: string;
  };
}
Request Iterative Improvement

"Can you make it more performant?"

"How would you make this more secure?"

"What edge cases are missing?"

Prompt Validation Checklist
Role clearly defined

Task unambiguous

Context sufficient

Constraints explicit

Output format specified

Examples provided (if needed)

Success criteria defined

Dependencies stated

Assumptions called out

Iteration opportunities offered