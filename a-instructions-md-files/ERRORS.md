# Error Handling & Troubleshooting Guide

## Error Handling Philosophy
- **Never expose internal errors to users**
- **Log all errors with context**
- **Provide meaningful error messages**
- **Graceful degradation**
- **Failing fast vs. failing gracefully**

## Error Types

### Client Errors (400-499)
| Code | Meaning | User Message | Action |
|------|---------|--------------|--------|
| 400 | Bad Request | "Invalid input provided" | Check validation logic |
| 401 | Unauthorized | "Please log in to continue" | Check auth token/session |
| 403 | Forbidden | "You don't have permission" | Check user permissions |
| 404 | Not Found | "Resource not found" | Check URL/route configuration |
| 405 | Method Not Allowed | "Method not allowed" | Check HTTP method |
| 429 | Too Many Requests | "Please slow down" | Check rate limiting |

### Server Errors (500-599)
| Code | Meaning | User Message | Action |
|------|---------|--------------|--------|
| 500 | Internal Server Error | "Something went wrong" | Check server logs |
| 502 | Bad Gateway | "Service temporarily unavailable" | Check upstream service |
| 503 | Service Unavailable | "Under maintenance" | Check service status |
| 504 | Gateway Timeout | "Service timed out" | Check timeout settings |

## Common Error Scenarios

### API Errors
```typescript
// Good error handling
try {
  const response = await fetch('/api/users');
  if (!response.ok) {
    throw new Error(`API error: ${response.status} ${response.statusText}`);
  }
  return await response.json();
} catch (error) {
  console.error('API call failed:', error);
  return { error: 'Failed to fetch users' };
}

Database Errors
typescript
// Good database error handling
try {
  const result = await db.query('SELECT * FROM users WHERE id = $1', [id]);
  return result.rows[0];
} catch (error) {
  console.error('Database error:', error.message);
  throw new DatabaseError('Failed to query user');
}
Validation Errors
typescript
// Good validation
const validateUser = (user) => {
  const errors = [];
  if (!user.email) errors.push('Email is required');
  if (!user.email.includes('@')) errors.push('Invalid email format');
  if (errors.length > 0) {
    return { valid: false, errors };
  }
  return { valid: true, errors: [] };
};
Debugging Strategy
Step-by-Step Debugging
Reproduce: Can you consistently reproduce the issue?

Isolate: What's the minimal case that causes the error?

Log: What does the error log show?

Trace: What's the call stack?

Test: Does the fix pass all tests?

Monitor: Is the fix working in production?

Common Solutions
Problem	Likely Cause	Solution
"Cannot read property of undefined"	Missing data/state	Check data initialization
"Maximum call stack exceeded"	Infinite recursion	Check base case
"Network Error"	API down or CORS	Check API status, CORS config
"Timeout error"	Slow response	Optimize query, increase timeout
"TypeError: ... is not a function"	Wrong import/type	Check imports and types
Logging Strategy
Log Levels
ERROR: Critical issues requiring immediate attention

WARN: Potential issues, not critical

INFO: Important events (user login, successful deploy)

DEBUG: Detailed info for development

TRACE: Very detailed info, for deep debugging

Log Format
javascript
// Structured logging example
const log = {
  timestamp: new Date().toISOString(),
  level: 'ERROR',
  message: 'Database connection failed',
  context: {
    service: 'auth-service',
    userId: '123',
    error: error.message
  }
};
Monitoring & Alerting
Metrics to Monitor
Error Rate: % of requests with errors

Response Time: P50, P95, P99

Resource Usage: CPU, Memory, Disk

Business Metrics: Active users, Revenue

Security: Login attempts, Unauthorized access

Alert Rules
Alert	Condition	Severity	Action
High error rate	>5% in 5 minutes	Critical	Page on-call
Slow response	P95 > 500ms	Warning	Investigate
Memory leak	Increasing memory usage	Warning	Restart service
Security incident	Many failed logins	Critical	Audit logs
Recovery Steps
If Critical Error Occurs
Rollback: Revert to previous working version

Investigate: Check logs and metrics

Fix: Implement hotfix

Test: Verify fix works

Deploy: Redeploy with fix

Communicate: Update stakeholders

Common Recovery Commands
bash
# Git rollback
git revert HEAD
git push origin main

# Database rollback
npx drizzle-kit migrate:down

# Container restart
docker-compose restart

# Cache clear
redis-cli FLUSHALL
Proactive Measures
Error Prevention
TypeScript: Use strong typing

Validation: Validate all inputs

Testing: Comprehensive test suite

Code Review: Peer reviews catch issues

Linting: Use ESLint/TSLint

Static Analysis: SonarQube, Semgrep

Documentation
Known Issues: Documented with workarounds

Error Codes: Map of error codes to solutions

Runbooks: Step-by-step resolution steps

FAQ: Common questions and solutions
