# Contributing to OpsPilot AI

## Welcome!

We're thrilled you're interested in contributing to OpsPilot AI! This document provides guidelines and instructions for contributing.

## Code of Conduct

Please read and adhere to our [Code of Conduct](../CODE_OF_CONDUCT.md) in all interactions.

## How to Contribute

### Reporting Bugs

1. **Check existing issues** to avoid duplicates
2. **Create a new issue** with:
   - Clear title describing the bug
   - Description of expected vs actual behavior
   - Steps to reproduce
   - Screenshots/logs if applicable
   - Your environment (OS, Node version, etc.)

### Suggesting Features

1. **Check existing issues** to see if already proposed
2. **Create a new issue** with:
   - Feature title
   - Problem it solves
   - Proposed solution
   - Alternative approaches
   - Additional context

### Code Contributions

1. **Fork the repository**
2. **Create a feature branch**: `git checkout -b feature/your-feature-name`
3. **Make changes** following our coding standards
4. **Write tests** for new functionality
5. **Commit** with clear messages: `git commit -m "Add feature description"`
6. **Push** to your fork: `git push origin feature/your-feature-name`
7. **Create a Pull Request** with clear description

## Development Setup

```bash
# Clone repository
git clone https://github.com/oumar080890/OpsPilot-AI.git
cd OpsPilot-AI

# Install dependencies
npm install

# Start development environment
docker-compose up -d
npm run db:migrate
npm run dev
```

## Coding Standards

### TypeScript
- Use strict mode
- Explicit types (avoid `any`)
- 80-char line length (soft limit)

### JavaScript/Node.js
```javascript
// Good
const getUserById = async (id: string): Promise<User> => {
  const user = await db.users.findById(id);
  return user;
};

// Bad
const getUser = async (id) => {
  return await db.users.findById(id);
};
```

### React/Frontend
```typescript
// Use functional components with hooks
const UserCard: React.FC<UserCardProps> = ({ user }) => {
  const [isLoading, setIsLoading] = useState(false);
  
  return <div>{user.name}</div>;
};
```

## Commit Message Guidelines

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types**: feat, fix, docs, style, refactor, perf, test, chore

**Examples**:
```
feat(auth): add two-factor authentication
fix(voice): resolve audio upload timeout
docs(readme): update setup instructions
refactor(api): simplify error handling
```

## Pull Request Process

1. **Update documentation** if needed
2. **Add tests** for new functionality
3. **Run tests**: `npm run test`
4. **Pass linting**: `npm run lint`
5. **Describe changes** clearly in PR
6. **Reference issues**: "Fixes #123"
7. **Request review** from maintainers

## Testing

```bash
# Run all tests
npm run test

# Run specific test file
npm run test -- path/to/test.spec.ts

# Watch mode
npm run test:watch

# Coverage
npm run test:coverage
```

**Coverage requirements**:
- Minimum 80% overall
- 90%+ for critical paths
- 100% for utilities

## Documentation

- Update README.md if behavior changes
- Add JSDoc comments to functions
- Include examples for complex features
- Update API documentation

## Questions?

- **Issues**: https://github.com/oumar080890/OpsPilot-AI/issues
- **Discussions**: https://github.com/oumar080890/OpsPilot-AI/discussions
- **Email**: dev@opspilot.ai

## Contributor License Agreement

By contributing, you agree that your contributions will be licensed under the MIT License.

---

**Thank you for contributing to OpsPilot AI! 🎉**
