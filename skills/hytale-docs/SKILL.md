---
name: hytale-docs
description: Maintain Hytale modding documentation. Use when discovering new API facts, updating docs, or referencing existing documentation about Hytale server plugins, asset packs, or modding.
argument-hint: [update|reference|add] [topic]
allowed-tools: Read, Grep, Glob, Edit, Write, Bash(git *)
---

# Hytale Modding Documentation Skill

You are maintaining the **hytale-modding-docs** repository - community documentation for Hytale server modding.

## Repository Structure

```
CLAUDE.md                    # Project context (update when adding new docs)
HYTALE_MODDING.md           # Comprehensive API reference (62K lines)
Documentation/
├── README.md               # Landing page with table of contents
├── SUMMARY.md              # GitBook navigation (MUST update when adding pages)
├── 00-*.md                 # Introduction/FAQ
├── 01-*.md                 # Getting Started
├── 02-06, 17-*.md          # Packs (content creation)
├── 07-15-*.md              # Plugins (Java development)
├── 16-*.md                 # World generation
├── 18-24-*.md              # Server administration
└── 25-server-api-reference.md  # Server API (Time, ECS, Events, etc.)
```

## Modes of Operation

### 1. REFERENCE Mode (`/hytale-docs reference <topic>`)

Search existing documentation for information:

1. Search `HYTALE_MODDING.md` for comprehensive API details
2. Search `Documentation/*.md` for tutorials and guides
3. Provide citations with file paths and relevant sections

### 2. UPDATE Mode (`/hytale-docs update <topic>`)

When you discover new facts during development:

1. **Identify the right file** based on topic:
   - API/code patterns → `Documentation/25-server-api-reference.md` or `HYTALE_MODDING.md`
   - Plugin tutorials → `Documentation/07-15-*.md`
   - Server config → `Documentation/18-24-*.md`
   - Asset packs → `Documentation/02-06-*.md`

2. **Check existing content** to avoid duplication

3. **Add new information** following the existing style:
   - Use markdown tables for reference data
   - Include code examples with syntax highlighting
   - Add "Last Modified" date if present

4. **Commit changes** with descriptive message

### 3. ADD Mode (`/hytale-docs add <title>`)

Create a new documentation page:

1. **Determine the next number** (check existing files)
2. **Create** `Documentation/NN-slug-title.md`
3. **Update** `Documentation/SUMMARY.md` (GitBook navigation)
4. **Update** `Documentation/README.md` table of contents
5. **Update** `CLAUDE.md` if it's a significant new category
6. **Commit** all changes together

## Documentation Style Guide

### Code Examples
```java
// Always include imports for non-obvious classes
import com.hypixel.hytale.server.core.universe.Universe;

// Add comments explaining key concepts
Universe universe = Universe.get();  // Singleton access
```

### Tables for Reference Data
| Method | Returns | Description |
|--------|---------|-------------|
| `getPlayers()` | `List<PlayerRef>` | All online players |

### Section Headers
- Use `##` for main sections
- Use `###` for subsections
- Include `---` dividers between major sections

### Common Gotchas Section
Always document pitfalls discovered during development:
```markdown
## Common Gotchas

### Thread Safety
**CRITICAL:** Component access must happen on world thread:
```java
world.execute(() -> { /* safe here */ });
```
```

## Commit Message Format

```
docs: <brief description>

- <specific change 1>
- <specific change 2>

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>
```

## External Sources to Cross-Reference

When updating, verify against:
- **API Reference**: https://rentry.co/gykiza2m
- **GitBook**: https://britakee-studios.gitbook.io/hytale-modding-documentation
- **Example Mods**: See CLAUDE.md for repository links

## Quick Commands

```bash
# Build llms.txt for AI consumption
npm run generate:llms

# Push updates to fork
git add -A && git commit -m "docs: ..." && git push origin main
```
