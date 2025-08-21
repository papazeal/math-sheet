# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Math Sheet Generator web app built with Astro and Svelte. It generates printable math worksheets with randomized problems (addition, subtraction, multiplication, division) across different difficulty levels and number types (integers, decimals, fractions).

## Commands

**Development:**
- `pnpm dev` - Start development server with hot reload
- `pnpm build` - Build for production
- `pnpm preview` - Preview production build locally

**Package Management:**
- Use `pnpm` exclusively for all package operations

## Architecture

**Framework Stack:**
- **Astro**: Static site generator with partial hydration
- **Svelte**: Component library for interactive UI elements  
- **TailwindCSS**: Utility-first CSS framework with Vite integration
- **TypeScript**: Strict configuration extending Astro's defaults

**Key Components:**
- `src/pages/index.astro`: Main page layout with print-optimized styling
- `src/components/Sheet.svelte`: Core worksheet generator with problem generation logic
- `src/components/AnswerField.svelte`: Interactive input fields for missing values
- `src/components/FractionDisplay.svelte`: Renders fractions with proper mathematical formatting

**Problem Generation Logic:**
- Located in `Sheet.svelte` starting at line 145
- Supports three difficulty levels (easy/medium/hard) with different number ranges
- Handles three number types: integers, decimals, and fractions
- Implements proper fraction arithmetic with reduction
- Uses shuffle mechanism to randomize which operand is missing (answer field)
- Prevents duplicate problems using Set-based deduplication

**Print Optimization:**
- Uses Tailwind's `print:` variants for print-specific styling
- Hides controls and branding when printing
- Optimized grid layout for worksheet format

## File Structure Notes

**Styling:** Global styles in `src/styles/global.css`, component styles use Tailwind classes
**Assets:** SVG logos and backgrounds in `src/assets/`
**Configuration:** Astro config integrates Svelte and TailwindCSS via Vite plugin