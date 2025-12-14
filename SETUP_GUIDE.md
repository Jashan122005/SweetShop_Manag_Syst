# Quick Setup Guide

This guide will help you get the Sweet Shop Management System up and running quickly.

## Prerequisites Checklist

- [ ] Node.js v18+ installed
- [ ] npm or yarn installed
- [ ] Supabase account created
- [ ] Git installed

## Step-by-Step Setup

### 1. Supabase Setup

1. Go to [supabase.com](https://supabase.com) and sign in
2. Create a new project
3. Wait for the project to be provisioned (2-3 minutes)
4. Go to Project Settings > API
5. Copy your Project URL and anon public key

### 2. Project Setup

```bash
# Clone the repository
git clone <your-repo-url>
cd sweet-shop-management

# Install dependencies
npm install

# Create environment file
cp .env.example .env
```

### 3. Configure Environment Variables

Edit `.env` and add your Supabase credentials:

```env
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_ANON_KEY=your-anon-key-here
```

### 4. Database Setup

The database migrations have already been applied through the Supabase system. The following tables are ready:
- `profiles` - User profiles with role information
- `sweets` - Sweet inventory

### 5. Create First Admin User

1. Start the development server:
```bash
npm run dev
```

2. Open http://localhost:5173 in your browser

3. Register a new user account

4. Go to your Supabase dashboard > SQL Editor

5. Run this SQL to make your user an admin:
```sql
UPDATE profiles
SET user_type = 'admin'
WHERE email = 'your-email@example.com';
```

6. Refresh the application - you should now see admin features!

### 6. Add Sample Data (Optional)

You can add some sample sweets through the UI, or run this SQL in your Supabase dashboard:

```sql
INSERT INTO sweets (name, category, price, quantity) VALUES
('Chocolate Bar', 'Chocolate', 2.99, 50),
('Gummy Bears', 'Gummy', 1.99, 100),
('Lollipop', 'Candy', 0.99, 200),
('Mint Chocolate', 'Chocolate', 3.49, 30),
('Sour Worms', 'Gummy', 2.49, 75);
```

## Common Issues

### Issue: "Missing Supabase environment variables"
**Solution**: Make sure you've created the `.env` file and added both `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY`.

### Issue: Can't see admin features
**Solution**: Make sure you've run the SQL query to update your user type to 'admin' and refreshed the page.

### Issue: "Failed to fetch sweets"
**Solution**: Check that:
1. Your Supabase project is active
2. Environment variables are correct
3. You're logged in
4. The database tables exist (check Supabase dashboard > Table Editor)

### Issue: CORS errors
**Solution**: This shouldn't happen with the current setup, but if it does:
1. Check that Edge Functions are deployed correctly
2. Verify CORS headers in the Edge Function code
3. Clear browser cache and reload

## Next Steps

1. Try registering a regular user account to test user features
2. Test purchasing sweets
3. As admin, try adding, editing, and deleting sweets
4. Test the search and filter functionality
5. Try restocking items

## Development Workflow

### Running the App
```bash
npm run dev          # Start development server
npm run build        # Build for production
npm run preview      # Preview production build
npm run lint         # Run linter
npm run typecheck    # Type check without building
```

### Git Workflow (with AI Co-authorship)

When making commits, follow this pattern if you used AI assistance:

```bash
git add .
git commit -m "feat: Add user authentication

Implemented login and registration forms using Supabase Auth.
Used AI to generate initial component structure and form validation.

Co-authored-by: Claude AI <claude@anthropic.com>"
```

## Support

For issues or questions:
1. Check the main README.md for detailed documentation
2. Review the code comments
3. Check Supabase dashboard for database issues
4. Review browser console for frontend errors
