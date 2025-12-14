# Git Workflow & AI Co-authorship Guide

This guide explains how to properly use Git for this project, including how to add AI co-authorship to commits.

## Commit Message Convention

We follow the Conventional Commits specification:

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, etc.)
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

### Examples

```bash
feat(auth): Implement user registration

Added registration form with email/password validation.
Integrated with Supabase Auth API.

Co-authored-by: Claude AI <claude@anthropic.com>
```

```bash
test(sweets): Add unit tests for sweet CRUD operations

Implemented tests for create, read, update, and delete operations.
All tests follow TDD principles with proper mocking.

Co-authored-by: Claude AI <claude@anthropic.com>
```

## Adding AI Co-authorship

When you use AI tools (like Claude, ChatGPT, Copilot, etc.) for any part of your commit, you MUST add them as co-authors.

### Format

Add this at the end of your commit message (after two blank lines):

```
Co-authored-by: AI Tool Name <email@example.com>
```

### Common AI Tool Co-authors

```
Co-authored-by: Claude AI <claude@anthropic.com>
Co-authored-by: GitHub Copilot <copilot@github.com>
Co-authored-by: ChatGPT <chatgpt@openai.com>
Co-authored-by: Gemini <gemini@google.com>
```

### When to Add AI Co-authorship

Add AI as co-author when you used it for:
- ✅ Generating boilerplate code
- ✅ Writing tests
- ✅ Debugging assistance
- ✅ Code suggestions
- ✅ Documentation
- ✅ Refactoring suggestions
- ✅ Architecture decisions

Do NOT add AI co-authorship for:
- ❌ Simple typo fixes
- ❌ Minor formatting changes
- ❌ Commits where you wrote 100% of the code yourself

## Complete Example Workflow

### 1. Make Changes
```bash
# Create a new feature branch
git checkout -b feat/search-functionality

# Make your changes
# ... edit files ...
```

### 2. Stage Changes
```bash
git add src/components/SearchBar.tsx
git add src/hooks/useSweets.ts
```

### 3. Commit with Proper Message
```bash
git commit -m "feat(search): Add search and filter functionality

Implemented search bar component with filters for:
- Sweet name (case-insensitive)
- Category
- Price range (min/max)

Used AI assistance for:
- Initial component structure
- Search query optimization
- Filter logic patterns

Co-authored-by: Claude AI <claude@anthropic.com>"
```

### 4. Push and Create PR
```bash
git push origin feat/search-functionality
```

## TDD Workflow with Git

Follow the Red-Green-Refactor cycle with commits:

### Red: Write Failing Test
```bash
git add tests/sweets.test.ts
git commit -m "test(sweets): Add test for creating sweet

Added test that expects sweet creation to return 201 status.
Test currently failing as feature not implemented yet.

Co-authored-by: Claude AI <claude@anthropic.com>"
```

### Green: Make Test Pass
```bash
git add src/api/sweets.ts
git commit -m "feat(sweets): Implement sweet creation endpoint

Added POST endpoint to create new sweets.
Test now passing.

Co-authored-by: Claude AI <claude@anthropic.com>"
```

### Refactor: Improve Code
```bash
git add src/api/sweets.ts
git commit -m "refactor(sweets): Extract validation logic

Moved sweet validation to separate function for reusability.
All tests still passing.

Co-authored-by: Claude AI <claude@anthropic.com>"
```

## Viewing Git History with Co-authors

To see commits with co-authors:

```bash
# View detailed commit history
git log --pretty=full

# View commits with co-authors
git log --format=fuller

# Search for AI-assisted commits
git log --all --grep="Co-authored-by: Claude"
```

## Branch Naming Convention

- `feat/feature-name` - New features
- `fix/bug-description` - Bug fixes
- `test/what-is-tested` - Adding tests
- `docs/what-is-documented` - Documentation
- `refactor/what-is-refactored` - Code refactoring

## Example Git History

Here's what a good commit history looks like:

```
* feat(admin): Add sweet management UI
|   Implemented admin panel with add/edit/delete functionality.
|   Co-authored-by: Claude AI <claude@anthropic.com>
|
* test(admin): Add tests for admin authorization
|   Tests verify only admins can manage sweets.
|   Co-authored-by: Claude AI <claude@anthropic.com>
|
* feat(inventory): Implement restock functionality
|   Added endpoint and UI for restocking sweets.
|   Co-authored-by: Claude AI <claude@anthropic.com>
|
* test(inventory): Add restock operation tests
|   Tests for successful restock and authorization.
|   Co-authored-by: Claude AI <claude@anthropic.com>
```

## Best Practices

1. **Commit Often**: Make small, focused commits
2. **Write Clear Messages**: Explain what and why, not how
3. **Be Honest About AI Usage**: Always credit AI assistance
4. **Test Before Committing**: Ensure tests pass
5. **Review Your Changes**: Use `git diff` before committing

## Commands Cheat Sheet

```bash
# Check status
git status

# View changes
git diff

# Stage files
git add <file>
git add .

# Commit
git commit -m "message"

# Push
git push origin <branch>

# View history
git log --oneline
git log --graph --oneline --all

# Create branch
git checkout -b <branch-name>

# Switch branch
git checkout <branch-name>

# Merge branch
git merge <branch-name>
```

## Remember

Every commit where you used AI should have the AI listed as a co-author. This transparency is:
- ✅ Honest and ethical
- ✅ Shows you understand AI's role
- ✅ Required for this project
- ✅ Good practice for modern development
