# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Slidev presentation about "Metaprogramming in Rust" by Krzysztof Atłasik. Slidev is a presentation framework that uses Markdown for slide content with Vue components and theme support.

## Commands

### Development
```bash
pnpm install    # Install dependencies
pnpm dev        # Start dev server with auto-open (default: http://localhost:3030)
```

### Build & Export
```bash
pnpm build      # Build for production (outputs to ./dist)
pnpm export     # Export slides to PDF or other formats
```

## Architecture

- **slides.md**: Main presentation content using Markdown with Slidev frontmatter
  - Theme: `bricks`
  - Supports Mermaid diagrams, MDC syntax, and slide transitions
  - Duration: 45 minutes

- **Themes**: Uses multiple Slidev themes (@slidev/theme-bricks, theme-default, theme-seriph, slidev-theme-takahashi)

- **Deployment**: Configured for both Netlify and Vercel
  - Build output: `dist/`
  - Build command: `npm run build`
  - SPA routing configured with rewrites to index.html

## Editing Slides

All slide content is in `slides.md`. The file uses:
- YAML frontmatter for configuration
- Markdown for content
- `---` separators between slides
- Support for Mermaid diagrams and Vue components
