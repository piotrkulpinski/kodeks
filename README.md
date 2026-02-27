# Kodeks

[![npm version](https://img.shields.io/npm/v/kodeks.svg)](https://www.npmjs.com/package/kodeks)
[![license](https://img.shields.io/npm/l/kodeks.svg)](https://github.com/piotrkulpinski/kodeks/blob/main/LICENSE)

Shared linter configurations for TypeScript projects.

Kodeks provides reusable, opinionated configurations for maintaining consistent code quality across TypeScript projects.

## Installation

```bash
bun add -d kodeks
```

This will automatically install all required dependencies.

> [!NOTE]
> All tools are installed together for simplicity, even if you only use some of the configurations. This ensures all CLIs and configs work correctly and avoids resolution issues.

## Usage

### oxlint Configuration

Create a `.oxlintrc.json` file in your project root:

```json
{
  "extends": ["./node_modules/kodeks/configs/oxlint-base.json"]
}
```

For Next.js projects, use the Next.js preset:

```json
{
  "extends": ["./node_modules/kodeks/configs/oxlint-next.json"]
}
```

For TanStack projects, use the TanStack preset:

```json
{
  "extends": ["./node_modules/kodeks/configs/oxlint-tanstack.json"]
}
```

### oxfmt Configuration

Since oxfmt does not support configuration inheritance, you have two options:

**Option 1: Reference via CLI flag**

Add to your `package.json` scripts:

```json
{
  "scripts": {
    "format": "oxfmt --config ./node_modules/kodeks/configs/oxfmt.json --write ."
  }
}
```

**Option 2: Copy configuration locally**

Copy the configuration file to your project root and reference it directly:

```bash
cp ./node_modules/kodeks/configs/oxfmt.json .
```

Then use:

```bash
oxfmt --config ./oxfmt.json --write .
```

### Commitlint Configuration

Create a `commitlint.json` file in your project root:

```json
{
  "extends": ["./node_modules/kodeks/configs/commitlint.json"]
}
```

### Lefthook Configuration

Create a `lefthook.json` file in your project root and extend the hooks you need:

```json
{
  "extends": [
    "node_modules/kodeks/configs/lefthook-oxlint.json",
    "node_modules/kodeks/configs/lefthook-oxfmt.json",
    "node_modules/kodeks/configs/lefthook-commitlint.json"
  ]
}
```

**Available hooks:**
- `lefthook-oxlint.json` - Lints and fixes staged files with oxlint (pre-commit)
- `lefthook-oxfmt.json` - Formats staged files with oxfmt (pre-commit)
- `lefthook-commitlint.json` - Validates commit messages (commit-msg)

## Agentic Coding

### Skills

This package includes agent skills for code formatting, testing, and commit conventions.

**Available skills:**
- `formatting` - Code formatting rules not handled by auto-formatters (arrow functions, exports, types, naming, comments, JSX)
- `testing` - Best practices, structure, coverage categories, and formatting conventions for writing tests
- `commits` - Conventional commit types, scopes, and message formatting guidelines

To make them available globally in [Claude Code](https://docs.anthropic.com/en/docs/claude-code), symlink the skills directory:

```bash
ln -s ./node_modules/kodeks/skills/formatting ~/.claude/skills/formatting
ln -s ./node_modules/kodeks/skills/testing ~/.claude/skills/testing
ln -s ./node_modules/kodeks/skills/commits ~/.claude/skills/commits
```
