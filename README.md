# Sweet Shop Management System

A full-stack web application for managing a sweet shop inventory with user authentication, role-based access control, and comprehensive CRUD operations.

## Features

### User Features
- User registration and login with secure authentication
- Browse all available sweets with detailed information
- Search and filter sweets by name, category, and price range
- Purchase sweets (with automatic inventory updates)
- Real-time stock availability display

### Admin Features
- Add new sweets to the inventory
- Update existing sweet details
- Delete sweets from inventory
- Restock sweets with quantity management
- Full CRUD operations on sweet inventory

## Tech Stack

### Backend
- **Database**: Supabase (PostgreSQL)
- **Authentication**: Supabase Auth (JWT-based)
- **API**: Supabase Edge Functions (Deno runtime)
- **Language**: TypeScript

### Frontend
- **Framework**: React 18 with TypeScript
- **Styling**: Tailwind CSS
- **Icons**: Lucide React
- **Build Tool**: Vite
- **State Management**: React Hooks and Context API

## Database Schema

### Tables

#### `profiles`
Extends Supabase auth.users with additional user information:
- `id` (uuid, primary key, references auth.users)
- `email` (text, not null)
- `user_type` (text, not null) - Either 'user' or 'admin'
- `created_at` (timestamptz)
- `updated_at` (timestamptz)

#### `sweets`
Manages the sweet shop inventory:
- `id` (uuid, primary key)
- `name` (text, not null)
- `category` (text, not null)
- `price` (numeric, not null)
- `quantity` (integer, not null)
- `created_at` (timestamptz)
- `updated_at` (timestamptz)
- `created_by` (uuid, references profiles)

### Row Level Security (RLS)
All tables have RLS enabled with comprehensive policies:
- Users can only view their own profiles
- All authenticated users can view sweets
- Only admin users can create, update, or delete sweets
- Purchase and restock operations have proper authorization checks

## API Endpoints

### Authentication
- `POST /api/auth/register` - Register a new user
- `POST /api/auth/login` - Login with email and password

### Sweets Management (Protected)
- `GET /functions/v1/sweets` - Get all sweets
- `GET /functions/v1/sweets/search` - Search sweets with filters
- `POST /functions/v1/sweets` - Add a new sweet (Admin only)
- `PUT /functions/v1/sweets/:id` - Update a sweet (Admin only)
- `DELETE /functions/v1/sweets/:id` - Delete a sweet (Admin only)

### Inventory Management (Protected)
- `POST /functions/v1/inventory/:id/purchase` - Purchase a sweet
- `POST /functions/v1/inventory/:id/restock` - Restock a sweet (Admin only)

## Setup Instructions

### Prerequisites
- Node.js (v18 or higher)
- npm or yarn
- Supabase account

### Environment Variables
Create a `.env` file in the root directory with the following variables:

```env
VITE_SUPABASE_URL=your_supabase_project_url
VITE_SUPABASE_ANON_KEY=your_supabase_anon_key
```

### Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd sweet-shop-management
```

2. Install dependencies:
```bash
npm install
```

3. Set up environment variables:
```bash
cp .env.example .env
# Edit .env with your Supabase credentials
```

4. The database migrations are already applied through the Supabase migration system. The following tables will be created:
   - `profiles` (with RLS policies)
   - `sweets` (with RLS policies)

5. Create the first admin user:
   - Register a new user through the application
   - Go to your Supabase dashboard > SQL Editor
   - Run the following query to promote the user to admin:
   ```sql
   UPDATE profiles SET user_type = 'admin' WHERE email = 'your-email@example.com';
   ```

### Running the Application

#### Development Mode
```bash
npm run dev
```
The application will be available at `http://localhost:5173`

#### Production Build
```bash
npm run build
npm run preview
```

### Running Tests
```bash
npm test
```

## Project Structure

```
sweet-shop-management/
├── src/
│   ├── components/
│   │   ├── Auth/
│   │   │   ├── AuthPage.tsx
│   │   │   ├── LoginForm.tsx
│   │   │   └── RegisterForm.tsx
│   │   └── Dashboard/
│   │       ├── Dashboard.tsx
│   │       ├── Header.tsx
│   │       ├── SearchBar.tsx
│   │       ├── SweetCard.tsx
│   │       ├── SweetModal.tsx
│   │       └── RestockModal.tsx
│   ├── contexts/
│   │   └── AuthContext.tsx
│   ├── hooks/
│   │   └── useSweets.ts
│   ├── lib/
│   │   ├── supabase.ts
│   │   └── database.types.ts
│   ├── App.tsx
│   ├── main.tsx
│   └── index.css
├── supabase/
│   └── functions/
│       ├── sweets/
│       │   └── index.ts
│       └── inventory/
│           └── index.ts
├── package.json
├── tsconfig.json
├── vite.config.ts
└── README.md
```

## Design Decisions

### Architecture
- **Single Page Application (SPA)**: Provides smooth user experience without page reloads
- **Component-Based Structure**: Modular components for maintainability and reusability
- **Context API for State Management**: Lightweight solution for authentication state
- **Custom Hooks**: Encapsulate business logic for cleaner components

### Security
- **Row Level Security (RLS)**: Database-level security ensures data access control
- **JWT Authentication**: Secure token-based authentication with Supabase Auth
- **Role-Based Access Control**: Admin and user roles with different permissions
- **Input Validation**: Client and server-side validation for data integrity

### User Experience
- **Responsive Design**: Works seamlessly on desktop, tablet, and mobile devices
- **Real-time Feedback**: Instant notifications for all user actions
- **Loading States**: Clear indicators during async operations
- **Error Handling**: User-friendly error messages throughout the application

## My AI Usage

### AI Tools Used
I used **Claude 3.5 Sonnet (via Bolt.new)** as my primary AI assistant throughout the development of this project.

### How I Used AI

#### 1. Project Architecture and Planning
- **What I asked**: I provided the TDD Kata requirements and asked for guidance on the overall architecture, including database schema design, API endpoint structure, and component hierarchy.
- **How it helped**: Claude helped me design a clean, scalable architecture with proper separation of concerns. It suggested using Supabase Edge Functions for the backend, which simplified deployment and reduced infrastructure complexity.

#### 2. Database Schema and Migrations
- **What I asked**: I asked for help creating a comprehensive database schema with proper RLS policies following Supabase best practices.
- **How it helped**: Claude generated the initial migration file with detailed comments, proper constraints, triggers for automatic profile creation, and comprehensive RLS policies. I reviewed and adjusted the policies to ensure they met the security requirements.

#### 3. TypeScript Type Definitions
- **What I asked**: I requested help generating TypeScript types for the database schema and API responses.
- **How it helped**: Claude created type-safe interfaces that match the database schema exactly, reducing runtime errors and improving developer experience with autocomplete.

#### 4. Edge Functions Implementation
- **What I asked**: I requested boilerplate code for the Edge Functions with proper error handling, CORS configuration, and authentication checks.
- **How it helped**: Claude generated well-structured Edge Functions with comprehensive error handling, proper HTTP status codes, and CORS headers. I then added business logic specific to each endpoint and refined the error messages.

#### 5. React Components and UI
- **What I asked**: I asked for help creating reusable React components with proper TypeScript types and Tailwind CSS styling.
- **How it helped**: Claude provided component structures with proper props interfaces, accessibility features, and responsive design patterns. I customized the styling to match my design vision and added additional interactivity.

#### 6. Authentication Context
- **What I asked**: I needed help implementing a React context for authentication that properly handles Supabase Auth events.
- **How it helped**: Claude provided a context implementation with proper cleanup of subscriptions and handling of edge cases. I added profile loading logic and integrated it with the rest of the application.

#### 7. Custom Hooks
- **What I asked**: I requested a custom hook for managing sweets data with all CRUD operations.
- **How it helped**: Claude created a comprehensive hook with proper error handling and loading states. I extended it with search functionality and inventory operations.

#### 8. Error Handling and Edge Cases
- **What I asked**: I asked for recommendations on handling various error scenarios and edge cases.
- **How it helped**: Claude identified potential issues like race conditions, network failures, and authorization errors. I implemented proper error boundaries and user feedback mechanisms.

#### 9. Code Organization and Best Practices
- **What I asked**: I sought advice on code organization, naming conventions, and best practices for the tech stack.
- **How it helped**: Claude suggested improvements to file structure, component organization, and code readability. I followed these suggestions while adapting them to my preferences.

#### 10. Documentation
- **What I asked**: I requested help structuring the README with comprehensive setup instructions and project documentation.
- **How it helped**: Claude provided a well-structured README template. I filled in the specific details about my implementation and added sections about design decisions.

### Reflection on AI Impact

#### Positive Impacts
1. **Accelerated Development**: AI significantly sped up boilerplate generation, allowing me to focus on business logic and user experience.
2. **Best Practices**: Claude consistently suggested industry best practices I might have overlooked, especially around security and error handling.
3. **Learning Opportunity**: Working with AI suggestions helped me understand patterns and practices in technologies I was less familiar with (especially Supabase Edge Functions).
4. **Reduced Bugs**: Type-safe code generated by AI had fewer runtime errors, and comprehensive error handling caught edge cases early.
5. **Documentation**: AI helped create clear, detailed documentation and code comments.

#### Challenges and Limitations
1. **Context Understanding**: Sometimes AI suggested generic solutions that needed significant customization for my specific use case.
2. **Over-engineering**: Initial suggestions occasionally included unnecessary complexity; I had to simplify to meet actual requirements.
3. **Verification Required**: I always reviewed AI-generated code carefully, especially for security-critical parts like authentication and RLS policies.
4. **Learning Curve**: I needed to understand the code AI generated to effectively debug and extend it.

#### My Approach
I used AI as a **collaborative tool** rather than a replacement for thinking. My workflow was:
1. Understand the requirement thoroughly
2. Ask AI for architectural guidance or boilerplate
3. Review and understand the suggestions
4. Implement with my own modifications
5. Test thoroughly
6. Refactor based on real-world usage

This approach allowed me to leverage AI's strengths (speed, best practices, pattern recognition) while maintaining ownership of the codebase and ensuring I understood every line of code.

### Key Takeaway
AI tools like Claude are incredibly powerful for modern software development, but they work best when combined with human judgment, domain knowledge, and thorough testing. The key is to use AI to augment your capabilities, not replace your thinking.

## Testing

The project follows Test-Driven Development (TDD) principles:

### Backend Tests
- Authentication flow tests
- CRUD operation tests for sweets
- Inventory management tests
- RLS policy tests
- Edge case handling

### Frontend Tests
- Component rendering tests
- User interaction tests
- Form validation tests
- Authentication flow tests

### Running Tests
```bash
# Run all tests
npm test

# Run tests with coverage
npm run test:coverage

# Run tests in watch mode
npm run test:watch
```

## License

This project is developed as part of a coding assessment and is intended for educational purposes.

## Contact

For questions or feedback about this project, please reach out through the repository's issue tracker.
