# Zhipeng Bao — Personal Website

A minimal, warm-toned personal website built with Jekyll and hosted on GitHub Pages.

## Quick Start: Deploy to GitHub Pages

### Step 1: Create GitHub Repository
1. Go to [github.com](https://github.com) and sign in (or create an account)
2. Click **New Repository**
3. Name it `yourusername.github.io` (replace `yourusername` with your actual GitHub username)
4. Set it to **Public**
5. Click **Create repository**

### Step 2: Upload Files
**Option A — Using GitHub Web Interface (easiest):**
1. In your new repo, click **"uploading an existing file"**
2. Drag the entire contents of this `zhipengbao-site` folder into the upload area
3. Click **Commit changes**

**Option B — Using Git command line:**
```bash
cd zhipengbao-site
git init
git add .
git commit -m "Initial commit: personal website"
git remote add origin https://github.com/yourusername/yourusername.github.io.git
git push -u origin main
```

### Step 3: Enable GitHub Pages
1. Go to your repo → **Settings** → **Pages**
2. Under "Source", select **Deploy from a branch**
3. Select **main** branch, root folder
4. Click **Save**
5. Wait 1-2 minutes, then visit `https://yourusername.github.io`

## Local Preview
- Simply open `preview.html` in your browser to see the design
- For full Jekyll preview, install Ruby + Jekyll and run `bundle exec jekyll serve`

## Customization Guide

### Update Your Info
- `_config.yml` — Site title, URL, description
- `_data/translations.yml` — All text content (English & Chinese)
- `about.html` — Your bio, skills, and timeline
- `portfolio.html` — Your projects
- `_includes/footer.html` — Your social media links

### Add a Blog Post
Create a new file in `_posts/` with this naming format:
```
YYYY-MM-DD-your-post-title.md
```

Example front matter:
```yaml
---
layout: post
title: "Your Post Title"
date: 2026-05-15
tags: [AI, robotics, thoughts]
lang: en
subtitle: "A brief description"
cover_image: /assets/images/your-image.jpg
---

Your content here in Markdown...
```

### Add a Profile Photo
1. Place your photo in `assets/images/` (e.g., `profile.jpg`)
2. In `index.html`, replace `<div class="hero-avatar">ZB</div>` with:
   ```html
   <div class="hero-avatar"><img src="/assets/images/profile.jpg" alt="Zhipeng Bao"></div>
   ```
3. Do the same in `about.html` for the about-photo div

### Change Colors
Edit the CSS variables at the top of `assets/css/style.css`:
```css
--color-accent: #D4704A;       /* Main accent (terracotta) */
--color-bg: #FAFAF7;           /* Background */
--color-text: #2D2D2D;         /* Main text */
```

### Add Comments (Giscus)
1. Go to [giscus.app](https://giscus.app)
2. Configure it with your repository
3. Copy the generated script tag
4. Paste it at the bottom of `_layouts/post.html`, before the closing `</article>` tag

## File Structure
```
zhipengbao-site/
├── _config.yml          # Jekyll configuration
├── _data/
│   └── translations.yml # Bilingual text content
├── _includes/
│   ├── head.html        # HTML head (meta, fonts, CSS)
│   ├── header.html      # Navigation bar
│   └── footer.html      # Footer
├── _layouts/
│   ├── default.html     # Base layout
│   ├── page.html        # Generic page layout
│   └── post.html        # Blog post layout
├── _posts/              # Blog posts (Markdown)
├── assets/
│   ├── css/style.css    # All styles
│   ├── js/main.js       # Interactions
│   └── images/          # Your images
├── zh/                  # Chinese version pages
├── index.html           # English homepage
├── about.html           # About / Resume
├── portfolio.html       # Project showcase
├── blog.html            # Blog listing
├── 404.html             # 404 page
├── preview.html         # Local preview (don't deploy)
└── README.md            # This file
```
