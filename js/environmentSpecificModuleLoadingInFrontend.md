
# Environment-Specific Module Loading in React with Vite: The Strict Mode-Aliasing Pattern

Managing multiple environments—development, staging, and production—is a common challenge in large-scale React projects. Often, different environments require distinct implementations of certain modules, such as APIs, analytics trackers, or feature toggles. 

Relying solely on **runtime environment checks** like:

```js
if (import.meta.env.PROD) {
  // production logic
} else {
  // development logic
}
```

works but has limitations:

- **Error-prone:** A wrong conditional could leak sensitive production logic in development builds.
- **Hard to maintain:** Complex modules with multiple environment branches quickly become messy.
- **Bundle size:** All environment-specific code is included, increasing bundle size unnecessarily.

The **Strict Mode-Aliasing Pattern** solves these issues by enforcing **build-time module resolution**. Only the correct environment module is included in the final bundle.

---

## Step 1. Define a File Naming Convention

A **consistent file naming scheme** is essential. Each environment-specific module should follow:

```
{fileName}.{mode}.js
```

**Example:** A user API module:

```
src/
  api/
    UserApi.development.js   // returns mock users
    UserApi.production.js    // calls real production API
    UserApi.staging.js       // calls the staging API
```

**Why this matters:**  

- The filename directly indicates the environment, reducing ambiguity.  
- Vite can automatically resolve the correct file based on the build mode.  
- Missing files are immediately detected, avoiding silent failures.

### Example Implementations

```js
// UserApi.development.js
export default {
  getUsers: async () => [
    { id: 1, name: 'Dev User 1' },
    { id: 2, name: 'Dev User 2' },
  ],
};
```

```js
// UserApi.production.js
export default {
  getUsers: async () => {
    const response = await fetch('https://api.example.com/users');
    return await response.json();
  },
};
```

```js
// UserApi.staging.js
export default {
  getUsers: async () => {
    const response = await fetch('https://staging-api.example.com/users');
    return await response.json();
  },
};
```

**Mode mapping to npm scripts:**

- `development` → `npm run dev`  
- `production` → `npm run build`  
- `staging` → `npm run build --mode staging`  

---

## Step 2. Configure Vite for Mode-Specific Loading

Vite allows **dynamic aliasing** in its configuration, letting you point a generic module import to the correct environment-specific file.

```js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import fs from 'fs';
import path from 'path';

/**
 * Builds a complete Vite alias map by scanning the source directory.
 *
 * ✅ Supports environment-specific files (e.g., api.development.js)
 * ✅ Mode-specific files override generic ones
 * ✅ Cross-platform path normalization
 *
 * @param {string} mode - Current Vite mode ('development' | 'production' | etc.)
 * @param {{
 *   root?: string,
 *   prefix?: string
 * }} [options]
 * @returns {Record<string, string>} Alias mappings for Vite's `resolve.alias`
 */
function aliases(mode, options = {}) {
  // --- Configurable Defaults ---
  const {
    root = 'src',
    prefix = '@',
  } = options;

  const EXTENSIONS = ['.js', '.jsx', '.ts', '.tsx'];
  const BASE_FILE_REGEX = /^([^.]+)\./;
  const srcRoot = path.resolve(__dirname, root);
  const escapedExts = EXTENSIONS.map(ext => `\\${ext}`).join('|');
  const modePattern = new RegExp(`\\.${mode}(${escapedExts})$`, 'i');

  const aliases = {};

  // --- Core Recursive Scanner ---
  function scan(dir) {
    for (const entry of fs.readdirSync(dir, { withFileTypes: true })) {
      const fullPath = path.join(dir, entry.name);

      if (entry.isDirectory()) {
        scan(fullPath);
        continue;
      }

      const ext = path.extname(entry.name);
      if (!EXTENSIONS.includes(ext)) continue;

      const baseMatch = entry.name.match(BASE_FILE_REGEX);
      if (!baseMatch) continue;

      const baseName = baseMatch[1];
      const isGeneric = entry.name === `${baseName}${ext}`;
      const isModeSpecific = modePattern.test(entry.name);

      // Skip irrelevant variants (e.g., api.production.js in dev mode)
      if (!isGeneric && !isModeSpecific) continue;

      // Compute alias key relative to src root
      const relativeKey = path
        .relative(srcRoot, path.join(path.dirname(fullPath), baseName))
        .replace(/\\/g, '/');
      const aliasKey = `${prefix}/${relativeKey}`;

      // Priority rule: mode-specific file always overrides generic one
      if (!aliases[aliasKey] || isModeSpecific) {
        aliases[aliasKey] = path.resolve(fullPath);
      }
    }
  }

  // --- Execute Recursive Scan ---
  scan(srcRoot);

  // Add the root-level prefix alias (lowest priority)
  aliases[prefix] = srcRoot;

  return aliases;
}

export default defineConfig(({ mode }) => ({
  plugins: [react()],
  resolve: {
    alias: aliases(mode),
  },
}));
```

**How it works:**
1. Recursively scans the `src` directory for files ending with `.{mode}.js`.
2. Constructs aliases such that `import UserApi from '@/api/UserApi'` points to the environment-specific file.
3. If a file is missing for the current mode, the default will be used.
---

## Step 3. Define npm Scripts for Environment Modes

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "build:staging": "vite build --mode staging",
    "preview": "vite preview"
  }
}
```

**Behavior:**

- `npm run dev` → development mode  
- `npm run build` → production mode  
- `npm run build:staging` → staging mode  

Vite passes the `mode` to the config, which then maps imports to the correct files.

---

## Step 4. Use Modules Normally in React

After configuration, importing modules is **clean and environment-agnostic**:

```js
import UserApi from '@/api/UserApi';

async function showUsers() {
  const users = await UserApi.getUsers();
  console.log(users);
}

showUsers();
```

**Outcome:**

| Build Mode | Module Used               | Behavior                     |
|------------|--------------------------|-------------------------------|
| Development| `UserApi.development.js` | Returns mock users           |
| Production | `UserApi.production.js`  | Fetches real API             |
| Staging    | `UserApi.staging.js`     | Fetches staging API          |

- Missing a mode-specific file results in a fallback to UserApi.js

---

## Example Folder Structure

```
src/
  api/
    UserApi.development.js
    UserApi.production.js
    UserApi.staging.js
    UserApi.js
  components/
    UserList.jsx
```

- Development build → resolves to `UserApi.development.js`  
- Production build → resolves to `UserApi.production.js`  
- Staging build → resolves to `UserApi.staging.js`  

---

## Pro Tips

1. **Always create a module for each environment** you intend to support.  
2. **Combine with `.env` files** for environment variables:

```
.env.development
.env.production
.env.staging
```

```js
console.log(import.meta.env.VITE_API_URL);
```

3. This pattern works for **any module**, not just API wrappers—components, utilities, or configuration modules can all follow the same convention.  
4. Keep the aliasing consistent: use `@/` as a universal path prefix for clean imports.  

---

## Conclusion

By combining:

- **Vite build modes**,  
- **npm scripts**, and  
- **strict `{fileName}.{mode}.js` imports**,  

you get a **robust and scalable environment-specific module loading pattern** for React projects:

- Automatic selection of environment-specific modules  
- Clean and maintainable imports  
- Build-time validation to catch missing modules  

This approach prevents accidental code leaks, reduces bundle size, and keeps your project organized as it grows.
