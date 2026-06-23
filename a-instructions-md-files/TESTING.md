
### 10. Testing Standards & Guidelines
```markdown
# TESTING.md - Testing Standards & Guidelines

## Testing Philosophy
- **Test early, test often**
- **Automate everything possible**
- **Test for expected and unexpected behavior**
- **Coverage is important, but quality matters more**
- **Tests should be deterministic**

## Test Pyramid

### 1. Unit Tests (Most)
**Purpose**: Test individual functions in isolation
**Characteristics**: Fast, cheap, runs in milliseconds

```javascript
// ✅ Good unit test
describe('UserService', () => {
  it('should create a user with valid data', () => {
    const result = createUser({ name: 'John', email: 'john@example.com' });
    expect(result).toHaveProperty('id');
    expect(result.name).toBe('John');
  });

  it('should throw error with invalid email', () => {
    expect(() => createUser({ name: 'John', email: 'invalid' }))
      .toThrow('Invalid email');
  });
});

2. Integration Tests (Medium)
Purpose: Test interactions between components
Characteristics: Slower, tests databases, APIs, external services

javascript
// ✅ Good integration test
describe('User API Integration', () => {
  beforeAll(async () => {
    await db.migrate();
    await db.seed();
  });

  afterAll(async () => {
    await db.cleanup();
  });

  it('should get user from database', async () => {
    const response = await request(app)
      .get('/api/users/1')
      .expect(200);
    
    expect(response.body).toMatchObject({
      id: 1,
      name: 'John Doe',
    });
  });
});
3. End-to-End Tests (Few)
Purpose: Test full application flow
Characteristics: Slow, tests full user journey

javascript
// ✅ Good E2E test
describe('User Journey', () => {
  it('should sign up, login, and create post', async () => {
    // Sign up
    await page.goto('/signup');
    await page.fill('#email', 'test@example.com');
    await page.fill('#password', 'Password123');
    await page.click('#submit');
    
    // Verify dashboard
    await expect(page.locator('h1')).toHaveText('Welcome');

    // Create post
    await page.click('#create-post');
    await page.fill('#title', 'My First Post');
    await page.fill('#content', 'Hello World');
    await page.click('#publish');
    
    // Verify post appears
    await expect(page.locator('.post')).toHaveText('My First Post');
  });
});
Testing Tools & Frameworks
Frontend Testing
javascript
// ✅ Jest for unit testing
import { render, screen, fireEvent } from '@testing-library/react';
import Button from './Button';

test('button handles click', () => {
  const handleClick = jest.fn();
  render(<Button onClick={handleClick}>Click me</Button>);
  
  fireEvent.click(screen.getByText('Click me'));
  expect(handleClick).toHaveBeenCalledTimes(1);
});

// ✅ React Testing Library for component testing
test('component renders correctly', () => {
  render(<UserProfile user={userData} />);
  expect(screen.getByText('John Doe')).toBeInTheDocument();
  expect(screen.getByAltText('User avatar')).toHaveAttribute('src', '/avatar.jpg');
});

// ✅ Cypress for E2E testing
describe('Login Flow', () => {
  it('should login successfully', () => {
    cy.visit('/login');
    cy.get('#email').type('test@example.com');
    cy.get('#password').type('Password123');
    cy.get('#submit').click();
    cy.url().should('include', '/dashboard');
  });
});
Backend Testing
javascript
// ✅ Jest for API testing
describe('POST /api/users', () => {
  it('creates a new user', async () => {
    const res = await request(app)
      .post('/api/users')
      .send({ name: 'Jane', email: 'jane@example.com' });
    
    expect(res.statusCode).toBe(201);
    expect(res.body).toHaveProperty('id');
    expect(res.body.name).toBe('Jane');
  });

  it('returns 400 for invalid email', async () => {
    const res = await request(app)
      .post('/api/users')
      .send({ name: 'Jane', email: 'invalid' });
    
    expect(res.statusCode).toBe(400);
    expect(res.body).toHaveProperty('error');
  });
});

// ✅ Database testing with in-memory DB
const setupDB = async () => {
  const db = await sqlite(':memory:');
  await db.migrate();
  return db;
};
Testing Patterns
Mocking
javascript
// ✅ Good: Mock external dependencies
jest.mock('../utils/email', () => ({
  sendEmail: jest.fn().mockResolvedValue(true),
}));

// ✅ Good: Mock APIs
jest.spyOn(api, 'fetchUser').mockResolvedValue({ name: 'Mock User' });

// ✅ Good: Mock environment variables
const originalEnv = process.env;
beforeEach(() => {
  process.env = { ...originalEnv, NODE_ENV: 'test' };
});
Test Fixtures
javascript
// ✅ Good: Test fixtures
const userFixtures = {
  validUser: () => ({
    id: 1,
    name: 'John Doe',
    email: 'john@example.com',
    role: 'user',
  }),
  adminUser: () => ({
    id: 2,
    name: 'Admin',
    email: 'admin@example.com',
    role: 'admin',
  }),
  invalidUser: () => ({
    id: 3,
    name: '', // Invalid: empty name
    email: 'invalid', // Invalid: malformed email
  }),
};
Test Coverage
Coverage Targets
Type	Minimum	Target
Overall	80%	90%
New Code	90%	95%
Critical Paths	95%	100%
Coverage Check
bash
# ✅ Good: Run coverage
npm test -- --coverage

# ✅ Good: Coverage thresholds in config
{
  "jest": {
    "coverageThreshold": {
      "global": {
        "branches": 80,
        "functions": 80,
        "lines": 80,
        "statements": 80
      }
    }
  }
}
Testing Best Practices
Arrange-Act-Assert (AAA)
javascript
// ✅ Good: AAA pattern
test('should add two numbers', () => {
  // Arrange
  const a = 1;
  const b = 2;
  
  // Act
  const result = add(a, b);
  
  // Assert
  expect(result).toBe(3);
});
Test Naming
javascript
// ✅ Good: Descriptive test names
it('should return 400 when email is missing', () => {});
it('should send confirmation email after signup', () => {});
it('should update user profile with valid data', () => {});

// ❌ Bad: Vague test names
it('works', () => {});
it('test1', () => {});
it('does something', () => {});
Testing Edge Cases
javascript
// ✅ Good: Test edge cases
describe('String validation', () => {
  it('should handle empty string', () => {});
  it('should handle max length (1000 chars)', () => {});
  it('should handle special characters', () => {});
  it('should handle null and undefined', () => {});
  it('should handle whitespace', () => {});
});
Performance Testing
Load Testing
javascript
// ✅ Good: k6 load test
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '1m', target: 50 }, // Ramp up
    { duration: '3m', target: 50 }, // Steady
    { duration: '1m', target: 0 },  // Ramp down
  ],
};

export default function () {
  const res = http.get('https://example.com/api/users');
  check(res, { 'status was 200': (r) => r.status === 200 });
  sleep(1);
}
Security Testing
Vulnerability Testing
bash
# ✅ Good: SAST (Static Analysis)
npm run test:security

# ✅ Good: Dependency scanning
npm audit

# ✅ Good: OWASP ZAP for DAST
zap-api-scan.py -t https://example.com -r report.html
Continuous Integration
CI Pipeline
yaml
# ✅ Good: GitHub Actions workflow
name: Test
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
      - run: npm ci
      - run: npm test -- --coverage
      - run: npm run build
      - run: npm run test:e2e
Test Checklist
Unit tests written for all new code

Integration tests for API endpoints

E2E tests for critical user paths

Test coverage ≥ 80%

Edge cases tested

Error scenarios tested

Mock external services

Test data isolated (no shared state)

Tests deterministic (always pass)

Pre-commit hooks run tests

CI runs tests on every push

Performance tests for critical APIs

Security scans in CI