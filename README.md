# 📚 SecByte Blog - Automated Content Sync

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Blog Sync](https://github.com/SujitMahakhud/secbyte-blog/actions/workflows/blog-sync.yml/badge.svg)](https://github.com/SujitMahakhud/secbyte-blog/actions/workflows/blog-sync.yml)

Automated synchronization of blog content from [www.secbyte.in](https://www.secbyte.in) - Your source for Microsoft Security, KQL, Microsoft Sentinel & Cybersecurity insights by Sujit Mahakhud.

## 🎯 Overview

This repository automatically syncs and archives blog posts from SecByte.in, making the content version-controlled, searchable, and accessible in Markdown format. The automation runs every 12 hours via GitHub Actions.

## 📂 Repository Structure

```
secbyte-blog/
├── .github/
│   └── workflows/
│       └── blog-sync.yml          # Automation workflow
├── content/
│   └── posts/                     # Blog posts in Markdown
│       ├── post-slug-1.md
│       ├── post-slug-2.md
│       └── ...
├── .gitignore
├── LICENSE
└── README.md
```

## 🔄 How It Works

1. **Scheduled Automation**: GitHub Actions workflow runs every 12 hours (configurable)
2. **Content Fetching**: Scrapes blog posts from www.secbyte.in
3. **Markdown Conversion**: Converts HTML content to Markdown with frontmatter
4. **Version Control**: Automatically commits new/updated posts to this repository

## 🚀 Features

- ✅ Automated blog post synchronization
- ✅ Markdown format with YAML frontmatter
- ✅ Preserves metadata (title, date, author, categories)
- ✅ Version-controlled content history
- ✅ Manual trigger support via workflow_dispatch
- ✅ MIT License for open collaboration

## 📝 Blog Post Format

Each blog post is stored as a Markdown file with frontmatter:

```markdown
---
title: "Blog Post Title"
date: 2025-01-22
author: Sujit Mahakhud
categories: [Microsoft Security, KQL, Sentinel]
tags: [cybersecurity, automation]
original_url: https://www.secbyte.in/post-slug
---

Blog content here...
```

## 🛠️ Manual Workflow Trigger

You can manually trigger the sync workflow:
1. Go to the [Actions tab](../../actions)
2. Select "Blog Sync Automation"
3. Click "Run workflow"

## 📊 Content Categories

- **Microsoft Security** - Best practices and security insights
- **KQL (Kusto Query Language)** - Query examples and tutorials  
- **Microsoft Sentinel** - SIEM configuration and detection rules
- **Cybersecurity** - General security insights and career guidance
- **Automation** - Security automation and orchestration

## 🔗 Links

- **Blog Website**: [www.secbyte.in](https://www.secbyte.in)
- **Author**: Sujit Mahakhud
- **Repository**: [github.com/SujitMahakhud/secbyte-blog](https://github.com/SujitMahakhud/secbyte-blog)

## 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🤝 Contributing

This is an automated archive repository. For blog content contributions or suggestions, please visit [www.secbyte.in](https://www.secbyte.in) or contact the author directly.

## ⚙️ Setup & Development

### Prerequisites
- Python 3.10+
- Required packages: `requests`, `beautifulsoup4`, `pyyaml`

### Local Testing
```bash
# Clone the repository
git clone https://github.com/SujitMahakhud/secbyte-blog.git
cd secbyte-blog

# Install dependencies
pip install requests beautifulsoup4 pyyaml

# Run sync script (to be added)
python scripts/sync_blog.py
```

## 📈 Status

- **Last Sync**: Automated via GitHub Actions
- **Total Posts**: Check [content/posts/](content/posts/) directory
- **Sync Frequency**: Every 12 hours (customizable)

## 📧 Contact

For questions or collaboration:
- **Website**: [www.secbyte.in](https://www.secbyte.in)
- **GitHub**: [@SujitMahakhud](https://github.com/SujitMahakhud)

---

**Note**: This is an automated content archive. All blog posts are authored by Sujit Mahakhud and originally published on [www.secbyte.in](https://www.secbyte.in).
