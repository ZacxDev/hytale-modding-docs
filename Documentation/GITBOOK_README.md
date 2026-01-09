# GitBook Setup Instructions

## âœ… GitBook Compatibility

Your Hytale Modding documentation is now **fully compatible** with GitBook! Here's what was added:

### Files Added for GitBook:

1. **SUMMARY.md** - GitBook's table of contents structure
2. **.gitbook.yaml** - GitBook configuration (for GitBook Cloud)
3. **book.json** - GitBook legacy configuration (for GitBook CLI)
4. **GITBOOK_README.md** - This setup guide

---

## Setting Up with GitBook

### Option 1: GitBook Cloud (Recommended)

1. **Create a GitBook Space:**
   - Go to [https://www.gitbook.com/](https://www.gitbook.com/)
   - Sign up or log in
   - Click "New Space" â†’ "Import from GitHub"

2. **Connect Your Repository:**
   - Select your GitHub repository
   - Choose the `Documentation` folder as the root
   - GitBook will automatically detect `.gitbook.yaml`

3. **Publish:**
   - GitBook will build and publish your documentation
   - You'll get a public URL like: `https://yourspace.gitbook.io/`

### Option 2: GitBook CLI (Legacy)

1. **Install GitBook CLI:**
   ```bash
   npm install -g gitbook-cli
   ```

2. **Navigate to Documentation Folder:**
   ```bash
   cd Documentation
   ```

3. **Install Plugins:**
   ```bash
   gitbook install
   ```

4. **Serve Locally:**
   ```bash
   gitbook serve
   ```
   Open http://localhost:4000 in your browser

5. **Build Static Site:**
   ```bash
   gitbook build
   ```
   Output will be in `_book/` folder

---

## GitBook Features Enabled

### Navigation
- âœ… Sidebar navigation (from SUMMARY.md)
- âœ… Expandable chapters
- âœ… Back to top button
- âœ… Previous/Next page links

### Search
- âœ… Enhanced search (search-pro plugin)
- âœ… Full-text search across all pages

### Code Highlighting
- âœ… Syntax highlighting for Java, JSON, Bash
- âœ… Prism.js for better code rendering

### GitHub Integration
- âœ… "Edit on GitHub" links on each page
- âœ… GitHub repository link in sidebar

### Additional Features
- âœ… Anchor links on headings
- âœ… Responsive design
- âœ… Mobile-friendly

---

## Customization

### Editing SUMMARY.md

The `SUMMARY.md` file controls the sidebar structure. Format:

```markdown
# Table of Contents

* [Page Title](filename.md)
  * [Subpage](subfolder/file.md)
```

### Updating book.json

Edit `book.json` to:
- Change title and description
- Add/remove plugins
- Configure plugin settings
- Update GitHub URLs

### Styling

Create `styles/website.css` for custom styles:

```css
/* Custom CSS for GitBook */
.book-summary {
  /* Sidebar styles */
}
```

---

## Folder Structure

```
Documentation/
â”œâ”€â”€ .gitbook.yaml          # GitBook Cloud config
â”œâ”€â”€ book.json              # GitBook CLI config
â”œâ”€â”€ SUMMARY.md             # Table of contents
â”œâ”€â”€ README.md              # Introduction page
â”œâ”€â”€ GITBOOK_README.md      # This file
â”‚
â”œâ”€â”€ 01-hytale-modding-overview.md
â”œâ”€â”€ 02-getting-started-with-packs.md
â”œâ”€â”€ 03-adding-a-block.md
â”œâ”€â”€ 04-block-state-changing.md
â”œâ”€â”€ 05-item-categories.md
â”œâ”€â”€ 06-block-animations.md
â”œâ”€â”€ 07-getting-started-with-plugins.md
â”œâ”€â”€ 08-custom-config-files.md
â”œâ”€â”€ 09-bootstrap-early-plugins.md
â””â”€â”€ 10-useful-tools-and-links.md
```

---

## GitBook Plugins Included

### Core Plugins:
- **edit-link** - "Edit this page" button
- **prism** - Enhanced code highlighting
- **github** - GitHub integration
- **anchorjs** - Anchor links on headings
- **expandable-chapters-small** - Collapsible sidebar sections
- **back-to-top-button** - Scroll to top button
- **search-pro** - Advanced search

### Disabled Plugins:
- `-highlight` - Replaced by Prism
- `-search` - Replaced by search-pro
- `-lunr` - Replaced by search-pro

---

## Publishing Options

### 1. GitBook Cloud
**Pros:** 
- Easy setup
- Automatic builds
- Custom domain support
- Built-in hosting

**Cons:**
- Requires account
- Some features are paid

### 2. GitHub Pages
**Steps:**
1. Build with `gitbook build`
2. Copy `_book/` contents to `docs/` folder
3. Enable GitHub Pages in repository settings
4. Choose `docs/` as source

### 3. Self-Hosted
**Steps:**
1. Build with `gitbook build`
2. Upload `_book/` folder to your web server
3. Configure web server to serve static files

---

## Updating Documentation

### Adding New Pages:

1. **Create Markdown File:**
   ```bash
   touch Documentation/11-new-topic.md
   ```

2. **Add to SUMMARY.md:**
   ```markdown
   * [New Topic](11-new-topic.md)
   ```

3. **Rebuild (if using CLI):**
   ```bash
   gitbook build
   ```

### Modifying Existing Pages:

1. Edit the `.md` file
2. GitBook Cloud: Changes auto-deploy on git push
3. GitBook CLI: Rerun `gitbook serve` or `gitbook build`

---

## Troubleshooting

### Plugin Installation Fails:
```bash
# Clear cache and reinstall
rm -rf node_modules
gitbook install
```

### Build Errors:
```bash
# Check for syntax errors in JSON files
jsonlint book.json

# Validate SUMMARY.md structure
cat SUMMARY.md
```

### Broken Links:
- Use relative paths: `[Link](file.md)`
- Not absolute: `[Link](/Documentation/file.md)`

---

## Best Practices

### âœ… Do:
- Use relative links between pages
- Keep SUMMARY.md updated
- Test locally before deploying
- Use consistent heading levels
- Add alt text to images

### âŒ Don't:
- Use absolute file paths
- Skip SUMMARY.md entries
- Mix tab/space indentation
- Forget to escape special characters in JSON

---

## Resources

- **GitBook Documentation:** [https://docs.gitbook.com/](https://docs.gitbook.com/)
- **GitBook GitHub:** [https://github.com/GitbookIO/gitbook](https://github.com/GitbookIO/gitbook)
- **Plugin List:** [https://plugins.gitbook.com/](https://plugins.gitbook.com/)
- **Markdown Guide:** [https://www.markdownguide.org/](https://www.markdownguide.org/)

---

## Quick Start Commands

```bash
# Install GitBook CLI globally
npm install -g gitbook-cli

# Navigate to Documentation folder
cd Documentation

# Install plugins from book.json
gitbook install

# Serve locally (with live reload)
gitbook serve

# Build static site
gitbook build

# Build and view
gitbook build && open _book/index.html
```

---

## Next Steps

1. âœ… Files are GitBook-ready
2. Choose your publishing method (Cloud or CLI)
3. Customize `book.json` with your repository URLs
4. Update GitHub URLs in edit-link plugin
5. Add any custom styling
6. Test locally before deploying
7. Publish and share with the community!

---

**Your documentation is now ready for GitBook! ðŸŽ‰**

All existing Markdown files are compatible and will render beautifully in GitBook's interface.
