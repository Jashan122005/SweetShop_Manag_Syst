# Quick Start - Sweet Shop Management System

Get up and running in 5 minutes!

## Prerequisites
- Node.js v18+ installed
- Supabase account (free tier is fine)

## Setup Steps

### 1. Install Dependencies
```bash
npm install
```

### 2. Configure Supabase

1. Go to [supabase.com](https://supabase.com) and create a new project
2. Wait for project initialization (~2 minutes)
3. Go to Project Settings → API
4. Copy your credentials

### 3. Set Environment Variables

```bash
cp .env.example .env
```

Edit `.env` and add:
```env
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_ANON_KEY=your-anon-key-here
```

### 4. Database is Ready!

The database schema has already been applied through Supabase migrations. You should see:
- `profiles` table
- `sweets` table

Both tables have Row Level Security enabled with proper policies.

### 5. Start Development Server

```bash
npm run dev
```

Open [http://localhost:5173](http://localhost:5173)

### 6. Create Your Admin Account

1. Click "Sign up" and create an account
2. Go to your Supabase Dashboard → SQL Editor
3. Run this query (replace with your email):

```sql
UPDATE profiles
SET user_type = 'admin'
WHERE email = 'your-email@example.com';
```

4. Refresh the app - you're now an admin!

### 7. Add Some Sweets

As admin, click "Add Sweet" and create a few items:

**Example Sweet:**
- Name: Chocolate Bar
- Category: Chocolate
- Price: 2.99
- Quantity: 50

### 8. Test the Features

**As Admin:**
- ✅ Add sweets
- ✅ Edit sweets
- ✅ Delete sweets
- ✅ Restock items
- ✅ Search and filter

**As User:**
- ✅ View sweets
- ✅ Search sweets
- ✅ Purchase sweets

## Common Commands

```bash
npm run dev          # Start dev server
npm run build        # Build for production
npm run preview      # Preview production build
npm run typecheck    # Check TypeScript
npm run lint         # Run linter
```

## Project Structure

```
sweet-shop-management/
├── src/
│   ├── components/       # React components
│   ├── contexts/         # Auth context
│   ├── hooks/            # Custom hooks
│   └── lib/              # Supabase client
├── supabase/
│   └── functions/        # Edge Functions (deployed)
└── Documentation files
```

## Key Files

- `src/App.tsx` - Main app component
- `src/contexts/AuthContext.tsx` - Authentication state
- `src/hooks/useSweets.ts` - Sweet operations
- `src/components/Dashboard/Dashboard.tsx` - Main dashboard

## API Endpoints (Already Deployed)

**Sweets:**
- GET `/functions/v1/sweets` - List all
- GET `/functions/v1/sweets/search` - Search
- POST `/functions/v1/sweets` - Create (admin)
- PUT `/functions/v1/sweets/:id` - Update (admin)
- DELETE `/functions/v1/sweets/:id` - Delete (admin)

**Inventory:**
- POST `/functions/v1/inventory/:id/purchase` - Buy
- POST `/functions/v1/inventory/:id/restock` - Restock (admin)

## Troubleshooting

### "Missing Supabase environment variables"
→ Check that `.env` exists and has both variables

### Can't see admin features
→ Run the SQL query to promote your user to admin

### "Failed to fetch sweets"
→ Verify Supabase credentials in `.env`

### CORS errors
→ Edge Functions have CORS enabled by default

## Next Steps

1. **Read the full README.md** for detailed documentation
2. **Check TESTING_GUIDE.md** to learn about TDD approach
3. **Review GIT_WORKFLOW.md** for commit conventions
4. **See DEPLOYMENT_GUIDE.md** when ready to deploy

## Features Overview

### User Features
- Register and login
- Browse all sweets
- Search by name, category, price
- Purchase sweets
- See stock availability

### Admin Features
- All user features +
- Add new sweets
- Edit sweet details
- Delete sweets
- Restock inventory
- View admin badge

## Tech Stack

- **Frontend**: React 18 + TypeScript + Tailwind CSS
- **Backend**: Supabase Edge Functions (Deno)
- **Database**: PostgreSQL (Supabase)
- **Auth**: Supabase Auth (JWT)
- **Build**: Vite
- **Icons**: Lucide React

## Development Tips

1. **Hot Module Replacement**: Changes auto-reload in dev mode
2. **Type Safety**: TypeScript catches errors before runtime
3. **Responsive**: Test on mobile (DevTools → Toggle device toolbar)
4. **Console**: Check browser console for errors

## Sample Data SQL (Optional)

Add sample sweets quickly:

```sql
INSERT INTO sweets (name, category, price, quantity) VALUES
('Chocolate Bar', 'Chocolate', 2.99, 50),
('Gummy Bears', 'Gummy', 1.99, 100),
('Lollipop', 'Candy', 0.99, 200),
('Mint Chocolate', 'Chocolate', 3.49, 30),
('Sour Worms', 'Gummy', 2.49, 75),
('Caramel Candy', 'Candy', 1.49, 60),
('Dark Chocolate', 'Chocolate', 3.99, 40),
('Rainbow Gummies', 'Gummy', 2.99, 90);
```

## Support

For detailed help:
- **Setup Issues**: See `SETUP_GUIDE.md`
- **Testing**: See `TESTING_GUIDE.md`
- **Deployment**: See `DEPLOYMENT_GUIDE.md`
- **Git Workflow**: See `GIT_WORKFLOW.md`
- **Architecture**: See `PROJECT_STRUCTURE.md`

## You're Ready! 🎉

The Sweet Shop Management System is fully functional and ready for development, testing, and deployment.

Start the dev server and explore the application:
```bash
npm run dev
```

Happy coding!
