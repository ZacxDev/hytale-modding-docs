# Hytale Modding Documentation

Community-maintained documentation for Hytale modding, hosted on [GitBook](https://britakee-studios.gitbook.io/hytale-modding-documentation).

## API Reference

**IMPORTANT**: See `@HYTALE_MODDING.md` for comprehensive Hytale Server API documentation including:
- Plugin lifecycle (`JavaPlugin` → `setup()` → `start()` → `shutdown()`)
- Command system (`AbstractCommand`, `CommandContext`)
- Event registration (keyed vs unkeyed events)
- Custom UI system (`.ui` file format, `InteractiveCustomUIPage`)
- Asset pack structure and paths
- Custom weapons & animations

## Project Structure

```
HYTALE_MODDING.md        # Comprehensive API reference (62K lines)
Documentation/           # All markdown documentation files
├── README.md           # Main entry point and overview
├── SUMMARY.md          # Table of contents (GitBook navigation)
├── 00-*.md             # Introduction/FAQ files
├── 01-25-*.md          # Numbered tutorial files
├── 25-server-api-reference.md  # Server API (Time, ECS, Events, etc.)
└── CHANGELOG.md        # Version history
scripts/
└── generate-llms-txt.js  # Generates llms.txt for AI consumption
```

## Commands

```bash
npm run generate:llms    # Generate Documentation/llms.txt from SUMMARY.md
```

## Content Categories

| Prefix | Category | Description |
|--------|----------|-------------|
| `00-*` | Introduction | FAQ, technical insights, YouTube links |
| `01-*` | Getting Started | Modding overview |
| `02-06, 17` | Packs | Asset packs, blocks, items, animations |
| `07-15` | Plugins | Java plugin development |
| `16` | World Gen | World generation modding |
| `18-24` | Server | Administration, configuration, commands |
| `25` | API Reference | Server API (Time, ECS, Events, Messages) |

## Writing Guidelines

- **Target audience**: Hytale modders (both content creators and Java developers)
- **Format**: GitBook-compatible markdown
- **File naming**: `NN-slug-title.md` where NN is a two-digit number
- **Update SUMMARY.md** when adding/removing pages (controls GitBook navigation)
- **Include code examples** with proper syntax highlighting (```java, ```json, ```bash)

## GitBook Conventions

- Links between pages use relative paths: `[Link Text](other-page.md)`
- Section headers in SUMMARY.md create GitBook navigation groups
- README.md in Documentation/ is the landing page

## Common Edits

**Adding a new page:**
1. Create `Documentation/NN-your-page.md`
2. Add entry to `Documentation/SUMMARY.md` in appropriate section
3. Update table of contents in `Documentation/README.md` if significant

**Updating version info:**
- Edit version table in `Documentation/README.md`
- Update `Documentation/CHANGELOG.md`

## Example Mods (Reference Implementations)

| Repository | Tech Stack | Demonstrates |
|------------|------------|--------------|
| [hytale-template-plugin](https://github.com/realBritakee/hytale-template-plugin) | Gradle + Kotlin DSL, GitHub Actions | Automated server testing, `./gradlew runServer` workflow, CI/CD releases |
| [hytale-plugin-template](https://github.com/ZacxDev/hytale-plugin-template) | Java 25, Nix flakes, Shadow JAR | Commands, events (keyed vs unkeyed), custom items with bundled assets |
| [more-arrows](https://github.com/ZacxDev/more-arrows) | Java 25, Gradle | Custom projectiles, elemental effects, particle trails, crafting recipes, asset bundles |

## External Resources

- **GitBook**: https://britakee-studios.gitbook.io/hytale-modding-documentation
- **Discord**: discord.gg/gCRv62araB (Britakee's server)
- **Partner**: [Hytale Creators](https://hytalecreators.net)
- **API Reference Source**: https://rentry.co/gykiza2m (2,959 classes documented)
