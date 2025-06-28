# Contributing to SharedSpace

Thank you for your interest in contributing to SharedSpace! This document provides guidelines and information for contributors.

## 🚀 Getting Started

### Prerequisites
- Node.js 18 or higher
- npm or yarn package manager
- Git

### Setting Up Development Environment

1. **Fork the repository**
   ```bash
   # Fork on GitHub, then clone your fork
   git clone https://github.com/yourusername/sharedspace-rental-platform.git
   cd sharedspace-rental-platform
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Start development server**
   ```bash
   npm run dev
   ```

4. **Create a feature branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```

## 📋 Development Guidelines

### Code Style

We use ESLint and Prettier to maintain consistent code style:

```bash
# Check linting
npm run lint

# Fix linting issues
npm run lint:fix

# Format code
npm run format
```

### TypeScript Guidelines

- **Use TypeScript for all new code**
- **Define proper interfaces** for all data structures
- **Avoid `any` type** - use proper typing
- **Export types** from `src/types/index.ts`

Example:
```typescript
// ✅ Good
interface PropertyFormData {
  title: string;
  description: string;
  type: 'apartment' | 'house' | 'villa';
}

// ❌ Avoid
const formData: any = {
  title: "Some title",
  // ...
};
```

### Component Guidelines

- **Use functional components** with hooks
- **Follow the component structure**:
  ```typescript
  interface ComponentProps {
    // Define props
  }

  const Component: React.FC<ComponentProps> = ({ prop1, prop2 }) => {
    // Hooks
    const [state, setState] = useState();
    
    // Event handlers
    const handleClick = () => {
      // ...
    };
    
    // Render
    return (
      <div>
        {/* JSX */}
      </div>
    );
  };

  export default Component;
  ```

- **Use descriptive prop names**
- **Implement proper error boundaries**
- **Add loading and error states**

### File Organization

```
src/
├── components/
│   ├── ComponentName/
│   │   ├── index.ts          # Export file
│   │   ├── ComponentName.tsx # Main component
│   │   └── ComponentName.test.tsx # Tests
├── pages/
├── hooks/
├── types/
├── utils/
└── data/
```

### Styling Guidelines

We use Tailwind CSS for styling:

- **Use Tailwind utility classes** instead of custom CSS
- **Follow responsive design patterns**:
  ```jsx
  <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
  ```
- **Use consistent spacing** (4, 6, 8, 12, 16, 24, 32)
- **Implement hover and focus states**
- **Ensure accessibility** with proper contrast and focus indicators

## 🧪 Testing

### Running Tests

```bash
# Run all tests
npm run test

# Run tests in watch mode
npm run test:watch

# Run tests with coverage
npm run test:coverage
```

### Writing Tests

- **Write tests for all new components**
- **Test user interactions**
- **Test error scenarios**
- **Use descriptive test names**

Example:
```typescript
import { render, screen, fireEvent } from '@testing-library/react';
import PropertyCard from './PropertyCard';

describe('PropertyCard', () => {
  it('should display property title and price', () => {
    const mockProperty = {
      id: '1',
      title: 'Test Property',
      pricing: { basePrice: 100 }
    };

    render(<PropertyCard property={mockProperty} />);
    
    expect(screen.getByText('Test Property')).toBeInTheDocument();
    expect(screen.getByText('$100/night')).toBeInTheDocument();
  });

  it('should call onClick when card is clicked', () => {
    const mockOnClick = jest.fn();
    const mockProperty = { /* ... */ };

    render(<PropertyCard property={mockProperty} onClick={mockOnClick} />);
    
    fireEvent.click(screen.getByRole('button'));
    expect(mockOnClick).toHaveBeenCalledWith(mockProperty);
  });
});
```

## 📝 Commit Guidelines

We follow conventional commits for clear commit history:

### Commit Message Format
```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

### Types
- **feat**: New feature
- **fix**: Bug fix
- **docs**: Documentation changes
- **style**: Code style changes (formatting, etc.)
- **refactor**: Code refactoring
- **test**: Adding or updating tests
- **chore**: Maintenance tasks

### Examples
```bash
feat(booking): add manual booking creation form
fix(calendar): resolve sync issue with external calendars
docs(readme): update installation instructions
style(components): improve button hover states
refactor(hooks): extract calendar logic to custom hook
test(property): add unit tests for property card component
chore(deps): update dependencies to latest versions
```

## 🐛 Bug Reports

When reporting bugs, please include:

1. **Clear description** of the issue
2. **Steps to reproduce** the bug
3. **Expected behavior**
4. **Actual behavior**
5. **Screenshots** (if applicable)
6. **Browser/OS information**
7. **Console errors** (if any)

Use our bug report template:

```markdown
## Bug Description
A clear description of what the bug is.

## Steps to Reproduce
1. Go to '...'
2. Click on '...'
3. Scroll down to '...'
4. See error

## Expected Behavior
What you expected to happen.

## Actual Behavior
What actually happened.

## Screenshots
If applicable, add screenshots.

## Environment
- OS: [e.g. macOS, Windows, Linux]
- Browser: [e.g. Chrome, Firefox, Safari]
- Version: [e.g. 22]

## Additional Context
Any other context about the problem.
```

## 💡 Feature Requests

For feature requests, please:

1. **Check existing issues** to avoid duplicates
2. **Describe the feature** clearly
3. **Explain the use case** and benefits
4. **Provide mockups** or examples (if applicable)
5. **Consider implementation** complexity

## 🔄 Pull Request Process

1. **Update documentation** if needed
2. **Add tests** for new functionality
3. **Ensure all tests pass**
4. **Update the README** if necessary
5. **Follow the PR template**

### PR Template
```markdown
## Description
Brief description of changes.

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [ ] Tests pass locally
- [ ] Added new tests
- [ ] Manual testing completed

## Screenshots
If applicable, add screenshots.

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Documentation updated
- [ ] No breaking changes
```

## 🏗️ Architecture Guidelines

### State Management
- **Use React hooks** for local state
- **Implement custom hooks** for shared logic
- **Consider context** for global state
- **Avoid prop drilling**

### API Integration
- **Create service functions** for API calls
- **Handle loading states**
- **Implement error handling**
- **Use proper TypeScript types**

### Performance
- **Implement lazy loading** for routes
- **Use React.memo** for expensive components
- **Optimize images** and assets
- **Minimize bundle size**

## 📚 Resources

- [React Documentation](https://reactjs.org/docs)
- [TypeScript Handbook](https://www.typescriptlang.org/docs)
- [Tailwind CSS Documentation](https://tailwindcss.com/docs)
- [Testing Library Documentation](https://testing-library.com/docs)

## 🤝 Community

- **Be respectful** and inclusive
- **Help others** learn and grow
- **Share knowledge** and best practices
- **Provide constructive feedback**

## 📞 Getting Help

If you need help:

1. **Check the documentation**
2. **Search existing issues**
3. **Ask in discussions**
4. **Contact maintainers**

Thank you for contributing to SharedSpace! 🎉