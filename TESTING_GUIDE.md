# Testing Guide

This guide covers the testing strategy and best practices for the Sweet Shop Management System.

## Testing Stack

### Frontend Testing
- **Framework**: Vitest (recommended for Vite projects)
- **React Testing**: @testing-library/react
- **User Interactions**: @testing-library/user-event
- **Mocking**: vitest/mock

### Backend Testing
- **Edge Functions**: Deno's built-in test runner
- **Database**: Supabase test helpers
- **API Testing**: supertest or fetch

## Test-Driven Development (TDD) Workflow

### The Red-Green-Refactor Cycle

1. **RED**: Write a failing test first
2. **GREEN**: Write minimal code to make the test pass
3. **REFACTOR**: Improve the code while keeping tests green

### Example TDD Workflow

#### Step 1: Write Failing Test (RED)

```typescript
// src/hooks/__tests__/useSweets.test.ts
import { describe, it, expect, beforeEach } from 'vitest';
import { renderHook, waitFor } from '@testing-library/react';
import { useSweets } from '../useSweets';

describe('useSweets', () => {
  it('should fetch sweets on mount', async () => {
    const { result } = renderHook(() => useSweets());

    expect(result.current.loading).toBe(true);

    await waitFor(() => {
      expect(result.current.loading).toBe(false);
      expect(result.current.sweets).toHaveLength(0);
    });
  });
});
```

Commit:
```bash
git commit -m "test(sweets): Add test for fetching sweets on mount

Test expects hook to fetch sweets when component mounts.
Currently failing - hook not implemented yet.

Co-authored-by: Claude AI <claude@anthropic.com>"
```

#### Step 2: Make Test Pass (GREEN)

```typescript
// src/hooks/useSweets.ts
export function useSweets() {
  const [sweets, setSweets] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const fetchSweets = async () => {
      const { data } = await supabase.from('sweets').select('*');
      setSweets(data || []);
      setLoading(false);
    };
    fetchSweets();
  }, []);

  return { sweets, loading };
}
```

Commit:
```bash
git commit -m "feat(sweets): Implement basic sweet fetching

Added useSweets hook that fetches sweets on mount.
Test now passing.

Co-authored-by: Claude AI <claude@anthropic.com>"
```

#### Step 3: Refactor (REFACTOR)

```typescript
// src/hooks/useSweets.ts
export function useSweets() {
  const [sweets, setSweets] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  const fetchSweets = useCallback(async () => {
    try {
      setLoading(true);
      setError(null);
      const { data, error: fetchError } = await supabase
        .from('sweets')
        .select('*')
        .order('created_at', { ascending: false });

      if (fetchError) throw fetchError;
      setSweets(data || []);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  }, []);

  useEffect(() => {
    fetchSweets();
  }, [fetchSweets]);

  return { sweets, loading, error, fetchSweets };
}
```

Commit:
```bash
git commit -m "refactor(sweets): Add error handling and improve code organization

- Added error state for better error handling
- Used useCallback for fetchSweets memoization
- Added proper error catching and finally block
- All tests still passing

Co-authored-by: Claude AI <claude@anthropic.com>"
```

## Test Categories

### Unit Tests
Test individual functions and components in isolation.

```typescript
// src/utils/__tests__/validation.test.ts
describe('validateSweet', () => {
  it('should validate sweet with correct data', () => {
    const sweet = {
      name: 'Chocolate',
      category: 'Candy',
      price: 2.99,
      quantity: 10
    };

    expect(validateSweet(sweet)).toBe(true);
  });

  it('should reject sweet with negative price', () => {
    const sweet = {
      name: 'Chocolate',
      category: 'Candy',
      price: -1,
      quantity: 10
    };

    expect(validateSweet(sweet)).toBe(false);
  });
});
```

### Integration Tests
Test how components work together.

```typescript
// src/components/__tests__/Dashboard.integration.test.tsx
describe('Dashboard Integration', () => {
  it('should display sweets after successful fetch', async () => {
    render(
      <AuthProvider>
        <Dashboard />
      </AuthProvider>
    );

    await waitFor(() => {
      expect(screen.getByText('Chocolate Bar')).toBeInTheDocument();
      expect(screen.getByText('Gummy Bears')).toBeInTheDocument();
    });
  });
});
```

### End-to-End Tests
Test complete user workflows.

```typescript
// e2e/purchase-flow.test.ts
describe('Purchase Flow', () => {
  it('should allow user to purchase a sweet', async () => {
    // Login
    await page.goto('/');
    await page.fill('[name="email"]', 'user@test.com');
    await page.fill('[name="password"]', 'password');
    await page.click('button[type="submit"]');

    // Find and purchase sweet
    await page.waitForSelector('.sweet-card');
    const initialStock = await page.textContent('.stock-quantity');
    await page.click('button:has-text("Purchase")');

    // Verify stock decreased
    await page.waitForTimeout(1000);
    const newStock = await page.textContent('.stock-quantity');
    expect(parseInt(newStock)).toBe(parseInt(initialStock) - 1);
  });
});
```

## Testing Best Practices

### 1. AAA Pattern (Arrange-Act-Assert)

```typescript
it('should add a new sweet', async () => {
  // Arrange
  const newSweet = {
    name: 'Lollipop',
    category: 'Candy',
    price: 0.99,
    quantity: 100
  };

  // Act
  const result = await addSweet(newSweet);

  // Assert
  expect(result.success).toBe(true);
  expect(result.data.name).toBe('Lollipop');
});
```

### 2. Descriptive Test Names

```typescript
// ❌ Bad
it('test sweet creation', () => { ... });

// ✅ Good
it('should create a sweet with valid data and return 201 status', () => { ... });
```

### 3. Test One Thing at a Time

```typescript
// ❌ Bad - Testing multiple things
it('should handle sweets', () => {
  expect(createSweet()).toBeTruthy();
  expect(updateSweet()).toBeTruthy();
  expect(deleteSweet()).toBeTruthy();
});

// ✅ Good - Separate tests
it('should create a sweet successfully', () => { ... });
it('should update a sweet successfully', () => { ... });
it('should delete a sweet successfully', () => { ... });
```

### 4. Mock External Dependencies

```typescript
import { vi } from 'vitest';

// Mock Supabase
vi.mock('../lib/supabase', () => ({
  supabase: {
    from: vi.fn(() => ({
      select: vi.fn(() => Promise.resolve({ data: mockSweets }))
    }))
  }
}));
```

### 5. Test Edge Cases

```typescript
describe('purchaseSweet', () => {
  it('should handle successful purchase', async () => { ... });

  it('should reject purchase when out of stock', async () => { ... });

  it('should reject purchase with invalid quantity', async () => { ... });

  it('should handle network errors gracefully', async () => { ... });

  it('should reject unauthorized purchases', async () => { ... });
});
```

## Test Coverage Goals

Aim for the following coverage:

- **Critical paths**: 100% (auth, payments, inventory)
- **Business logic**: 90%+
- **UI components**: 80%+
- **Utility functions**: 100%

## Running Tests

```bash
# Run all tests
npm test

# Run tests in watch mode
npm run test:watch

# Run tests with coverage
npm run test:coverage

# Run specific test file
npm test src/hooks/__tests__/useSweets.test.ts

# Run tests matching pattern
npm test -- --grep="purchase"
```

## Example Test Suite Structure

```
src/
├── components/
│   └── __tests__/
│       ├── LoginForm.test.tsx
│       ├── SweetCard.test.tsx
│       └── Dashboard.test.tsx
├── hooks/
│   └── __tests__/
│       └── useSweets.test.ts
├── contexts/
│   └── __tests__/
│       └── AuthContext.test.tsx
└── lib/
    └── __tests__/
        └── api.test.ts
```

## Sample Test Commands in package.json

```json
{
  "scripts": {
    "test": "vitest run",
    "test:watch": "vitest",
    "test:coverage": "vitest run --coverage",
    "test:ui": "vitest --ui"
  }
}
```

## Common Testing Patterns

### Testing Async Operations

```typescript
it('should fetch sweets asynchronously', async () => {
  const { result } = renderHook(() => useSweets());

  await waitFor(() => {
    expect(result.current.sweets).toHaveLength(5);
  });
});
```

### Testing Error States

```typescript
it('should handle fetch errors', async () => {
  vi.mocked(supabase.from).mockRejectedValueOnce(new Error('Network error'));

  const { result } = renderHook(() => useSweets());

  await waitFor(() => {
    expect(result.current.error).toBe('Network error');
  });
});
```

### Testing User Interactions

```typescript
it('should call onPurchase when button clicked', async () => {
  const onPurchase = vi.fn();

  render(<SweetCard sweet={mockSweet} onPurchase={onPurchase} />);

  const button = screen.getByRole('button', { name: /purchase/i });
  await userEvent.click(button);

  expect(onPurchase).toHaveBeenCalledWith(mockSweet.id);
});
```

## Resources

- [Vitest Documentation](https://vitest.dev/)
- [React Testing Library](https://testing-library.com/react)
- [TDD Best Practices](https://testdriven.io/)
- [Jest/Vitest Matchers](https://vitest.dev/api/expect.html)

## Remember

- Write tests before implementation (TDD)
- Keep tests simple and focused
- Test behavior, not implementation
- Mock external dependencies
- Aim for high coverage on critical paths
- Run tests before committing
- Keep tests maintainable
