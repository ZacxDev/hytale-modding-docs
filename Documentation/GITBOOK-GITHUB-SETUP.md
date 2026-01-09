# GitBook + GitHub Setup Guide

Complete guide to publishing your Hytale Modding documentation using GitBook connected to your GitHub repository.

---

## Prerequisites

- ✅ GitHub account
- ✅ Git installed on your computer
- ✅ Documentation files ready (already done!)

---

## Method 1: GitBook Cloud + GitHub (Recommended)

This is the easiest and most automated method. GitBook will automatically rebuild your docs whenever you push changes to GitHub.

### Step 1: Push Your Documentation to GitHub

#### 1.1 Create a New GitHub Repository

1. Go to [https://github.com/new](https://github.com/new)
2. Enter repository details:
   - **Repository name:** `hytale-modding` (or your preferred name)
   - **Description:** "Comprehensive Hytale modding documentation"
   - **Visibility:** Public or Private
   - **Initialize:** Don't add README, .gitignore, or license (you already have files)
3. Click **"Create repository"**

#### 1.2 Push Your Files to GitHub

Open terminal/command prompt in your project folder:

```bash
# Navigate to your project folder
cd C:\Users\Chris\Documents\GitHub\hytale-modding

# Initialize git (if not already done)
git init

# Add all files
git add .

# Commit the files
git commit -m "Initial commit: Hytale modding documentation"

# Add your GitHub repository as remote
git remote add origin https://github.com/YOUR-USERNAME/hytale-modding.git

# Push to GitHub
git branch -M main
git push -u origin main
```

**Replace `YOUR-USERNAME`** with your actual GitHub username.

---

### Step 2: Create GitBook Account

1. Go to [https://www.gitbook.com/](https://www.gitbook.com/)
2. Click **"Sign up"** or **"Get started"**
3. Choose **"Sign up with GitHub"** (recommended for easy integration)
4. Authorize GitBook to access your GitHub account

---

### Step 3: Create a New GitBook Space

1. After logging in, click **"New Space"** or **"Create a new space"**
2. Choose **"Import from Git"**
3. Select **"GitHub"** as the provider
4. Click **"Connect to GitHub"** if not already connected

---

### Step 4: Configure GitHub Integration

#### 4.1 Select Your Repository

1. You'll see a list of your GitHub repositories
2. Find and select: **`hytale-modding`**
3. Click **"Next"** or **"Import"**

#### 4.2 Configure Import Settings

GitBook will ask for these settings:

**Repository Settings:**
- **Branch:** `main` (or `master` if that's your default branch)
- **Root folder:** `Documentation/`
  - This tells GitBook to look in the Documentation folder for your content

**Structure Settings:**
GitBook will auto-detect:
- ✅ `README.md` - Your homepage
- ✅ `SUMMARY.md` - Table of contents for navigation
- ✅ `.gitbook.yaml` - GitBook configuration

#### 4.3 Finalize Import

1. Review the settings
2. Click **"Import"** or **"Create space"**
3. Wait for GitBook to process your documentation (usually 1-2 minutes)

---

### Step 5: Configure Your Space

#### 5.1 Space Settings

1. Go to **Space Settings** (gear icon)
2. Configure:
   - **Space name:** "Hytale Modding Documentation"
   - **Space description:** Add a description
   - **Space icon:** Upload an icon (optional)
   - **Visibility:** Public or Private

#### 5.2 Customize Domain (Optional)

**Free tier:**
- Default URL: `https://your-username.gitbook.io/hytale-modding/`

**Paid tiers:**
- Custom domain: `https://docs.yourdomain.com`

---

### Step 6: Your Docs Are Live! 🎉

Your documentation is now published and accessible at:
```
https://your-username.gitbook.io/hytale-modding/
```

**Every time you push to GitHub, GitBook automatically rebuilds!**

---

## Workflow: Making Updates

### Update Documentation Workflow

1. **Edit files locally** in your favorite editor
2. **Test locally** (optional - see Method 2 below)
3. **Commit changes:**
   ```bash
   git add .
   git commit -m "Update: Added new tutorial section"
   ```
4. **Push to GitHub:**
   ```bash
   git push origin main
   ```
5. **GitBook auto-updates** (takes 1-2 minutes)
6. **Check live site** to verify changes

---

## Method 2: Test Locally with GitBook CLI

Want to preview changes before pushing to GitHub? Use GitBook CLI.

### Install GitBook CLI

```bash
# Install Node.js first (if not installed)
# Download from: https://nodejs.org/

# Install GitBook CLI globally
npm install -g gitbook-cli
```

### Serve Locally

```bash
# Navigate to Documentation folder
cd C:\Users\Chris\Documents\GitHub\hytale-modding\Documentation

# Install GitBook plugins (first time only)
gitbook install

# Serve documentation locally
gitbook serve
```

Visit: **http://localhost:4000**

The local server has **live reload** - changes to files automatically refresh the browser!

### Build Static HTML

```bash
# Build static site
gitbook build

# Output is in _book/ folder
```

You can then:
- Upload `_book/` folder to any web host
- Use for offline documentation
- Deploy to GitHub Pages

---

## Advanced: GitHub Pages Deployment

Want to host on GitHub Pages instead of GitBook Cloud?

### Setup GitHub Pages

1. **Build your documentation:**
   ```bash
   cd Documentation
   gitbook build
   ```

2. **Copy to docs folder:**
   ```bash
   # Create docs folder in repo root
   mkdir -p ../docs
   
   # Copy built files
   cp -r _book/* ../docs/
   ```

3. **Create .nojekyll file:**
   ```bash
   # In docs folder
   cd ../docs
   touch .nojekyll
   ```
   This tells GitHub Pages not to process files with Jekyll.

4. **Commit and push:**
   ```bash
   git add docs/
   git commit -m "Add built documentation"
   git push origin main
   ```

5. **Enable GitHub Pages:**
   - Go to your repo on GitHub
   - **Settings** → **Pages**
   - **Source:** Deploy from a branch
   - **Branch:** main
   - **Folder:** /docs
   - Click **Save**

6. **Access your docs:**
   ```
   https://YOUR-USERNAME.github.io/hytale-modding/
   ```

---

## Troubleshooting

### GitBook Can't Find Documentation

**Problem:** GitBook shows empty or can't find files

**Solution:**
1. Verify `.gitbook.yaml` exists in Documentation folder
2. Check `root: ./` setting in `.gitbook.yaml`
3. Ensure `SUMMARY.md` exists
4. Check GitHub integration settings - make sure root folder is set to `Documentation/`

### Auto-Deploy Not Working

**Problem:** Changes pushed to GitHub don't update GitBook

**Solutions:**
1. Check GitBook **Integrations** settings
2. Verify GitHub connection is active
3. Check for build errors in GitBook
4. Try **"Sync with Git"** button manually
5. Check branch name matches (main vs master)

### Local Server Won't Start

**Problem:** `gitbook serve` fails

**Solutions:**
```bash
# Clear GitBook cache
rm -rf _book

# Reinstall plugins
gitbook install

# Try building first
gitbook build

# Then serve
gitbook serve
```

### Plugin Installation Fails

**Problem:** `gitbook install` errors

**Solutions:**
```bash
# Use specific Node.js version (10.x recommended for GitBook)
# Install nvm (Node Version Manager)
nvm install 10
nvm use 10

# Then retry
gitbook install
```

---

## GitBook Features in Your Docs

### What Works Out of the Box:

✅ **Navigation**
- Sidebar from SUMMARY.md
- Breadcrumbs
- Previous/Next page buttons

✅ **Search**
- Full-text search across all pages
- Instant results

✅ **Code Highlighting**
- Java, JSON, Bash syntax highlighting
- Copy code buttons

✅ **Mobile Responsive**
- Works on phones and tablets
- Touch-friendly navigation

✅ **Table of Contents**
- Auto-generated from headers
- Floating TOC on right side

---

## Customization Options

### Customize Theme (GitBook Cloud)

In GitBook web interface:
1. Go to **Customize** → **Appearance**
2. Choose:
   - Light/Dark theme
   - Color scheme
   - Logo and favicon
   - Custom CSS

### Customize Plugins (GitBook CLI)

Edit `book.json`:

```json
{
  "plugins": [
    "github",
    "edit-link",
    "prism",
    "-lunr",
    "-search",
    "search-pro"
  ]
}
```

### Add Custom Domain (GitBook Cloud - Paid)

1. Go to **Space Settings** → **Domain**
2. Add custom domain: `docs.yourdomain.com`
3. Update DNS records as instructed
4. Enable HTTPS (automatic)

---

## GitHub Repository Structure

Your repository should look like this:

```
hytale-modding/
├── .git/
├── Documentation/                    ← GitBook looks here
│   ├── .gitbook.yaml                ← GitBook config
│   ├── SUMMARY.md                   ← Navigation structure
│   ├── README.md                    ← Homepage
│   ├── book.json                    ← Plugins & settings
│   ├── 01-hytale-modding-overview.md
│   ├── 02-getting-started-with-packs.md
│   ├── ... (other tutorial files)
│   └── GITBOOK-GITHUB-SETUP.md      ← This guide
├── memory-bank/                     ← Your project files
├── README.md                        ← Repo README
└── .gitignore
```

---

## Recommended .gitignore

Create/update `.gitignore` in repository root:

```gitignore
# GitBook build output
Documentation/_book/
Documentation/node_modules/

# OS files
.DS_Store
Thumbs.db

# Editor files
.vscode/
.idea/
*.swp
*.swo
*~

# Logs
*.log
npm-debug.log*

# Temporary files
*.tmp
.temp/
```

---

## GitHub Workflow Best Practices

### Branching Strategy

```bash
# Main branch for published docs
main

# Create feature branch for large changes
git checkout -b update-plugin-tutorial
# Make changes...
git commit -m "Update plugin tutorial with new examples"
git push origin update-plugin-tutorial
# Create pull request on GitHub
# Review and merge
```

### Commit Message Convention

Good commit messages:
```
✅ "Add: New tutorial on advanced animations"
✅ "Update: Fixed typos in block state tutorial"
✅ "Fix: Corrected code examples in plugin guide"
✅ "Docs: Updated GitBook setup instructions"
```

### Protecting Your Main Branch

On GitHub:
1. **Settings** → **Branches**
2. Add **branch protection rule** for `main`
3. Enable:
   - ☑ Require pull request before merging
   - ☑ Require status checks to pass

---

## Quick Reference Commands

### Git Commands
```bash
# Check status
git status

# Add files
git add Documentation/

# Commit changes
git commit -m "Your message"

# Push to GitHub
git push origin main

# Pull latest changes
git pull origin main

# Create new branch
git checkout -b feature-name

# Switch branch
git checkout main
```

### GitBook CLI Commands
```bash
# Install plugins
gitbook install

# Serve locally (live reload)
gitbook serve

# Build static site
gitbook build

# Specify different book.json
gitbook serve --config custom-book.json
```

---

## Monitoring & Analytics

### GitBook Analytics (Paid Plans)

- Page views
- Search queries
- Popular pages
- Geographic data

### Google Analytics Integration

Add to `book.json`:
```json
{
  "plugins": ["ga"],
  "pluginsConfig": {
    "ga": {
      "token": "UA-XXXXXXXX-X"
    }
  }
}
```

---

## Support & Resources

### Official Documentation
- **GitBook Docs:** [https://docs.gitbook.com/](https://docs.gitbook.com/)
- **GitBook GitHub:** [https://github.com/GitbookIO/gitbook](https://github.com/GitbookIO/gitbook)
- **GitHub Docs:** [https://docs.github.com/](https://docs.github.com/)

### Community
- GitBook Community Forum
- GitHub Discussions (on your repo)
- Stack Overflow (tag: gitbook)

---

## Summary: Quick Start

**Fastest path to published docs:**

1. **Push to GitHub**
   ```bash
   git init
   git add .
   git commit -m "Initial docs"
   git remote add origin https://github.com/USERNAME/hytale-modding.git
   git push -u origin main
   ```

2. **Connect GitBook**
   - Go to [gitbook.com](https://www.gitbook.com/)
   - Sign up with GitHub
   - Import your repository
   - Set root folder to `Documentation/`

3. **Done!** 🎉
   - Your docs are live at: `https://username.gitbook.io/hytale-modding/`
   - Updates auto-deploy when you push to GitHub

---

**That's it! Your documentation is now version-controlled, automatically deployed, and beautifully formatted!** 📚✨
