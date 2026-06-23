
### 9. - Security Standards & Guidelines
```markdown
# SECURITY.md - Security Best Practices

## Security Principles (OWASP)

### Zero Trust Architecture
- **Never Trust, Always Verify**
- **Assume Breach**
- **Least Privilege**
- **Defense in Depth**

### OWASP Top 10 (2021)
1. **Broken Access Control** - Check permissions
2. **Cryptographic Failures** - Use strong encryption
3. **Injection** - Validate inputs, use parameterized queries
4. **Insecure Design** - Security by design
5. **Security Misconfiguration** - Secure defaults
6. **Vulnerable Components** - Keep dependencies updated
7. **Identification Failures** - Strong authentication
8. **Data Integrity Failures** - Verify data
9. **Security Logging Failures** - Monitor and log
10. **Server-Side Request Forgery** - Validate URLs

## Authentication & Authorization

### Authentication
```typescript
// ✅ Good: Password hashing with bcrypt
import bcrypt from 'bcrypt';
const saltRounds = 12;
const hashedPassword = await bcrypt.hash(password, saltRounds);
const isValid = await bcrypt.compare(password, hashedPassword);

// ✅ Good: JWT with short expiration
const token = jwt.sign(
  { userId: user.id },
  process.env.JWT_SECRET,
  { expiresIn: '15m' }
);

// ✅ Good: Refresh token rotation
const refreshToken = jwt.sign(
  { userId: user.id, version: user.tokenVersion },
  process.env.REFRESH_SECRET,
  { expiresIn: '7d' }
);

// ❌ Bad: Weak password requirements
// ❌ Bad: Storing passwords in plain text
// ❌ Bad: No rate limiting on login attempts
// ❌ Bad: Session tokens in URLs

Authorization
typescript
// ✅ Good: Role-based access control (RBAC)
enum Role {
  ADMIN = 'admin',
  MODERATOR = 'moderator',
  USER = 'user',
}

function hasPermission(user: User, resource: string, action: string) {
  const permissions = {
    admin: ['*'],
    moderator: ['posts:edit', 'posts:delete', 'users:view'],
    user: ['posts:view', 'posts:create', 'profile:edit'],
  };
  return permissions[user.role]?.includes(`${resource}:${action}`);
}

// ✅ Good: Attribute-based access control (ABAC)
function canAccessResource(user: User, resource: Resource) {
  return user.id === resource.ownerId || user.role === 'admin';
}

// ❌ Bad: Hardcoded permissions in middleware
// ❌ Bad: Client-side authorization only
Input Validation & Sanitization
Validation
typescript
// ✅ Good: Zod validation
import { z } from 'zod';

const userSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8).regex(/[A-Z]/, 'Must contain uppercase'),
  username: z.string().alphanumeric().min(3).max(20),
  age: z.number().int().positive().optional(),
});

const validated = userSchema.parse(req.body);

// ✅ Good: Validation on both client and server
// ✅ Good: Whitelist allowed characters
// ✅ Good: Validate file types and sizes
Sanitization
typescript
// ✅ Good: HTML sanitization (DOMPurify)
import DOMPurify from 'dompurify';
const clean = DOMPurify.sanitize(userInput, {
  ALLOWED_TAGS: ['b', 'i', 'em', 'strong', 'p'],
  ALLOWED_ATTR: [],
});

// ✅ Good: SQL injection prevention (parameterized queries)
const result = await db.query(
  'SELECT * FROM users WHERE email = $1',
  [email]
);

// ❌ Bad: SQL injection risk
const result = await db.query(
  `SELECT * FROM users WHERE email = '${email}'`
);
Data Protection
Encryption
typescript
// ✅ Good: AES-GCM for data encryption
import crypto from 'crypto';

function encrypt(text: string): string {
  const iv = crypto.randomBytes(16);
  const cipher = crypto.createCipheriv(
    'aes-256-gcm',
    Buffer.from(process.env.ENCRYPTION_KEY, 'hex'),
    iv
  );
  const encrypted = Buffer.concat([
    cipher.update(text, 'utf8'),
    cipher.final(),
  ]);
  const authTag = cipher.getAuthTag();
  return Buffer.concat([iv, authTag, encrypted]).toString('hex');
}

// ✅ Good: HTTPS for all traffic
// ✅ Good: Secure headers
app.use(helmet());
app.use(helmet.hsts({ maxAge: 31536000 }));
app.use(helmet.referrerPolicy({ policy: 'same-origin' }));
Secrets Management
bash
# ✅ Good: Environment variables
JWT_SECRET=your-secret-key
DATABASE_URL=postgresql://user:pass@localhost/db
S3_ACCESS_KEY=your-access-key

# ❌ Bad: Hardcoded secrets in code
const JWT_SECRET = 'hardcoded-secret';

# ✅ Good: Use Vault or Secrets Manager
import { SecretsManager } from '@aws-sdk/client-secrets-manager';
Network Security
CORS Configuration
typescript
// ✅ Good: Strict CORS policy
app.use(cors({
  origin: ['https://example.com', 'https://sub.example.com'],
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
  allowedHeaders: ['Authorization', 'Content-Type'],
  credentials: true,
  maxAge: 86400,
}));

// ❌ Bad: Allow all origins
app.use(cors({ origin: '*' }));
Rate Limiting
typescript
// ✅ Good: Rate limiting per endpoint
import rateLimit from 'express-rate-limit';

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit each IP to 100 requests
  message: 'Too many requests from this IP',
});

app.use('/api/auth', limiter);
app.use('/api/public', limiter);

// ✅ Good: Specific limits for sensitive endpoints
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5, // 5 login attempts
  message: 'Too many login attempts',
});
Session Management
Secure Sessions
typescript
// ✅ Good: Session configuration
app.use(session({
  secret: process.env.SESSION_SECRET,
  cookie: {
    httpOnly: true,
    secure: process.env.NODE_ENV === 'production',
    sameSite: 'lax',
    maxAge: 24 * 60 * 60 * 1000, // 24 hours
  },
  resave: false,
  saveUninitialized: false,
}));

// ✅ Good: Invalidate sessions on logout
app.post('/logout', (req, res) => {
  req.session.destroy();
  res.clearCookie('session');
});
File Upload Security
Secure Uploads
typescript
// ✅ Good: File upload restrictions
const upload = multer({
  limits: {
    fileSize: 5 * 1024 * 1024, // 5MB limit
    files: 5, // max 5 files
  },
  fileFilter: (req, file, cb) => {
    const allowedTypes = ['image/jpeg', 'image/png', 'image/webp'];
    if (allowedTypes.includes(file.mimetype)) {
      cb(null, true);
    } else {
      cb(new Error('Invalid file type'));
    }
  },
});

// ✅ Good: Scan uploaded files
// ✅ Good: Store files outside web root
// ✅ Good: Use random file names
Dependency Security
Vulnerable Dependencies
bash
# ✅ Good: Regular audits
npm audit
npm audit fix
npm audit fix --force

# ✅ Good: Lock files
# ❌ Bad: Allow automatic dependency updates without review

# ✅ Good: Use Dependabot or Renovate
# ✅ Good: Pin exact versions
Security Headers
Required Headers
typescript
// ✅ Good: Security headers
app.use((req, res, next) => {
  res.setHeader('X-Frame-Options', 'DENY');
  res.setHeader('X-Content-Type-Options', 'nosniff');
  res.setHeader('Referrer-Policy', 'same-origin');
  res.setHeader('Permissions-Policy', 'geolocation=(), microphone=()');
  next();
});

// Content Security Policy (CSP)
app.use(helmet.contentSecurityPolicy({
  directives: {
    defaultSrc: ["'self'"],
    scriptSrc: ["'self'", 'https://trusted-cdn.com'],
    styleSrc: ["'self'", "'unsafe-inline'"],
    imgSrc: ["'self'", 'data:', 'https://images.example.com'],
  },
}));
Monitoring & Incident Response
Security Monitoring
typescript
// ✅ Good: Security event logging
function logSecurityEvent(event: string, details: any) {
  console.log({
    timestamp: new Date().toISOString(),
    event,
    details,
    ip: req.ip,
    userId: req.user?.id,
  });
}

// ✅ Good: Monitor for
// - Multiple failed logins
// - Access denied attempts
// - Suspicious data access
// - Account changes
// - Password resets
Incident Response Plan
Identification: Detect the incident

Containment: Isolate the issue

Eradication: Remove the threat

Recovery: Restore services

Lessons Learned: Prevent recurrence

Communication: Notify affected parties

Security Checklist
Code Security
All user inputs validated and sanitized

SQL injection prevented (parameterized queries)

XSS prevented (sanitization, CSP)

CSRF protection implemented

Secrets in environment variables

Strong password policies

Session management secure

File uploads validated and sanitized

API responses don't expose sensitive data

Error messages don't expose internals

Infrastructure
HTTPS enforced

All services behind firewall/VPC

Rate limiting implemented

DDoS protection in place

Regular backups (encrypted)

Security monitoring/alerting configured

Vulnerabilities scanned regularly

Security headers set

Access logs retained

Incident response plan documented

Authentication
MFA implemented for admin accounts

JWT tokens have short expiration (15m)

Refresh token rotation

Password hashing (bcrypt, salt rounds ≥ 12)

Session invalidation on logout

Account lockout after failed attempts

Password reset with secure tokens

Data Protection
Encryption at rest (database)

Encryption in transit (TLS 1.2+)

Sensitive data masked in logs

Data retention policy defined

PII handling compliant with regulations

User consent obtained for data collection

Third-Party
Dependencies audited for vulnerabilities

API keys rotated regularly

Third-party services have security policies

Contractual data processing agreements

Regular security reviews of third-party services