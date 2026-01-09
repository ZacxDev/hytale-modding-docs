# Quick Start Guide

## 🚀 GitBook + GitHub in 3 Steps

### Step 1: Push to GitHub
```bash
cd C:\Users\Chris\Documents\GitHub\hytale-modding
git init
git add .
git commit -m "Initial commit: Hytale modding docs"
git remote add origin https://github.com/YOUR-USERNAME/hytale-modding.git
git push -u origin main
```

### Step 2: Connect to GitBook
1. Go to [gitbook.com](https://www.gitbook.com/)
2. Sign up with GitHub
3. Click "New Space" → "Import from Git"
4. Select your `hytale-modding` repository
5. Set root folder: `Documentation/`
6. Click "Import"

### Step 3: Done! 🎉
Your docs are live at:
```
https://your-username.gitbook.io/hytale-modding/
```

---

## 📝 Making Updates

```bash
# 1. Edit your files locally
# 2. Commit changes
git add .
git commit -m "Updated tutorial"

# 3. Push to GitHub
git push origin main

# 4. GitBook auto-updates (1-2 minutes)
```

---

## 🔧 Test Locally (Optional)

```bash
npm install -g gitbook-cli
cd Documentation
gitbook install
gitbook serve
```

Visit: http://localhost:4000

---

## 📚 Full Guide

For detailed instructions, see: [GITBOOK-GITHUB-SETUP.md](GITBOOK-GITHUB-SETUP.md)

---

## ✅ Your Files Are Ready

- ✅ SUMMARY.md - Navigation structure
- ✅ README.md - Homepage
- ✅ .gitbook.yaml - GitBook config
- ✅ book.json - Plugins & settings
- ✅ All tutorial files
- ✅ GitBook compatible

**Just push to GitHub and connect to GitBook!**
