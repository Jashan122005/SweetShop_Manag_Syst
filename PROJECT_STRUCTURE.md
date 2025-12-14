# Project Structure

This document provides a comprehensive overview of the Sweet Shop Management System's file structure and organization.

## Directory Tree

```
sweet-shop-management/
├── public/                          # Static assets
├── src/                             # Source code
│   ├── components/                  # React components
│   │   ├── Auth/                    # Authentication components
│   │   │   ├── AuthPage.tsx         # Main auth page wrapper
│   │   │   ├── LoginForm.tsx        # Login form component
│   │   │   └── RegisterForm.tsx     # Registration form component
│   │   └── Dashboard/               # Dashboard components
│   │       ├── Dashboard.tsx        # Main dashboard component
│   │       ├── Header.tsx           # Dashboard header with user info
│   │       ├── SearchBar.tsx        # Search and filter component
│   │       ├── SweetCard.tsx        # Individual sweet display card
│   │       ├── SweetModal.tsx       # Add/Edit sweet modal
│   │       └── RestockModal.tsx     # Restock modal
│   ├── contexts/                    # React contexts
│   │   └── AuthContext.tsx          # Authentication context provider
│   ├── hooks/                       # Custom React hooks
│   │   └── useSweets.ts             # Hook for sweet operations
│   ├── lib/                         # Libraries and utilities
│   │   ├── supabase.ts              # Supabase client configuration
│   │   └── database.types.ts        # TypeScript types for database
│   ├── App.tsx                      # Main app component
│   ├── main.tsx                     # Application entry point
│   └── index.css                    # Global styles (Tailwind)
├── supabase/                        # Supabase configuration
│   └── functions/                   # Edge Functions
│       ├── sweets/                  # Sweets CRUD operations
│       │   └── index.ts
│       └── inventory/               # Inventory management
│           └── index.ts
├── .env.example                     # Environment variables template
├── .gitignore                       # Git ignore rules
├── eslint.config.js                 # ESLint configuration
├── index.html                       # HTML entry point
├── package.json                     # Project dependencies and scripts
├── postcss.config.js                # PostCSS configuration
├── tailwind.config.js               # Tailwind CSS configuration
├── tsconfig.json                    # TypeScript configuration
├── tsconfig.app.json                # TypeScript app configuration
├── tsconfig.node.json               # TypeScript node configuration
├── vite.config.ts                   # Vite build configuration
├── README.md                        # Main project documentation
├── SETUP_GUIDE.md                   # Quick setup instructions
├── TESTING_GUIDE.md                 # Testing documentation
├── GIT_WORKFLOW.md                  # Git and AI co-authorship guide
├── DEPLOYMENT_GUIDE.md              # Deployment instructions
└── PROJECT_STRUCTURE.md             # This file
```

## Core Files Explained

### Frontend Components

#### `src/components/Auth/`
Authentication-related components for user registration and login.

- **AuthPage.tsx**: Main authentication page that toggles between login and registration
- **LoginForm.tsx**: Login form with email/password authentication
- **RegisterForm.tsx**: Registration form with password confirmation

#### `src/components/Dashboard/`
Main application interface after authentication.

- **Dashboard.tsx**: Main dashboard component that orchestrates all dashboard features
- **Header.tsx**: Navigation header with user info and sign out button
- **SearchBar.tsx**: Search and filter interface for sweets
- **SweetCard.tsx**: Display card for individual sweets with action buttons
- **SweetModal.tsx**: Modal dialog for adding/editing sweets (admin only)
- **RestockModal.tsx**: Modal dialog for restocking sweets (admin only)

### State Management

#### `src/contexts/AuthContext.tsx`
Provides authentication state throughout the application.

**Features:**
- User session management
- Profile loading (including user_type)
- Sign up/sign in/sign out functions
- Admin status checking

**Usage:**
```typescript
const { user, profile, isAdmin, signIn, signOut } = useAuth();
```

### Custom Hooks

#### `src/hooks/useSweets.ts`
Encapsulates all sweet-related operations.

**Features:**
- Fetch all sweets
- Add new sweet (admin)
- Update sweet (admin)
- Delete sweet (admin)
- Purchase sweet
- Restock sweet (admin)
- Search/filter sweets

**Usage:**
```typescript
const {
  sweets,
  loading,
  error,
  addSweet,
  updateSweet,
  deleteSweet,
  purchaseSweet,
  restockSweet,
  searchSweets
} = useSweets();
```

### Configuration & Setup

#### `src/lib/supabase.ts`
Configures and exports the Supabase client.

```typescript
import { supabase } from './lib/supabase';
```

#### `src/lib/database.types.ts`
TypeScript types generated from Supabase schema.

Provides type safety for all database operations.

### Backend (Edge Functions)

#### `supabase/functions/sweets/index.ts`
Handles all CRUD operations for sweets.

**Endpoints:**
- `GET /sweets` - List all sweets
- `GET /sweets/search` - Search sweets
- `POST /sweets` - Create sweet (admin)
- `PUT /sweets/:id` - Update sweet (admin)
- `DELETE /sweets/:id` - Delete sweet (admin)

#### `supabase/functions/inventory/index.ts`
Handles inventory management.

**Endpoints:**
- `POST /inventory/:id/purchase` - Purchase sweet
- `POST /inventory/:id/restock` - Restock sweet (admin)

## Key Patterns & Conventions

### Component Structure

```typescript
// 1. Imports
import { useState } from 'react';
import { ComponentDependency } from './ComponentDependency';

// 2. Type Definitions
interface ComponentProps {
  // prop types
}

// 3. Component Definition
export function Component({ prop }: ComponentProps) {
  // 4. State
  const [state, setState] = useState();

  // 5. Effects & Handlers
  const handleAction = () => {
    // handler logic
  };

  // 6. Render
  return (
    // JSX
  );
}
```

### Naming Conventions

- **Components**: PascalCase (e.g., `SweetCard.tsx`)
- **Hooks**: camelCase with 'use' prefix (e.g., `useSweets.ts`)
- **Contexts**: PascalCase with 'Context' suffix (e.g., `AuthContext.tsx`)
- **Utilities**: camelCase (e.g., `formatPrice.ts`)
- **Types**: PascalCase (e.g., `Sweet`, `Profile`)
- **Constants**: UPPER_SNAKE_CASE (e.g., `API_URL`)

### File Organization

```
Feature-based organization:
components/
  FeatureName/
    FeatureName.tsx       (Main component)
    FeatureCard.tsx       (Sub-component)
    FeatureModal.tsx      (Related component)
    useFeature.ts         (Feature-specific hook)
```

## Data Flow

### Authentication Flow

```
1. User submits login form
   └─> LoginForm.tsx
       └─> AuthContext.signIn()
           └─> Supabase Auth API
               └─> Profile loaded from database
                   └─> User state updated
                       └─> App.tsx rerenders with Dashboard
```

### Sweet Management Flow (Admin)

```
1. Admin clicks "Add Sweet"
   └─> Dashboard.tsx
       └─> Opens SweetModal.tsx
           └─> Admin fills form
               └─> Calls useSweets.addSweet()
                   └─> Edge Function: POST /sweets
                       └─> Database insert with RLS check
                           └─> Returns new sweet
                               └─> useSweets refetches all sweets
                                   └─> Dashboard updates
```

### Purchase Flow (User)

```
1. User clicks "Purchase"
   └─> SweetCard.tsx
       └─> Calls useSweets.purchaseSweet()
           └─> Edge Function: POST /inventory/:id/purchase
               └─> Checks stock availability
                   └─> Updates quantity in database
                       └─> Returns updated sweet
                           └─> useSweets refetches all sweets
                               └─> Dashboard updates with new stock
```

## State Management Strategy

### Global State (Context)
- Authentication state (user, profile, session)
- Available to all components via `useAuth()`

### Local State (useState)
- Component-specific UI state
- Form inputs
- Modal visibility
- Loading states

### Server State (Custom Hooks)
- Database-backed data (sweets)
- Managed by `useSweets()`
- Automatic refetching after mutations

## Styling Approach

### Tailwind CSS
- Utility-first CSS framework
- Responsive design with breakpoints
- Custom color palette
- Consistent spacing scale

### Component Styling Pattern
```tsx
<div className="
  bg-white              // Background
  rounded-lg            // Border radius
  shadow-md             // Shadow
  p-6                   // Padding
  hover:shadow-xl       // Hover state
  transition-shadow     // Transition
  duration-300          // Duration
">
```

## Environment Variables

### Required Variables
```env
VITE_SUPABASE_URL=        # Supabase project URL
VITE_SUPABASE_ANON_KEY=   # Supabase anonymous key
```

### Usage in Code
```typescript
const url = import.meta.env.VITE_SUPABASE_URL;
```

## Database Schema

### Tables

**profiles**
```sql
id          uuid (PK)
email       text
user_type   text ('user' | 'admin')
created_at  timestamptz
updated_at  timestamptz
```

**sweets**
```sql
id          uuid (PK)
name        text
category    text
price       numeric
quantity    integer
created_at  timestamptz
updated_at  timestamptz
created_by  uuid (FK -> profiles)
```

## Build & Development

### Development Server
```bash
npm run dev
# Runs on http://localhost:5173
```

### Production Build
```bash
npm run build
# Output: dist/
```

### Type Checking
```bash
npm run typecheck
```

### Linting
```bash
npm run lint
```

## Testing Structure

```
src/
  components/
    __tests__/
      Component.test.tsx
  hooks/
    __tests__/
      useHook.test.ts
```

## Adding New Features

### 1. Create Component
```
src/components/FeatureName/
  FeatureName.tsx
```

### 2. Add Types
```typescript
// In database.types.ts or component file
interface FeatureData {
  // ...
}
```

### 3. Create Hook (if needed)
```
src/hooks/
  useFeature.ts
```

### 4. Add Edge Function (if needed)
```
supabase/functions/feature-name/
  index.ts
```

### 5. Update Database (if needed)
```sql
-- Create migration in Supabase
```

## Security Considerations

### Frontend Security
- Environment variables prefixed with `VITE_`
- No sensitive data in client code
- JWT tokens handled by Supabase Auth
- XSS protection via React's built-in escaping

### Backend Security
- Row Level Security (RLS) on all tables
- JWT verification on all Edge Functions
- Admin-only operations checked server-side
- Input validation on all mutations

## Performance Optimizations

### Code Splitting
- React lazy loading for routes
- Dynamic imports for large components

### Asset Optimization
- Vite's automatic code splitting
- CSS minification
- Tree shaking

### Caching Strategy
- Browser cache for static assets
- Service worker (optional)
- Supabase connection pooling

## Troubleshooting

### Common File Locations

**Environment Issues:**
- Check: `.env` file in root
- Verify: Variable names start with `VITE_`

**Type Errors:**
- Check: `src/lib/database.types.ts`
- Run: `npm run typecheck`

**Import Errors:**
- Check: `tsconfig.json` paths
- Verify: File extensions included in imports

**Build Errors:**
- Check: `vite.config.ts`
- Run: `npm run build` locally
- Review: Console errors

## Additional Resources

- Component examples in `src/components/`
- Database schema in Supabase dashboard
- API endpoints in `supabase/functions/`
- Setup instructions in `SETUP_GUIDE.md`
- Deployment guide in `DEPLOYMENT_GUIDE.md`
