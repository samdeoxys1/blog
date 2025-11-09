# SEO Checklist for Google Indexing

## ✅ Completed
1. Created `robots.txt` to allow search engine crawling
2. Added SEO-friendly metadata to `_config.yml`
3. You already have:
   - `jekyll-sitemap` plugin (generates sitemap.xml)
   - `jekyll-seo-tag` plugin (generates meta tags)
   - Google Search Console verification file

## 🔧 Next Steps (Critical)

### 1. Commit and Push Changes
```bash
git add robots.txt _config.yml
git commit -m "Add SEO improvements for Google indexing"
git push origin main
```

### 2. Verify Site is Live
Visit: https://samdeoxys1.github.io/blog/
- Check that your site loads correctly
- Verify sitemap exists at: https://samdeoxys1.github.io/blog/sitemap.xml

### 3. Submit to Google Search Console
1. Go to: https://search.google.com/search-console
2. Verify you've added the property for `https://samdeoxys1.github.io/blog/`
3. Submit your sitemap:
   - URL: `https://samdeoxys1.github.io/blog/sitemap.xml`
4. Request indexing for your main pages:
   - Home: `https://samdeoxys1.github.io/blog/`
   - Individual blog posts

### 4. Check robots.txt Accessibility
After deploying, verify at: https://samdeoxys1.github.io/blog/robots.txt
It should show your robots.txt content allowing all crawlers.

## 📝 Optional Improvements

### Add Descriptions to Posts
Add `description` or `excerpt` to post front matter for better snippets:
```yaml
---
title: "Your Post Title"
description: "A brief description that will appear in search results"
date: 2025-06-28
categories: mathematical_think_throughs
---
```

### Add Structured Data
Your jekyll-seo-tag plugin should handle this, but verify by:
1. Visit: https://search.google.com/test/rich-results
2. Enter your blog URL
3. Check for errors

### Build Backlinks
Google ranks sites higher with external links:
- Share posts on social media
- Link from your university profile
- Guest post or comment on related blogs
- Add to your email signature

## ⏰ Timeline

- **Initial crawl**: 1-4 weeks after submission
- **Ranking improvements**: 2-6 months with regular content
- **Check status**: Use "site:samdeoxys1.github.io/blog" in Google search

## 🐛 If Still Not Indexed After 2 Weeks

1. Check Google Search Console for:
   - Coverage errors
   - Manual actions
   - Crawl stats

2. Verify no duplicate content issues (dates in filenames look suspicious - 2025 dates?)

3. Check if GitHub Pages is blocking crawlers (unlikely but possible)

4. Use Google's URL Inspection tool for specific pages

