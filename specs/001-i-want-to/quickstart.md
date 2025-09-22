# Quickstart Guide: Simple Notes App

**Target Audience**: Developers implementing the Notes app  
**Prerequisites**: Node.js v18+, basic HTML/CSS/JavaScript knowledge  
**Estimated Setup Time**: 15-30 minutes

## Development Environment Setup

### 1. Project Structure Creation
```bash
# Create project directories
mkdir -p backend/src/{models,services,api}
mkdir -p backend/tests/{unit,integration,contract}
mkdir -p frontend/src/{components,styles,utils}
mkdir -p frontend/tests/{unit,e2e}

# Initialize package.json files
cd backend && npm init -y
cd ../frontend && npm init -y
cd .. && npm init -y  # Root workspace
```

### 2. Install Dependencies

#### Backend Dependencies
```bash
cd backend
npm install express cors helmet morgan
npm install --save-dev jest supertest eslint prettier nodemon
```

#### Frontend Dependencies  
```bash
cd frontend
npm install --save-dev jest @playwright/test eslint prettier live-server
```

#### Root Development Tools
```bash
npm install --save-dev concurrently eslint prettier
```

### 3. Configuration Files

#### Backend ESLint (`.eslintrc.js`)
```javascript
module.exports = {
  env: { node: true, es2021: true, jest: true },
  extends: ['eslint:recommended'],
  parserOptions: { ecmaVersion: 12 },
  rules: {
    'no-console': 'warn',
    'no-unused-vars': 'error'
  }
};
```

#### Jest Configuration (`jest.config.js`)
```javascript
module.exports = {
  testEnvironment: 'node',
  collectCoverageFrom: ['src/**/*.js'],
  coverageThreshold: {
    global: { branches: 80, functions: 80, lines: 80, statements: 80 }
  }
};
```

## Implementation Order (TDD Approach)

### Phase 1: Backend Core (TDD)
1. **Test First**: Note model validation tests
2. **Implement**: Note model with validation
3. **Test First**: File service tests (read/write notes.json)
4. **Implement**: File service with error handling
5. **Test First**: Notes service tests (CRUD operations)
6. **Implement**: Notes service with business logic

### Phase 2: API Layer (TDD)
1. **Test First**: GET /api/notes contract tests
2. **Implement**: Express route with error handling
3. **Test First**: POST /api/notes contract tests  
4. **Implement**: Create note endpoint with validation
5. **Integration Tests**: Full API workflow testing

### Phase 3: Frontend (TDD)
1. **Test First**: Note form validation tests
2. **Implement**: HTML form with client-side validation
3. **Test First**: Notes list rendering tests
4. **Implement**: Notes display with proper ordering
5. **E2E Tests**: Complete user workflow testing

### Phase 4: Integration & Polish
1. **Performance Testing**: API response time validation
2. **Accessibility Testing**: WCAG compliance verification
3. **Error Handling**: User-friendly error messages
4. **Documentation**: Code comments and API docs

## Quick Start Commands

### Development Workflow
```bash
# Terminal 1: Backend development server
cd backend && npm run dev

# Terminal 2: Frontend development server  
cd frontend && npm run dev

# Terminal 3: Run tests
npm run test:watch
```

### Package.json Scripts

#### Backend Scripts
```json
{
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js",
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "lint": "eslint src/",
    "format": "prettier --write src/"
  }
}
```

#### Frontend Scripts
```json
{
  "scripts": {
    "dev": "live-server --port=3001",
    "test": "jest",
    "test:e2e": "playwright test",
    "lint": "eslint src/",
    "format": "prettier --write src/"
  }
}
```

## File Templates

### Basic Express Server (`backend/server.js`)
```javascript
const express = require('express');
const cors = require('cors');
const helmet = require('helmet');
const morgan = require('morgan');

const app = express();
const PORT = process.env.PORT || 3000;

// Middleware
app.use(helmet());
app.use(cors());
app.use(morgan('combined'));
app.use(express.json({ limit: '1mb' }));

// Routes
app.get('/api/notes', (req, res) => {
  // TODO: Implement get notes
  res.json({ success: true, data: [], count: 0 });
});

app.post('/api/notes', (req, res) => {
  // TODO: Implement create note
  res.status(501).json({ 
    success: false, 
    error: { message: 'Not implemented' } 
  });
});

// Error handling
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({
    success: false,
    error: { message: 'Internal server error' }
  });
});

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});

module.exports = app;
```

### Basic Frontend (`frontend/index.html`)
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simple Notes App</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <main class="container">
        <header>
            <h1>My Notes</h1>
        </header>
        
        <section class="note-form">
            <form id="noteForm" aria-label="Create new note">
                <input type="text" id="title" placeholder="Note title" required>
                <textarea id="content" placeholder="Note content" required></textarea>
                <button type="submit">Save Note</button>
            </form>
        </section>
        
        <section class="notes-list">
            <div id="notesContainer" aria-label="Notes list">
                <!-- Notes will be rendered here -->
            </div>
        </section>
    </main>
    
    <script src="script.js"></script>
</body>
</html>
```

## Testing Strategy

### Unit Tests Example
```javascript
// backend/tests/unit/noteModel.test.js
const { validateNote } = require('../../src/models/note');

describe('Note Model Validation', () => {
  test('should validate note with title and content', () => {
    const note = { title: 'Test', content: 'Content' };
    const result = validateNote(note);
    expect(result.isValid).toBe(true);
  });

  test('should reject note with empty title', () => {
    const note = { title: '', content: 'Content' };
    const result = validateNote(note);
    expect(result.isValid).toBe(false);
    expect(result.errors.title).toContain('required');
  });
});
```

### E2E Tests Example
```javascript
// frontend/tests/e2e/noteCreation.spec.js
const { test, expect } = require('@playwright/test');

test('user can create and view notes', async ({ page }) => {
  await page.goto('http://localhost:3001');
  
  // Create a note
  await page.fill('#title', 'Test Note');
  await page.fill('#content', 'This is test content');
  await page.click('button[type="submit"]');
  
  // Verify note appears in list
  await expect(page.locator('[data-testid="note-item"]')).toContainText('Test Note');
});
```

## Performance Benchmarks

### Acceptance Criteria
- **Page Load**: <3 seconds (target: <1 second)
- **API Response**: <500ms (target: <200ms)
- **UI Interactions**: <200ms (target: <100ms)
- **Memory Usage**: <10MB total application memory

### Monitoring Setup
```javascript
// Simple performance monitoring
const startTime = Date.now();
// ... operation ...
const duration = Date.now() - startTime;
console.log(`Operation took ${duration}ms`);
```

## Troubleshooting

### Common Issues
1. **Port conflicts**: Change PORT in server.js or frontend dev server
2. **CORS errors**: Verify cors middleware configuration
3. **File permissions**: Ensure write access to notes.json location
4. **Memory issues**: Monitor for large note content or count limits

### Development Tips
- Use `npm run test:watch` for continuous testing during development
- Check browser console for frontend errors
- Monitor server logs for backend issues
- Use browser dev tools for performance profiling

## Next Steps

After quickstart setup:
1. Run initial test suite (should have some failing tests - this is expected for TDD)
2. Implement Note model with validation
3. Build file service for persistence
4. Create API endpoints following contract specifications
5. Develop frontend components with accessibility in mind
6. Add comprehensive error handling and user feedback

## Constitutional Compliance Checklist

✅ **Code Quality**: ESLint and Prettier configured  
✅ **Testing**: Jest and Playwright test frameworks ready  
✅ **UX Consistency**: Semantic HTML and accessibility setup  
✅ **Performance**: Monitoring and benchmarks established

**Ready for**: Task generation and implementation execution