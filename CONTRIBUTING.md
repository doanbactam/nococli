# Contributing to nococli

We appreciate your contributions! Here's how to help.

## Development

1. Clone the repository
2. Install dependencies: `bun install` or `pnpm install`
3. Build: `bun run build` or `pnpm run build`
4. Test: `bun test` or `pnpm test`

## Important: Remove AI Signatures from PRs

When submitting PRs using tools like `/ship` (gstack), **please manually remove** any AI attribution lines like:

- `🤖 Generated with Claude Code`
- Any co-author footers from AI tools

This is especially important for **nococli** — a tool dedicated to removing AI signatures from commits. Our PRs should reflect that principle.

### Before Submitting

Check your PR description and commit messages:
```bash
# View your commits
git log --oneline origin/master..HEAD

# Check PR body for AI footers
# Edit PR if needed
```

## Code Style

- TypeScript for all source files
- Follow existing patterns in `src/`
- Build before pushing: `bun run build`

## Testing

- Run tests: `bun test`
- Test e2e: `bun run test:e2e`

Thank you for keeping nococli authentic! 🚀
