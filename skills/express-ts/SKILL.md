---
name: express-ts
description: |
  Production Node.js backend architecture using Express, TypeScript, and modern middleware patterns.
  Covers Zod request validation, centralized error handling (Custom HttpErrors), AsyncHandler wrapper,
  structured logging (Pino/Winston), Helmet/CORS security, rate limiting, and graceful shutdown handling.

  Use when developing, architecting, or refactoring Node.js/TypeScript REST APIs with Express.
license: MIT
metadata:
  version: v1
---

# Express & TypeScript Production API Standards

Architect reliable, secure, high-performance Node.js REST APIs with Express and TypeScript.

---

## 1. Directory Structure

```
src/
├── config/             # Environment variables (Zod validated)
├── controllers/        # Request handling and HTTP status codes
├── middlewares/        # Auth, validation, rate limiting, error handling
├── routes/             # Express router declarations
├── services/           # Core business logic
├── types/              # Custom TS interfaces & Express module declarations
├── utils/              # Custom error classes, logger, async handler
├── app.ts              # Express app setup & middleware pipeline
└── server.ts           # Server listener & graceful shutdown handlers
```

---

## 2. Type-Safe Environment Configuration

```ts
// src/config/env.ts
import dotenv from 'dotenv';
import { z } from 'zod';

dotenv.config();

const envSchema = z.object({
  NODE_ENV: z.enum(['development', 'production', 'test']).default('development'),
  PORT: z.string().transform(Number).default('4000'),
  DATABASE_URL: z.string().url(),
  JWT_SECRET: z.string().min(32),
  CORS_ORIGIN: z.string().default('*'),
});

export const env = envSchema.parse(process.env);
```

---

## 3. Zod Request Validation Middleware

```ts
// src/middlewares/validate.ts
import { Request, Response, NextFunction } from 'express';
import { AnyZodObject, ZodError } from 'zod';

export const validate = (schema: AnyZodObject) => {
  return async (req: Request, res: Response, next: NextFunction) => {
    try {
      req.body = await schema.parseAsync(req.body);
      next();
    } catch (error) {
      if (error instanceof ZodError) {
        return res.status(400).json({
          status: 'error',
          message: 'Validation failed',
          errors: error.errors.map(err => ({
            field: err.path.join('.'),
            message: err.message,
          })),
        });
      }
      next(error);
    }
  };
};
```

---

## 4. Centralized Error Handling & AsyncHandler

```ts
// src/utils/errors.ts
export class AppError extends Error {
  constructor(
    public statusCode: number,
    public message: string,
    public isOperational = true
  ) {
    super(message);
    Object.setPrototypeOf(this, new.target.prototype);
  }
}

// src/utils/asyncHandler.ts
import { Request, Response, NextFunction, RequestHandler } from 'express';

export const asyncHandler = (fn: RequestHandler) => {
  return (req: Request, res: Response, next: NextFunction) => {
    Promise.resolve(fn(req, res, next)).catch(next);
  };
};

// src/middlewares/errorHandler.ts
export const errorHandler = (
  err: Error,
  _req: Request,
  res: Response,
  _next: NextFunction
) => {
  if (err instanceof AppError) {
    return res.status(err.statusCode).json({
      status: 'error',
      message: err.message,
    });
  }

  console.error('Unhandled error:', err);
  return res.status(500).json({
    status: 'error',
    message: 'Internal server error',
  });
};
```

---

## 5. Security & Graceful Shutdown

```ts
// src/server.ts
import http from 'http';
import { app } from './app';
import { env } from './config/env';

const server = http.createServer(app);

server.listen(env.PORT, () => {
  console.log(`Server running on port ${env.PORT} in ${env.NODE_ENV} mode`);
});

const gracefulShutdown = (signal: string) => {
  console.log(`${signal} received. Closing HTTP server gracefully...`);
  server.close(async () => {
    console.log('HTTP server closed.');
    // Close DB pools / Redis connections here
    process.exit(0);
  });
};

process.on('SIGTERM', () => gracefulShutdown('SIGTERM'));
process.on('SIGINT', () => gracefulShutdown('SIGINT'));
```
