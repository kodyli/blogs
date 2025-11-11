
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
import path from 'path';
import fs from 'fs';

// --- Constants and Regex Definitions ---

/**
 * 💡 Standard file extensions handled by Vite/Rollup.
 * We only target common JavaScript/TypeScript extensions for module resolution.
 */
const EXTENSIONS = ['.js', '.jsx', '.ts', '.tsx'];

/**
 * The prefix used for all generated aliases (e.g., '@/components').
 * This corresponds to the root of the 'src' directory.
 */
const ALIAS_PREFIX = '@';

/**
 * Regex to extract the base name of a file, ignoring extensions and mode suffixes.
 * E.g., for 'api.development.js', it captures 'api'. For 'index.js', it captures 'index'.
 */
const BASE_FILE_REGEX = /^([^.]+)\./;

// --- Core Alias Collection Logic ---

/**
 * Recursively scans a directory to collect all module files and determines
 * the final, prioritized path for each module, respecting Vite's current mode.
 *
 * Mode-specific files (e.g., 'file.development.js' when mode is 'development') will always
 * overwrite generic files ('file.js'). Files for other modes (e.g.,
 * 'file.production.js' when mode is 'development') are ignored.
 *
 * @param {string} dir - The directory currently being scanned.
 * @param {string} mode - The current Vite build mode (e.g., 'production', 'development').
 * @param {Map<string, string>} aliasMap - The map to store the final aliases.
 * Key: module base path (e.g., 'src/utils/api').
 * Value: absolute path to the prioritized file (e.g., '/path/to/src/utils/api.development.js').
 */
function collectPrioritizedAliases(dir, mode, aliasMap) {
  const entries = fs.readdirSync(dir, { withFileTypes: true });

  // Dynamically create a regex to match files explicitly for the current mode.
  // Example for 'development' mode: /\.development\.(js|jsx|ts|tsx)$/i
  const escapedExtensions = EXTENSIONS.map(ext => `\\${ext}`).join('|');
  const currentModePattern = new RegExp(`\\.${mode}(${escapedExtensions})$`, 'i');

  for (const entry of entries) {
    const fullPath = path.join(dir, entry.name);

    if (entry.isDirectory()) {
      // Recurse into subdirectories
      collectPrioritizedAliases(fullPath, mode, aliasMap);
      continue;
    }

    const ext = path.extname(entry.name);
    if (!EXTENSIONS.includes(ext)) {
      // Skip non-module files (like .css, .md, etc.)
      continue;
    }

    // Determine base module name (e.g., 'api' from 'api.js')
    const baseMatch = entry.name.match(BASE_FILE_REGEX);
    if (!baseMatch) {
        continue;
    }
    const baseName = baseMatch[1];

    // The unique key for this module, regardless of extension or mode suffix.
    // Example: 'path/to/src/utils/api'
    const moduleKey = path.join(path.dirname(fullPath), baseName);

    // --- Strict Filtering & Prioritization ---

    // 1. Check if it's the simple, generic file (e.g., 'api.js')
    const isGenericFile = entry.name === (baseName + ext);

    // 2. Check if it's the current mode's file (e.g., 'api.development.js' when mode='development')
    const isCurrentModeFile = currentModePattern.test(entry.name);

    // Rule: Skip files that are neither generic NOR match the current mode.
    // This ignores files like 'api.production.js' when the current mode is 'development'.
    if (!isGenericFile && !isCurrentModeFile) {
        continue;
    }

    // Prioritization Rule (Robust against file system order):
    // If the module key is new, or if the current file is the mode-specific one,
    // this file wins and updates the alias map. This ensures the mode-specific
    // file always takes precedence over the generic file.
    if (!aliasMap.has(moduleKey) || isCurrentModeFile) {
        aliasMap.set(moduleKey, fullPath);
    }
  }
}

// --- Vite Configuration Export ---

export default defineConfig(({ mode }) => {
  // Resolve the absolute path to the source root directory
  const srcRoot = path.resolve(__dirname, 'src');
  const aliasMap = new Map();

  // 1. Collect all prioritized file paths into the map based on the current mode
  collectPrioritizedAliases(srcRoot, mode, aliasMap);

  // 2. Transform the absolute path map into the final Vite alias format
  const viteAliases = {};
  for (const [filePath, absolutePath] of aliasMap.entries()) {
    // Calculate the path relative to 'srcRoot' and convert backslashes for cross-platform compatibility
    const relativeKey = path.relative(srcRoot, filePath).replace(/\\/g, '/');

    // Generate the desired alias key (e.g., '@/utils/api') mapped to its absolute, resolved path.
    viteAliases[`${ALIAS_PREFIX}/${relativeKey}`] = absolutePath;
  }

  return {
    plugins: [
      // Standard plugin for React projects
      react()
    ],
    resolve: {
      alias: {
        // Must be defined first to ensure all collected aliases (e.g., '@/api' -> 'api.development.js')
        // take precedence over the generic `@` alias below.
        ...viteAliases,

        // Standard alias for the src directory itself (e.g., import { logo } from '@/assets/logo.png')
        [ALIAS_PREFIX]: srcRoot
      },
    },
  };
});
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
