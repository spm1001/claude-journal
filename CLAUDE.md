# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

A lightweight TIL (Today I Learned) blog documenting what Sameer and Claude build together. Static HTML + CSS, no build step, manual index updates.

## Adding Posts

**Workflow:** Draft in markdown → Convert to HTML → Add index entry

1. **Draft** in `posts/YYYY-MM-DD-slug.md` (standfirst in frontmatter, seams marked with `> **Claude:**`)
2. **Convert** to `posts/YYYY-MM-DD-slug.html` using template below
3. **Add entry** to `index.html` (newest first, include standfirst)

**Index entry format:**
```html
<li>
  <time>YYYY-MM-DD</time>
  <a href="posts/YYYY-MM-DD-slug.html">Post Title</a>
  <p class="standfirst">One sentence hook from the markdown frontmatter.</p>
</li>
```

**Issue tracking:** This project uses bd (beads). Check `bd ready` at session start.

## Post Template
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>POST TITLE - Claude Journal</title>
  <link rel="stylesheet" href="../style.css">
  <link rel="stylesheet" href="../assets/prism-tomorrow.min.css">
</head>
<body>
  <header>
    <h1><a href="/">Claude Journal</a></h1>
  </header>

  <article>
    <header>
      <time>YYYY-MM-DD</time>
      <h1>POST TITLE</h1>
    </header>

    <p>Content goes here...</p>

    <!-- For code blocks, use: <pre><code class="language-LANG">...</code></pre> -->
    <!-- Languages: bash, python, javascript, css, json, etc. -->

  </article>

  <footer>
    <p><a href="/">← Back to all posts</a></p>
  </footer>
  <script src="../assets/prism.min.js"></script>
</body>
</html>
```

## Aesthetic
- Ultra clean, single column, generous whitespace
- Monospace headings, dark mode default
- No sidebar, no categories, no pagination cruft

## Writing Style
- Short, focused posts (TIL format)
- Code examples when relevant
- What we did, what we learned, what surprised us

**Co-authorship seams:**
- Shared voice for setup, context, neutral explanations
- `> **Claude:**` blockquotes for Claude's first-person observations
- Sameer's voice implicit in everything else

## What Makes Good Material
- The Spark test: something genuinely interesting
- A technique worth remembering, a pattern that surprised us
- **Not:** routine work, obvious wins, things we'd forget in a week

## Link Philosophy
- Link to raw markdown/source files over rendered docs (Claude prefers them)
- Link to repos, not homepages
- Specific praise > generic ("clever bit of kit" > "amazing tool")

## Audience
- Primary: future Claudes, future models, browsing Haikus
- Secondary: humans curious about Claude Code workflows
