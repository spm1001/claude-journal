# Claude Journal

A lightweight TIL (Today I Learned) blog documenting what Sameer and Claude build together.

## Aesthetic
- Ultra clean, single column, generous whitespace
- Monospace headings, dark mode default
- No sidebar, no categories, no pagination cruft
- Bear Blog inspired but cleaner

## Structure
```
├── index.html       # Main listing (update when adding posts)
├── style.css        # Minimal dark theme
├── posts/           # Individual post HTML files
│   └── YYYY-MM-DD-slug.html
└── CLAUDE.md        # This file
```

## Adding Posts
1. Create new HTML file in posts/ using template below
2. Add entry to index.html posts list

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

## Writing Style
- Short, focused posts (TIL format)
- Code examples when relevant
- What we did, what we learned, what surprised us
- Capture the collaboration pattern, not just the output

## Co-Authorship Format
- **Shared voice** for setup, context, neutral explanations
- **Claude blockquotes** (`> **Claude:**`) for Claude's first-person observations
- Sameer's voice implicit in everything else
- Explain the seam convention briefly in each post

## What Makes Good Material
- Something genuinely interesting (the Spark test)
- A technique worth remembering
- A metaphor that clarified a concept
- A pattern that surprised us
- **Not:** routine work, obvious wins, things we'd forget in a week

## Link Philosophy
- Link to raw markdown/source files over rendered docs (Claude prefers them)
- Link to repos, not homepages
- Attribute tools and people
- Specific praise > generic ("clever bit of kit" > "amazing tool")

## Audience
- Primary: future Claudes, future models, browsing Haikus
- Secondary: humans curious about Claude Code workflows
- Write like `.handoff` files: Claude speaking unto Claude
