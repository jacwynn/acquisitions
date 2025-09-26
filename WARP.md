# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

This is a Node.js/Express.js REST API project called "acquisitions" that implements user authentication and management functionality. The application uses modern JavaScript (ES modules), PostgreSQL with Drizzle ORM, and follows a structured MVC architecture pattern.

## Tech Stack & Key Dependencies

- **Runtime**: Node.js (ES modules with `"type": "module"`)
- **Framework**: Express.js 5.x with modern middleware stack
- **Database**: PostgreSQL via Neon Database (@neondatabase/serverless)
- **ORM**: Drizzle ORM with drizzle-kit for migrations
- **Authentication**: JWT tokens with bcrypt for password hashing
- **Validation**: Zod schemas for request validation
- **Logging**: Winston with file-based logging (logs/combined.log, logs/error.log)
- **Code Quality**: ESLint with Prettier formatting

## Architecture Overview

The codebase follows a clean layered architecture with clear separation of concerns:

```
src/
├── config/          # Configuration (database, logger)
├── controllers/     # Request handlers and route logic
├── middleware/      # Express middleware (not yet implemented)
├── models/          # Drizzle schema definitions
├── routes/          # Express route definitions
├── services/        # Business logic layer
├── utils/           # Helper utilities (JWT, cookies, formatting)
└── validations/     # Zod validation schemas
```

### Key Architecture Patterns

1. **Import Path Aliases**: Uses Node.js `imports` field in package.json for clean imports:
   - `#config/*` → `./src/config/*`
   - `#controllers/*` → `./src/controllers/*`
   - `#models/*` → `./src/models/*`
   - etc.

2. **Service Layer Pattern**: Controllers delegate business logic to service layer (auth.service.js)

3. **Database Layer**: Drizzle ORM with schema-first approach, using Neon's serverless PostgreSQL

4. **Validation Layer**: Zod schemas provide type-safe request validation before controller processing

5. **Logging Strategy**: Structured logging with Winston, separate error and combined logs

## Development Commands

### Core Development
```bash
# Start development server with hot reload
npm run dev

# Run the application
npm start
```

### Code Quality & Formatting
```bash
# Run ESLint
npm run lint

# Fix ESLint issues automatically
npm run lint:fix

# Format code with Prettier
npm run format

# Check if code is properly formatted
npm run format:check
```

### Database Operations
```bash
# Generate new migration from schema changes
npm run db:generate

# Run pending migrations
npm run db:migrate

# Open Drizzle Studio (database GUI)
npm run db:studio
```

## Current Implementation Status

### Completed Features
- User registration endpoint (`POST /api/auth/sign-up`)
- Database schema for users table with Drizzle ORM
- Password hashing with bcrypt
- JWT token generation and cookie management
- Request validation with Zod
- Structured logging with Winston
- Health check endpoints (`/health`, `/api`)

### Authentication Flow
- Sign-up creates user, hashes password, generates JWT, sets HTTP-only cookie
- User model includes: id, name, email, password (hashed), role, timestamps
- Basic role-based access (default: 'user', can be 'admin')

### Known Issues (from logs)
The application shows some authentication service debugging in progress, with recent successful user registrations after resolving password hashing issues.

## Database Schema

### Users Table
```sql
users (
  id: SERIAL PRIMARY KEY,
  name: VARCHAR(255) NOT NULL,
  email: VARCHAR(255) NOT NULL UNIQUE,
  password: VARCHAR(255) NOT NULL,
  role: VARCHAR(50) NOT NULL DEFAULT 'user',
  createdAt: TIMESTAMP DEFAULT NOW() NOT NULL,
  updatedAt: TIMESTAMP DEFAULT NOW() NOT NULL
)
```

## Environment Setup

The application requires a `.env` file with:
- `DATABASE_URL`: Neon PostgreSQL connection string
- `PORT`: Server port (defaults to 3000)
- `LOG_LEVEL`: Winston log level (defaults to 'info')
- `NODE_ENV`: Environment (development/production)

## Code Style Guidelines

- **ES Modules**: All imports/exports use ES module syntax
- **Import Aliases**: Use the defined path aliases (#config/*, #models/*, etc.)
- **Async/Await**: Prefer async/await over Promises for readability
- **Error Handling**: Use try/catch blocks with proper logging via Winston
- **Validation**: All user input must be validated through Zod schemas
- **Database**: Use Drizzle ORM for all database operations
- **Logging**: Use the configured Winston logger for all logging operations

## Security Considerations

- Passwords are hashed using bcrypt with salt rounds of 10
- JWT tokens are stored in HTTP-only cookies
- Database uses prepared statements via Drizzle ORM (SQL injection protection)
- Input validation with Zod schemas before processing
- Helmet.js provides security headers
- CORS configured for cross-origin requests

## Testing

Currently, no test framework is set up. The ESLint configuration includes Jest globals, suggesting tests are planned but not yet implemented.

## Future Development Areas

Based on the current structure, the application appears to be set up for:
- Sign-in functionality (controller exists but incomplete)
- Middleware for authentication/authorization
- Additional user management features
- Role-based access control implementation