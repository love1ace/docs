# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This repository contains documentation for Byul AI's financial news API with two main components:
1. **Mintlify Documentation Site** (`/docs/`) - REST API and WebSocket documentation 
2. **MCP Server Package** (`/byul-mcp/`) - Model Context Protocol server for AI tool integrations

## Development Commands

### Documentation Site (docs/)
- `pnpm dev` or `mintlify dev` - Start local development server at http://localhost:3000
- `pnpm build` or `mintlify build` - Build documentation site for production
- `mintlify broken-links` - Validate all links for broken references
- `mintlify update` - Update Mintlify CLI to latest version

### MCP Server (byul-mcp/)
- `pnpm build` - Compile TypeScript to dist/ with postbuild script
- `pnpm dev` - Run development server with tsx
- `pnpm typecheck` - Type checking without compilation
- `pnpm start` - Run compiled server from dist/

### Package Management
- Both projects use pnpm as package manager
- Documentation uses pnpm@10.8.0, MCP server uses pnpm@9

## Architecture

### Documentation Site Structure
- `docs.json` - Main configuration defining dual-tab navigation (REST API, WebSocket)
- Theme colors: primary #4338ca, light #7c3aed, dark #5b21b6
- Navigation organized by API type with Getting Started, Examples, and Error Handling sections
- Content structure:
  - REST API: quickstart, authentication, data formats, filtering, examples (curl, Node.js, Python, PHP, React)
  - WebSocket: connection, subscription, real-time data, error handling, examples
  - Shared: API reference with OpenAPI specification

### MCP Server Architecture
- **Entry points**: CLI (`src/cli.ts`) and programmatic (`src/index.ts`)
- **Protocol**: stdio-based MCP server implementing Model Context Protocol v1.2.0
- **API Proxy**: Forwards requests to Byul REST API endpoints
- **Tools**: `news_fetch` with comprehensive filtering options
- **Resources**: `byul://news` resource with URL-based parameter passing
- **Response format**: Summary string + original JSON payload from Byul API

### Key Technologies
- **Documentation**: Mintlify framework with MDX content
- **MCP Server**: TypeScript with @modelcontextprotocol/sdk, Zod validation
- **Build**: TypeScript compilation with custom postbuild script
- **Runtime**: Node.js 18+ required for MCP server

### API Key Guidelines
- **ALWAYS use `byul_api_key` as the example API key format** in all documentation and code examples
- **NEVER use** `byul_v2_your_api_key`, `byul_v2_your_api_key_here`, or any other variations
- This standardized format ensures consistency across all WebSocket documentation and examples
- When creating new documentation or updating existing files, search for and replace any non-standard API key formats with `byul_api_key`