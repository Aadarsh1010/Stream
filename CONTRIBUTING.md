# Contributing to StreamHub

Thank you for your interest in contributing! This guide will help you set up your development environment and contribute effectively.

---

## Table of Contents

1. [Code of Conduct](#code-of-conduct)
2. [Getting Started](#getting-started)
3. [Development Workflow](#development-workflow)
4. [Testing](#testing)
5. [Code Style](#code-style)
6. [Pull Request Process](#pull-request-process)
7. [Adding New Video Extractors](#adding-new-video-extractors)
8. [Reporting Bugs](#reporting-bugs)
9. [Feature Requests](#feature-requests)

---

## Code of Conduct

This project adheres to a code of conduct. By participating, you are expected to:
- Be respectful and inclusive
- Accept constructive feedback
- Focus on what's best for the community
- Show empathy towards others

---

## Getting Started

### Prerequisites

- **Node.js** 18+ ([Download](https://nodejs.org))
- **Git**
- **PostgreSQL** (or use SQLite for development)
- **npm** or **yarn**

### Setup

1. **Fork & Clone**
```bash
git clone https://github.com/yourusername/streamhub.git
cd streamhub
```

2. **Install Dependencies**
```bash
npm ci
```

3. **Database Setup**

**Option A: SQLite (easiest)**
```bash
cp prisma/schema.sqlite.prisma prisma/schema.prisma
# Update .env:
# DATABASE_URL="file:./dev.db"
npx prisma generate
npx prisma db push
```

**Option B: PostgreSQL**
```bash
# Update .env:
# DATABASE_URL="postgresql://username:password@localhost:5432/streamhub"
npx prisma generate
npx prisma migrate dev --name init
```

4. **Environment Variables**

Copy `.env.example` to `.env` and fill in:
```env
DATABASE_URL="your-database-url"
NEXTAUTH_SECRET="generate-a-random-string"
NEXTAUTH_URL="http://localhost:3000"
```

Generate NEXTAUTH_SECRET:
```bash
openssl rand -base64 32
```

5. **Start Development Server**
```bash
npm run dev
```

Open http://localhost:3000 in your browser.

---

## Development Workflow

### Branch Strategy

- `main` - stable production code
- `feature/xxx` - new features
- `bugfix/xxx` - bug fixes
- `docs/xxx` - documentation updates

### Making Changes

1. **Create a branch**
```bash
git checkout -b feature/add-subtitles
```

2. **Make your changes**
- Follow code style guidelines
- Add/update tests
- Update documentation if needed

3. **Test locally**
```bash
npm test          # Run all tests
npm run lint      # Check code quality
npm run format    # Format code
npm run dev       # Manual testing
```

4. **Commit**
```bash
git add .
git commit -m "feat: add subtitle support for VTT files"
```

**Commit message format** (conventional commits):
- `feat:` new feature
- `fix:` bug fix
- `docs:` documentation changes
- `style:` formatting, missing semicolons, etc. (no code change)
- `refactor:` code restructuring
- `test:` adding or updating tests
- `chore:` build process, tooling, etc.

5. **Push**
```bash
git push origin feature/add-subtitles
```

6. **Open Pull Request**
- Go to GitHub
- Create PR from your branch to `main`
- Fill out PR template
- Request review from maintainers

---

## Testing

### Unit Tests (Jest)

```bash
# Run all tests
npm test

# Run with watch mode
npm run test:watch

# Run with coverage
npm run test:coverage

# Run only API tests
npm run test:api
```

### Test Structure

```
src/
├── __tests__/
│   ├── api/              # API route tests
│   │   ├── auth.test.ts
│   │   ├── content.test.ts
│   │   └── ...
│   ├── components/       # Component tests
│   │   ├── ContentCard.test.tsx
│   │   └── ...
│   └── extractVideo.test.ts  # Extraction logic tests
```

### Writing Tests

- Use `jest` for unit/integration tests
- Use `@testing-library/react` for component tests
- Mock external dependencies (axios, Prisma) with `jest.mock()`
- Keep tests isolated - use `beforeEach` to reset mocks

Example:
```typescript
describe('ContentCard', () => {
  it('renders title correctly', () => {
    render(<ContentCard content={mockContent} onClick={mockFn} isWishlisted={false} />)
    expect(screen.getByText('Inception')).toBeInTheDocument()
  })
})
```

### E2E Tests (Playwright - Coming Soon)

```bash
npm run test:e2e
```

---

## Code Style

We use **ESLint** and **Prettier** for consistent code style.

### Formatting

```bash
# Format all files
npm run format

# Check formatting without changing
npm run format:check

# Auto-fix lint errors
npm run lint:fix
```

### Guidelines

- **TypeScript**: Always use types, avoid `any`
- **Naming**: `camelCase` for variables/functions, `PascalCase` for components/types
- **Imports**: Group: 1) React, 2) next/, 3. third-party, 4. internal
- **Components**: Functional components with hooks, not class components
- **API Routes**: Use `getServerSession()` for auth, always check session
- **Prisma**: Use generated types, proper where clauses with userId filtering

---

## Pull Request Process

### Before Submitting

- [ ] All tests pass (`npm test`)
- [ ] No lint errors (`npm run lint`)
- [ ] Code formatted (`npm run format`)
- [ ] Type checking passes (`npm run type-check`)
- [ ] Documentation updated (if needed)
- [ ] Commit messages follow conventional format

### PR Template

Fill out the PR template completely:
- **Description**: What changed and why
- **Type**: Feature / Bug / Docs / etc.
- **Testing**: How to test the changes
- **Screenshots**: For UI changes
- **Breaking changes**: Note any breaking changes

### Review Process

1. Maintainers review code
2. Address feedback (make changes, push to same branch)
3. Squash/rebase if multiple commits
4. Merge to `main` (squash and merge preferred)

---

## Adding New Video Extractors

One of the most valuable contributions is adding support for new video hosting sites.

### Steps

1. **Identify the Pattern**
   - What URL should your extractor match? (e.g., `gofile.io/d/`)
   - Where is the video URL in the page/API?

2. **Update `src/lib/extractVideo.ts`**

Add your extractor in the appropriate place:

```typescript
if (url.includes('newhost.com')) {
  // Extract identifier from URL
  const idMatch = url.match(/newhost\.com\/([a-zA-Z0-9]+)/)
  if (!idMatch) return { directUrl: null }

  // Call their API or parse page
  const response = await axios.get(`https://api.newhost.com/get/${idMatch[1]}`)
  const videoUrl = response.data?.videoUrl || response.data?.downloadUrl

  if (videoUrl) {
    return { directUrl: videoUrl, mimeType: 'video/mp4' }
  }
}
```

3. **Add Tests**

```typescript
it('should extract from newhost.com', async () => {
  ;(axios as any).mockResolvedValue({
    data: { videoUrl: 'https://cdn.newhost.com/video.mp4' }
  })

  const result = await extractVideo('https://newhost.com/abc123')
  expect(result.directUrl).toBe('https://cdn.newhost.com/video.mp4')
})
```

4. **Test Manually**
```bash
curl -X POST http://localhost:3000/api/extract-video \
  -H "Content-Type: application/json" \
  -d '{"url":"https://newhost.com/your-video"}'
```

5. **Update Documentation**
- Add the site to supported list in `README.md`
- Note any special requirements (API key, rate limits)

---

## Reporting Bugs

### Bug Report Template

```
**Description**
Clear description of the bug.

**To Reproduce**
Steps to reproduce:
1. Go to '...'
2. Click on '....'
3. See error

**Expected Behavior**
What should happen.

**Screenshots/Logs**
If applicable, add screenshots and console logs.

**Environment**
- OS: [e.g. macOS 14, Windows 11]
- Browser: [e.g. Chrome 120, Firefox 121]
- Node: [e.g. 18.17.0]
- StreamHub version: [e.g. v1.0.0]

**Additional Context**
Anything else relevant.
```

---

## Feature Requests

We welcome feature requests! Before opening an issue:

1. **Check existing issues** to avoid duplicates
2. **Consider if it fits the project scope** (video streaming platform)
3. **Provide clear use case** - why is this needed?
4. **Suggest implementation** if possible

### Feature Request Template

```
**Feature Description**
Clear description of the proposed feature.

**Problem it Solves**
What user problem does this address?

**Proposed Solution**
How should it work?

**Alternatives Considered**
Other approaches you've considered.

**Additional Context**
Mockups, examples from other platforms, etc.
```

---

## Questions?

- **Discussions**: Use GitHub Discussions
- **Issues**: Use GitHub Issues
- **Documentation**: See `/docs` folder

Happy coding! 🎬

---

## License

By contributing, you agree that your contributions will be licensed under the MIT License.
