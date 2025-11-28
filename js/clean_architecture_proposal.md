# Enhancing App Maintainability: Separating Business Logic from UI with Separation of Concerns

---

## Introduction

In modern web applications, tightly coupling business logic and UI increases maintenance costs and makes UI framework upgrades difficult. Following **separation of concerns principles**, separating the business logic from UI allows teams to **reuse stable logic while updating or swapping UI frameworks** easily.

---

## 1. The Current Problem

- Many projects **mix business logic and UI together**, creating tightly coupled components.
- Upgrading UI frameworks (like React 16 → React 19) **can force rewriting the entire app**, even though the **business rules remain unchanged**.
- This increases development time, introduces risk, and makes maintenance costly.

**Key Points:**
- UI components **directly contain business logic**, making upgrades risky.
- Any UI framework upgrade requires touching both UI and logic layers.
- Rewriting logic is wasteful since the business rules themselves haven’t changed.

---

## 2. The Key Insight

- **Business rules rarely change** if teams continue performing the same processes.
- UI frameworks and libraries may evolve, but **core logic — authentication, payments, validations — remains stable**.
- Rewriting business logic for a UI change is **unnecessary and costly**.

---

## 3. The Solution

### Concept
- **Separate business logic from UI components.**
- Place all business logic in a **central core package (`@cdn/core`)** that aggregates use cases from `packages/`.
- React apps or future apps consume only this core logic.

### Proposed Folder Structure

```
cdn/                                
├── package.json             # Root workspace + devDependencies
├── package-lock.json
├── .eslintrc.cjs                    
├── .prettierrc                       
├── cdn-web-react16/         # React 16 web app
│   ├── package.json
│   ├── index.html
│   ├── vite.config.js
│   └── src/
│       ├── main.jsx
│       ├── App.jsx
│       ├── api/             # API clients (axios, etc.)
│       │   └── auth-client.js
│       ├── components/
│       ├── pages/
│       ├── hooks/
│       └── styles/
├── cdn-web-react19/         # React 19 web app
│   ├── package.json
│   ├── index.html
│   ├── vite.config.js
│   └── src/
│       ├── main.jsx
│       ├── App.jsx
│       ├── api/             # API clients (fetch, react-query, etc.)
│       │   └── auth-client.js
│       ├── components/
│       ├── pages/
│       ├── hooks/
│       └── styles/
├── packages/                # Shared workspace packages
│   ├── core/                # @cdn/core
│   │   ├── package.json
│   │   ├── vite.config.js
│   │   └── src/
│   │       └── index.js     # Aggregates exports
│   ├── auth/                # @cdn/auth
│   │   ├── package.json
│   │   ├── vite.config.js
│   │   └── src/
│   │       ├── index.js     # Public API
│   │       ├── use-cases/   # LoginUseCase.js
│   │       ├── entities/    # User.js, Token.js
│   │       └── ports/       # AuthApiClient interface
│   ├── payments/            # @cdn/payments
│   │   ├── package.json
│   │   ├── vite.config.js
│   │   └── src/
│   │       └── index.js
│   └── shared/              # @cdn/shared
│       ├── package.json
│       ├── vite.config.js
│       └── src/
│           └── index.js
└── tests/
    └── setupTests.js
```

---

## 4. Example `package.json` Files

### Root `package.json`
```json
{
  "name": "cdn",
  "private": true,
  "workspaces": [
    "packages/*",
    "cdn-web-react16",
    "cdn-web-react19"
  ],
  "scripts": {
    "dev:react16": "npm run dev -w cdn-web-react16",
    "dev:react19": "npm run dev -w cdn-web-react19",
    "build:react16": "npm run build -w cdn-web-react16",
    "build:react19": "npm run build -w cdn-web-react19",
    "test": "npm test --workspaces",
    "lint": "eslint .",
    "format": "prettier --write ."
  },
  "devDependencies": {
    "eslint": "^8.0.0",
    "prettier": "^3.0.0",
    "vite": "^5.0.0",
    "vitest": "^1.0.0"
  }
}
```

### packages/core/package.json
```json
{
  "name": "@cdn/core",
  "version": "1.0.0",
  "main": "index.js",
  "dependencies": {
    "@cdn/auth": "^1.0.0",
    "@cdn/payments": "^1.0.0",
    "@cdn/shared": "^1.0.0"
  }
}
```

### cdn-web-react19/package.json
```json
{
  "name": "cdn-web-react19",
  "version": "1.0.0",
  "private": true,
  "dependencies": {
    "react": "^19.0.0",
    "react-dom": "^19.0.0",
    "@cdn/core": "^1.0.0"
  }
}
```

### cdn-web-react16/package.json
```json
{
  "name": "cdn-web-react16",
  "version": "1.0.0",
  "private": true,
  "dependencies": {
    "react": "^16.0.0",
    "react-dom": "^16.0.0",
    "@cdn/core": "^1.0.0"
  }
}
```

### packages/auth/package.json
```json
{
  "name": "@cdn/auth",
  "version": "1.0.0",
  "main": "dist/index.js",
  "module": "dist/index.mjs",
  "scripts": {
    "build": "vite build",
    "dev": "vite build --watch"
  },
  "dependencies": {
    "@cdn/shared": "^1.0.0"
  },
  "devDependencies": {
    "vite": "^5.0.0"
  }
}
```

### packages/payments/package.json
```json
{
  "name": "@cdn/payments",
  "version": "1.0.0",
  "main": "index.js"
}
```

### packages/shared/package.json
```json
{
  "name": "@cdn/shared",
  "version": "1.0.0",
  "main": "index.js"
}
```

---

## 5. Example Code

### packages/auth/src/index.js
```javascript
export * from './use-cases/LoginUseCase';
export * from './entities/User';
```

### packages/auth/src/use-cases/LoginUseCase.js
```javascript
/**
 * LoginUseCase - Orchestrates login flow and transforms data
 * 
 * Responsibilities:
 * - Calls API client to communicate with backend
 * - Transforms API response to domain model
 */
export class LoginUseCase {
  constructor(authApiClient) {
    this.authApiClient = authApiClient;
  }
  
  // Arrow function to preserve 'this' context
  execute = async (username, password) => {
    try {
      // Call API client (returns raw API response)
      const apiResponse = await this.authApiClient.login(username, password);
      
      // Transform API response to domain model
      if (apiResponse.success) {
        return {
          success: true,
          user: {
            id: apiResponse.data.user_id,
            username: apiResponse.data.user_name,
            role: apiResponse.data.user_role.name
          },
          token: apiResponse.token
        };
      }
      
      return { success: false, error: apiResponse.message };
    } catch (error) {
      return { success: false, error: error.message };
    }
  }
}

export function validatePassword(password) {
  return password && password.length >= 4;
}
```

### packages/auth/vite.config.js
```javascript
import { resolve } from 'path';
import { defineConfig } from 'vite';

export default defineConfig({
  build: {
    lib: {
      entry: resolve(__dirname, 'src/index.js'),
      name: 'CdnAuth',
      fileName: 'index',
    },
    rollupOptions: {
      // Make sure to externalize deps that shouldn't be bundled
      external: ['@cdn/shared'],
      output: {
        globals: {
          '@cdn/shared': 'CdnShared',
        },
      },
    },
  },
});
```

### packages/shared/index.js
```javascript
/**
 * Shared utilities
 */
export const VERSION = '1.0.0';
```

### packages/core/index.js
```javascript
// Import from workspace packages by name
// Export use case classes (not instances)
export { LoginUseCase, validatePassword } from '@cdn/auth';
export * from '@cdn/payments';
export * from '@cdn/shared';
```

### cdn-web-react19/src/api/auth-client.js
```javascript
/**
 * AuthApiClient for React 19 - Uses modern fetch API
 * Only handles HTTP communication, returns raw API response
 */
export class AuthApiClient {
  async login(username, password) {
    const response = await fetch('/api/auth/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ 
        user_name: username,  // Map to backend API format
        pass: password 
      })
    });
    
    if (!response.ok) {
      throw new Error('API request failed');
    }
    
    // Return raw API response (no transformation)
    return response.json();
  }
}
```

### cdn-web-react19/src/App.jsx
```javascript
import React, { useState } from 'react';
import { LoginUseCase } from '@cdn/core';
import { AuthApiClient } from './api/auth-client';

// Composition Root: Configure dependencies at the entry point or a dedicated container
const authApiClient = new AuthApiClient();
const loginUseCase = new LoginUseCase(authApiClient);

function App() {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  const handleLogin = async () => {
    setLoading(true);
    setError(null);
    
    // Execute the use case
    const result = await loginUseCase.execute('admin', '1234');
    
    setLoading(false);
    if (result.success) {
      setUser(result.user);
    } else {
      setError(result.error);
    }
  };

  return (
    <div>
      <h1>Login (React 19)</h1>
      <button onClick={handleLogin} disabled={loading}>
        {loading ? 'Logging in...' : 'Login'}
      </button>
      {user && <p>Welcome, {user.username}! (Role: {user.role})</p>}
      {error && <p style={{ color: 'red' }}>Error: {error}</p>}
    </div>
  );
}

export default App;
```

### cdn-web-react16/src/api/auth-client.js
```javascript
/**
 * AuthApiClient for React 16 - Uses axios library
 * Only handles HTTP communication, returns raw API response
 */
import axios from 'axios';

export class AuthApiClient {
  async login(username, password) {
    const response = await axios.post('/api/auth/login', {
      user_name: username,  // Map to backend API format
      pass: password
    });
    
    // Return raw API response (no transformation)
    return response.data;
  }
}
```

### cdn-web-react16/src/App.jsx
```javascript
import React from 'react';
import { LoginUseCase } from '@cdn/core';
import { AuthApiClient } from './api/auth-client';

// Composition Root
const authApiClient = new AuthApiClient();
const loginUseCase = new LoginUseCase(authApiClient);

class App extends React.Component {
  state = { user: null, loading: false, error: null };

  handleLogin = async () => {
    this.setState({ loading: true, error: null });
    
    // Execute the use case
    const result = await loginUseCase.execute('admin', '1234');
    
    if (result.success) {
      this.setState({ user: result.user, loading: false });
    } else {
      this.setState({ error: result.error, loading: false });
    }
  };

  render() {
    const { user, loading, error } = this.state;
    return (
      <div>
        <h1>Login (React 16)</h1>
        <button onClick={this.handleLogin} disabled={loading}>
          {loading ? 'Logging in...' : 'Login'}
        </button>
        {user && <p>Welcome, {user.username}! (Role: {user.role})</p>}
        {error && <p style={{ color: 'red' }}>Error: {error}</p>}
      </div>
    );
  }
}

export default App;
```

---

## 6. Handling Different API Formats with Dependency Injection

### The Challenge
Backend APIs often have different formats than your domain model:

```javascript
// Your domain model (clean, consistent)
{ id: 1, username: 'admin', role: 'admin' }

// Backend API response (varies by API)
{ user_id: 1, user_name: 'admin', user_role: { name: 'admin' } }
```

### The Solution: Separation of Concerns

**API Clients** (in each React app):
1. Handle HTTP communication only
2. Use their preferred library (axios, fetch, react-query)
3. Return raw API responses

**Use Cases** (in use-cases/):
1. Receive API clients via dependency injection
2. Call API clients to get raw responses
3. Transform API format to domain model
4. Apply business logic and validation

**Key Benefits:**
- ✅ **Simple API Clients**: Just HTTP wrappers, no business logic
- ✅ **Framework Freedom**: Each app uses its preferred HTTP library
- ✅ **Domain Logic Centralized**: All mapping in use cases
- ✅ **Easy Testing**: Mock API clients to return different responses
- ✅ **Flexibility**: Swap HTTP libraries without touching business logic

---

## 7. Testing Business Logic

With business logic separated from UI, you can test it independently without rendering components:

### tests/auth.test.js
```javascript
import { describe, it, expect } from 'vitest';
import { LoginUseCase, validatePassword } from '@cdn/auth';

// Mock API client for testing
class MockAuthApiClient {
  async login(username, password) {
    // Simulate backend API response
    if (username === 'admin' && password === '1234') {
      return {
        success: true,
        data: {
          user_id: 1,
          user_name: 'admin',
          user_role: { name: 'admin' }
        },
        token: 'mock-jwt-token'
      };
    }
    return { success: false, message: 'Invalid credentials' };
  }
}

describe('LoginUseCase', () => {
  it('should transform API response to domain model', async () => {
    const mockClient = new MockAuthApiClient();
    const useCase = new LoginUseCase(mockClient);
    
    const result = await useCase.execute('admin', '1234');
    
    // Check transformation from API format to domain model
    expect(result.success).toBe(true);
    expect(result.user.id).toBe(1);  // user_id -> id
    expect(result.user.username).toBe('admin');  // user_name -> username
    expect(result.user.role).toBe('admin');  // user_role.name -> role
    expect(result.token).toBe('mock-jwt-token');
  });

  it('should handle API error responses', async () => {
    const mockClient = new MockAuthApiClient();
    const useCase = new LoginUseCase(mockClient);
    
    const result = await useCase.execute('wrong', 'wrong');
    
    expect(result.success).toBe(false);
    expect(result.error).toBe('Invalid credentials');
  });

  it('should handle API exceptions', async () => {
    const mockClient = {
      login: async () => { throw new Error('Network error'); }
    };
    const useCase = new LoginUseCase(mockClient);
    
    const result = await useCase.execute('admin', '1234');
    
    expect(result.success).toBe(false);
    expect(result.error).toBe('Network error');
  });
});

describe('validatePassword', () => {
  it('should validate password length', () => {
    expect(validatePassword('1234')).toBe(true);
    expect(validatePassword('123')).toBe(false);
    expect(validatePassword('')).toBe(false);
  });
});
```

**Key Benefits:**
- ✅ **Fast tests** - No DOM rendering or component mounting
- ✅ **Framework-agnostic** - Same tests work regardless of UI framework
- ✅ **Easy mocking** - Mock API calls without component complexity
- ✅ **Better coverage** - Test edge cases without UI interaction

---

## 8. Benefits

| Benefit                       | Description |
|-------------------------------|-------------|
| **Lower Upgrade Costs**        | Upgrade UI frameworks without touching business rules. |
| **Faster Development**         | New apps can reuse existing logic, reducing duplication. |
| **Reduced Risk**               | Core logic stays the same; fewer bugs during upgrades. |
| **Scalable Architecture**      | Easily add new apps or features without rewriting logic. |
| **Improved Testing**           | Business rules can be tested independently of UI. |

---

## 9. Costs / Trade-offs

- **Initial Setup Complexity** – Requires planning, restructuring, and workspace configuration.
- **Build & CI Overhead** – Multiple apps need separate build processes.
- **Developer Learning Curve** – Team must understand modular architecture and workspaces.

**These are one-time costs** that are far outweighed by long-term efficiency and maintainability gains.

---

## 10. Example Scenario

- Upgrading from **React UI 16 → React UI 19** currently requires rewriting both UI and logic.
- With this architecture:
  - Only the **UI components are updated**.
  - **Business rules stay the same**, saving time and avoiding errors.

---

## 11. Architecture Diagram

```mermaid
graph TD
    A[React 16 App] -->|creates| A1[AuthApiClient - axios]
    B[React 19 App] -->|creates| B1[AuthApiClient - fetch]
    A1 -->|injected into| C[LoginUseCase]
    B1 -->|injected into| C
    C -->|exported by| D[@cdn/core]
    A -->|imports| D
    B -->|imports| D
    A1 -->|calls| G[Backend API]
    B1 -->|calls| G
    
    style C fill:#4CAF50,color:#fff
    style D fill:#2196F3,color:#fff
    style A fill:#FF9800,color:#fff
    style B fill:#FF9800,color:#fff
    style A1 fill:#FFC107,color:#000
    style B1 fill:#FFC107,color:#000
    style G fill:#9E9E9E,color:#fff
```

**Dependency Flow:**
- 🔶 **UI Layer** → creates API clients, imports use cases from Core
- 🟡 **API Clients** → injected into use cases, calls Backend APIs
- 🟢 **Use Cases** → pure business logic, receives API client via constructor
- 🔵 **Core Layer** → exports use case classes (not instances)
- ⚪ **Backend API** → called by API clients

**Key Principle:** Dependencies point inward. Use cases depend on API client abstraction (interface), not implementation.

---

## 12. Conclusion

Separating UI and business logic allows:
- **Reuse of stable business rules** across apps and frameworks.
- **Reduced rework, technical debt, and upgrade costs.**
- **Faster, safer feature development.**

**Final Takeaway:** By implementing separation of concerns principles, teams can **upgrade UI frameworks independently**, **reuse proven logic**, and **deliver features faster with lower risk**.

