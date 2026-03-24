# User Testing

Testing surface and validation approach for nococli.

**What belongs here:** Testing surface, required testing tools, resource cost classification.

---

## Validation Surface

nococli is a CLI tool. The testing surface is:

1. **CLI Commands** - Test via terminal/shell
   - `npx nococli` (default install)
   - `npx nococli install`
   - `npx nococli uninstall`
   - `npx nococli status`
   - `npx nococli patterns`

2. **Git Hook Execution** - Test via git commit workflow
   - Hook removes AI signatures from commit messages
   - Hook preserves human co-authors
   - Hook handles edge cases

## Required Testing Tools

| Tool | Purpose | Installation |
|------|---------|--------------|
| Bun | Unit test runner | Built-in |
| Git | Hook testing | System dependency |
| Bash | Hook execution | System dependency |

## Testing Approach

### Unit Tests
- Location: `src/**/*.test.ts`
- Runner: `bun test`
- Coverage: Pattern matching, hook generation

### E2E Tests
- Location: `scripts/e2e-*.mjs`
- Runner: `bun run test:e2e`
- Coverage: Installation, hook functionality

### Manual Testing
- Create temporary git repositories
- Install hook
- Make commits with various AI signatures
- Verify signatures removed

## Resource Cost Classification

| Surface | Resource Usage | Max Concurrent |
|---------|---------------|----------------|
| CLI testing | Minimal (~50MB) | 5 |
| Git hook testing | Low (~100MB per test repo) | 3 |

**Rationale:**
- CLI commands are lightweight, no long-running processes
- Git operations create temporary repos but are quick
- No heavy services or browser instances needed

## Testing Isolation

E2E tests use isolated environments:
- Create temp directories for each test
- Set isolated HOME to avoid affecting user config
- Clean up all temp files after tests

```javascript
// Example from e2e-install.mjs
const tempRoot = fs.mkdtempSync(path.join(os.tmpdir(), 'nococli-e2e-'));
function createHome(name) {
  const homeDir = path.join(tempRoot, name);
  fs.mkdirSync(homeDir, { recursive: true });
  return { HOME: homeDir, USERPROFILE: homeDir };
}
```

## Validation Concurrency

Based on dry run observations:
- **Max concurrent validators for git hook testing: 3**
- **Max concurrent validators for CLI commands: 5**

Each flow validator should use its own isolated temp directory to avoid conflicts.

---

## Flow Validator Guidance: CLI/Git Hook Testing

**Isolation Rules:**
1. Each validator must create its own isolated temp directory under the system temp folder
2. Each validator must set isolated HOME environment variable to avoid affecting user's real git config
3. Clean up all temp files and directories after testing completes

**Testing Approach:**
1. Build the project: `bun run build`
2. Create isolated test environment:
   ```javascript
   const tempRoot = fs.mkdtempSync(path.join(os.tmpdir(), 'nococli-flow-<unique-id>'));
   const homeDir = path.join(tempRoot, 'home');
   fs.mkdirSync(homeDir, { recursive: true });
   ```
3. Set environment variables: `{ HOME: homeDir, USERPROFILE: homeDir }`
4. Run installation: `node dist/cli.js install`
5. Create test git repo within the temp directory
6. Make commits with various AI signature patterns
7. Verify signatures are correctly removed/preserved

**Shared State to Avoid:**
- User's real git config (`~/.gitconfig`)
- Global git template directory
- Any repository outside the temp directory

**Constraints:**
- Do NOT modify user's actual global git config
- Do NOT run tests against production/user repositories
- All file operations must be within the temp directory
