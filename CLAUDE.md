# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Mintlify documentation site built using the Mintlify CLI framework. The project is structured as a documentation website with MDX-based content pages, API reference documentation, and AI tools guides.

## Development Commands

### Local Development
- `mint dev` or `npm run dev` - Start local development server at http://localhost:3000
- `mint dev --port 3333` - Run on custom port (3333 in this example)
- `mint build` or `npm run build` - Build the documentation site for production

### Maintenance
- `mint update` - Update Mintlify CLI to latest version
- `mint broken-links` - Validate all links in documentation for broken references

### Package Management
- Uses pnpm as package manager (specified in package.json: `"packageManager": "pnpm@10.8.0"`)
- Install dependencies with `pnpm install`

## Architecture

### Core Configuration
- `docs.json` - Main configuration file defining navigation structure, theme colors, branding, and site metadata
- Theme uses mint colors: primary #16A34A, light #07C983, dark #15803D
- Two-tab navigation: "Guides" and "API reference"

### Content Structure
- **Root pages**: index.mdx, quickstart.mdx, development.mdx
- **Guides sections**:
  - Getting started (index, quickstart, development)
  - Customization (settings, navigation) 
  - Writing content (markdown, code, images, snippets)
  - AI tools (cursor, claude-code, windsurf)
- **API reference**: Introduction and endpoint examples (GET, POST, DELETE, webhook)
- **Static assets**: Images, logos (light/dark variants), favicon

### File Types
- `.mdx` files for all content pages (MDX = Markdown + JSX components)
- `openapi.json` for API specification
- Standard web assets (SVG logos, PNG images)

## Development Patterns

### MDX Components
The site uses Mintlify-specific MDX components like:
- `<Card>` for featured content blocks
- `<Columns>` for multi-column layouts  
- `<Steps>` for step-by-step instructions
- `<Info>`, `<Frame>`, `<AccordionGroup>`, `<Accordion>` for content organization

### Navigation Management
- Navigation structure is entirely controlled via `docs.json` 
- Pages are referenced by their filename without extension
- Nested folder structure reflects in dot notation (e.g., "essentials/settings")

### Content Guidelines
- All content pages require frontmatter with `title` and `description`
- Code examples use language-specific syntax highlighting
- Images should be placed in `/images/` directory
- Reusable content goes in `/snippets/` directory