# Express API Patterns (Reference)

Code patterns referenced by `express-api-review`. Prefer matching existing project style when it differs.

## Application factory

Separate `createApp()` from `server.ts` so tests can import the app without binding a port:

```typescript
// app.ts
import express, { Express } from 'express';
import helmet from 'helmet';
import cors from 'cors';
import { errorHandler } from './middleware/errorHandler';
import { requestLogger } from './middleware/requestLogger';
import routes from './routes';

export const createApp = (): Express => {
  const app = express();

  app.use(helmet());
  app.use(cors({ origin: process.env.CORS_ORIGIN?.split(',') ?? [] }));
  app.use(express.json());
  app.use(express.urlencoded({ extended: true }));
  app.use(requestLogger);

  app.use('/api', routes);

  app.use(errorHandler);
  return app;
};
```

```typescript
// server.ts
import { createApp } from './app';

const port = Number(process.env.PORT) || 3000;
createApp().listen(port, () => console.log(`Listening on ${port}`));
```

## Validation middleware (Zod)

```typescript
import { z } from 'zod';
import type { Request, Response, NextFunction } from 'express';

const createUserSchema = z.object({
  body: z.object({
    name: z.string().min(1),
    email: z.string().email(),
    password: z.string().min(8),
  }),
});

export const validateRequest =
  (schema: z.ZodSchema) =>
  async (req: Request, res: Response, next: NextFunction): Promise<void> => {
    try {
      await schema.parseAsync({
        body: req.body,
        query: req.query,
        params: req.params,
      });
      next();
    } catch (error) {
      if (error instanceof z.ZodError) {
        res.status(400).json({ error: 'Validation failed', details: error.errors });
        return;
      }
      next(error);
    }
  };
```

## Parameter validation (IDs)

```typescript
export const validateId = (req: Request, res: Response, next: NextFunction): void => {
  const id = Number(req.params.id);
  if (!Number.isInteger(id) || id <= 0) {
    res.status(400).json({ error: 'Invalid ID' });
    return;
  }
  req.params.id = String(id);
  next();
};
```

## Error types and global handler

```typescript
export class AppError extends Error {
  constructor(
    public statusCode: number,
    public message: string,
    public code: string = 'INTERNAL_ERROR',
  ) {
    super(message);
    this.name = 'AppError';
  }
}

export class NotFoundError extends AppError {
  constructor(resource: string) {
    super(404, `${resource} not found`, 'NOT_FOUND');
  }
}

export const errorHandler = (
  err: Error,
  _req: Request,
  res: Response,
  _next: NextFunction,
): void => {
  if (err instanceof AppError) {
    res.status(err.statusCode).json({ error: err.code, message: err.message });
    return;
  }

  if ((err as { type?: string }).type === 'entity.parse.failed') {
    res.status(400).json({ error: 'INVALID_JSON', message: 'Invalid JSON body' });
    return;
  }

  console.error(err);
  res.status(500).json({
    error: 'INTERNAL_ERROR',
    message: process.env.NODE_ENV === 'production' ? 'An unexpected error occurred' : err.message,
  });
};
```

## Thin controller

```typescript
export const getUser = async (req: Request, res: Response, next: NextFunction): Promise<void> => {
  try {
    const user = await userService.findById(req.params.id);
    if (!user) {
      throw new NotFoundError('User');
    }
    res.status(200).json(user);
  } catch (error) {
    next(error);
  }
};
```

## Rate limiting

```typescript
import rateLimit from 'express-rate-limit';

export const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100,
  standardHeaders: true,
  legacyHeaders: false,
});
```

## Request typing

```typescript
// types/express.d.ts
import type { User } from '../models/User';

declare global {
  namespace Express {
    interface Request {
      user?: User;
      requestId?: string;
    }
  }
}
```
