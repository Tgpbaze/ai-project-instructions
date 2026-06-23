
### 14. `API.md` - API Development Guide
```markdown
# API.md - API Development Standards

## API Design Principles

### RESTful API Guidelines
- **Use nouns for resources** (not verbs)
- **Use HTTP methods correctly**
- **Use standard HTTP status codes**
- **Version your API**
- **Use consistent naming conventions**

### Resource Naming
```javascript
// ✅ Good
GET    /users
GET    /users/{id}
POST   /users
PUT    /users/{id}
DELETE /users/{id}
GET    /users/{id}/posts
GET    /posts?author={id}

// ❌ Bad
GET    /getUsers
POST   /createUser
GET    /user/{id}/posts
GET    /users/active

HTTP Status Codes
Code	Meaning	When to Use
200	OK	Successful GET, PUT, PATCH
201	Created	Successful POST
204	No Content	Successful DELETE
400	Bad Request	Invalid input
401	Unauthorized	Missing/invalid authentication
403	Forbidden	Authenticated but not authorized
404	Not Found	Resource doesn't exist
409	Conflict	Resource already exists
422	Unprocessable Entity	Validation errors
429	Too Many Requests	Rate limiting
500	Internal Server Error	Server error
API Implementation
Express/Node.js API
typescript
// ✅ Good: Express API structure
import express, { Request, Response, NextFunction } from 'express';
import { body, validationResult } from 'express-validator';
import rateLimit from 'express-rate-limit';

const router = express.Router();

// ✅ Good: Rate limiting
const createUserLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 10, // 10 requests
  message: 'Too many account creations from this IP',
});

// ✅ Good: Input validation
router.post(
  '/users',
  createUserLimiter,
  [
    body('email').isEmail().normalizeEmail(),
    body('password').isLength({ min: 8, max: 100 }),
    body('name').isLength({ min: 2, max: 100 }).trim(),
  ],
  async (req: Request, res: Response, next: NextFunction) => {
    // Check validation
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({
        success: false,
        errors: errors.array().map(e => ({
          field: e.param,
          message: e.msg,
        })),
      });
    }

    try {
      const user = await createUser(req.body);
      res.status(201).json({
        success: true,
        data: user,
      });
    } catch (error) {
      if (error.code === 'P2002') { // Unique constraint
        return res.status(409).json({
          success: false,
          error: 'Email already exists',
        });
      }
      next(error);
    }
  }
);

// ✅ Good: Global error handler
app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
  console.error('Error:', err);

  // Don't expose internal errors
  if (err instanceof CustomError) {
    return res.status(err.status).json({
      success: false,
      error: err.message,
    });
  }

  res.status(500).json({
    success: false,
    error: 'Internal server error',
  });
});
API Documentation (OpenAPI)
yaml
# ✅ Good: OpenAPI 3.0 specification
openapi: 3.0.0
info:
  title: User API
  version: 1.0.0
  description: API for managing users

paths:
  /users:
    get:
      summary: List users
      tags:
        - Users
      parameters:
        - name: limit
          in: query
          schema:
            type: integer
            minimum: 1
            maximum: 100
          description: Number of users to return
        - name: offset
          in: query
          schema:
            type: integer
            minimum: 0
          description: Number of users to skip
      responses:
        200:
          description: List of users
          content:
            application/json:
              schema:
                type: object
                properties:
                  data:
                    type: array
                    items:
                      $ref: '#/components/schemas/User'
                  total:
                    type: integer

components:
  schemas:
    User:
      type: object
      required:
        - id
        - email
        - name
      properties:
        id:
          type: string
          format: uuid
        email:
          type: string
          format: email
        name:
          type: string
          minLength: 2
          maxLength: 100
        createdAt:
          type: string
          format: date-time
GraphQL API
Schema Definition
graphql
# ✅ Good: GraphQL schema
type User {
  id: ID!
  email: String!
  name: String!
  posts: [Post!]!
  createdAt: DateTime!
  updatedAt: DateTime!
}

type Post {
  id: ID!
  title: String!
  content: String!
  author: User!
  comments: [Comment!]!
  createdAt: DateTime!
}

type Query {
  user(id: ID!): User
  users(limit: Int = 10, offset: Int = 0): [User!]!
  posts(authorId: ID): [Post!]!
}

type Mutation {
  createUser(input: CreateUserInput!): User!
  updateUser(id: ID!, input: UpdateUserInput!): User!
  deleteUser(id: ID!): Boolean!
}

input CreateUserInput {
  email: String!
  password: String!
  name: String!
}

input UpdateUserInput {
  name: String
  email: String
}
Resolvers
typescript
// ✅ Good: GraphQL resolvers
const resolvers = {
  Query: {
    user: async (_parent, { id }, { dataSources }) => {
      return dataSources.userAPI.getUser(id);
    },
    users: async (_parent, { limit, offset }, { dataSources }) => {
      return dataSources.userAPI.getUsers({ limit, offset });
    },
  },
  Mutation: {
    createUser: async (_parent, { input }, { dataSources }) => {
      return dataSources.userAPI.createUser(input);
    },
  },
  User: {
    posts: async (parent, _args, { dataSources }) => {
      return dataSources.postAPI.getPostsByAuthor(parent.id);
    },
  },
};
GraphQL Best Practices
javascript
// ✅ Good: Error handling
const resolvers = {
  Mutation: {
    createUser: async (_parent, { input }, { dataSources }) => {
      try {
        return await dataSources.userAPI.createUser(input);
      } catch (error) {
        if (error.code === 'DUPLICATE_EMAIL') {
          throw new ApolloError(
            'Email already exists',
            'DUPLICATE_EMAIL'
          );
        }
        throw error;
      }
    },
  },
};

// ✅ Good: Complexity limiting
import { createComplexityLimitRule } from 'graphql-validation-complexity';

const server = new ApolloServer({
  schema,
  validationRules: [
    createComplexityLimitRule(1000, {
      scalarCost: 1,
      objectCost: 5,
    }),
  ],
});

API Security
Authentication
typescript
// ✅ Good: JWT authentication
import jwt from 'jsonwebtoken';

function generateToken(user: User): string {
  return jwt.sign(
    { userId: user.id, email: user.email },
    process.env.JWT_SECRET,
    { expiresIn: '1h' }
  );
}

function verifyToken(token: string): JwtPayload {
  try {
    return jwt.verify(token, process.env.JWT_SECRET) as JwtPayload;
  } catch (error) {
    throw new AuthenticationError('Invalid token');
  }
}

// ✅ Good: Auth middleware
function authMiddleware(req: Request, res: Response, next: NextFunction) {
  const authHeader = req.headers.authorization;
  if (!authHeader) {
    return res.status(401).json({ error: 'No token provided' });
  }

  const token = authHeader.split(' ')[1];
  try {
    const payload = verifyToken(token);
    req.user = payload;
    next();
  } catch (error) {
    return res.status(401).json({ error: 'Invalid token' });
  }
}
API Key Management
typescript
// ✅ Good: API key authentication
function apiKeyMiddleware(req: Request, res: Response, next: NextFunction) {
  const apiKey = req.headers['x-api-key'];
  if (!apiKey) {
    return res.status(401).json({ error: 'API key required' });
  }

  const isValid = validateApiKey(apiKey);
  if (!isValid) {
    return res.status(401).json({ error: 'Invalid API key' });
  }

  next();
}
API Performance
Caching
typescript
// ✅ Good: Redis caching
import Redis from 'ioredis';

const redis = new Redis(process.env.REDIS_URL);

async function getCached<T>(
  key: string,
  fetch: () => Promise<T>,
  ttl = 300
): Promise<T> {
  const cached = await redis.get(key);
  if (cached) {
    return JSON.parse(cached);
  }

  const data = await fetch();
  await redis.setex(key, ttl, JSON.stringify(data));
  return data;
}

// ✅ Good: Cache invalidation
async function invalidateUserCache(userId: string) {
  await redis.del(`user:${userId}`);
  await redis.del(`user:${userId}:posts`);
}
Pagination
typescript
// ✅ Good: Cursor-based pagination
interface PaginationParams {
  limit?: number;
  cursor?: string;
}

interface PaginatedResponse<T> {
  data: T[];
  nextCursor?: string;
  hasMore: boolean;
}

async function getUsersPaginated({ limit = 10, cursor }: PaginationParams) {
  const query = User.find()
    .sort({ createdAt: -1 })
    .limit(limit + 1);

  if (cursor) {
    const decoded = Buffer.from(cursor, 'base64').toString();
    const parsed = JSON.parse(decoded);
    query.where('createdAt').lt(new Date(parsed.createdAt));
  }

  const users = await query;
  const hasMore = users.length > limit;
  if (hasMore) {
    users.pop();
  }

  return {
    data: users,
    hasMore,
    nextCursor: hasMore
      ? Buffer.from(JSON.stringify({ createdAt: users[users.length - 1].createdAt })).toString('base64')
      : undefined,
  };
}
API Testing
Integration Testing
typescript
// ✅ Good: API integration tests
import request from 'supertest';
import app from '../app';

describe('User API', () => {
  let authToken: string;

  beforeAll(async () => {
    // Create test user and get token
    const response = await request(app)
      .post('/auth/login')
      .send({ email: 'test@example.com', password: 'password123' });
    authToken = response.body.token;
  });

  test('GET /users returns 200', async () => {
    const response = await request(app)
      .get('/users')
      .set('Authorization', `Bearer ${authToken}`);
    expect(response.status).toBe(200);
    expect(Array.isArray(response.body.data)).toBe(true);
  });

  test('POST /users validates input', async () => {
    const response = await request(app)
      .post('/users')
      .set('Authorization', `Bearer ${authToken}`)
      .send({ email: 'invalid', password: '123' });
    expect(response.status).toBe(400);
  });
});
API Documentation Checklist
OpenAPI/Swagger documentation

Example requests and responses

Authentication requirements documented

Rate limits documented

Error codes documented

Versioning strategy documented

Deprecation policy documented

Interactive API explorer (Swagger UI)

Postman/Insomnia collection

SDK generated for popular languages

Migration guide for breaking changes

Changelog maintained

API Checklist
Follows REST/GraphQL best practices

Proper HTTP status codes

Input validation and sanitization

Authentication and authorization

Rate limiting implemented

CORS configured

Request/response logging

Error handling (no internal errors exposed)

API versioning

Documentation complete

Tests written (unit + integration)

Performance metrics monitored

Security headers set

Data compression enabled

Pagination for list endpoints

Filtering and sorting supported

Cache headers set

Idempotency for POST/PUT/PATCH

Graceful degradation