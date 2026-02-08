# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

This is an **Obsidian vault** containing study notes for EE428 Computer Vision, Midterm 1. It is not a software project -- it is a collection of Markdown files organized as an interlinked knowledge base viewed in Obsidian.

## Repository Structure

- `00-Index/` - Master index with study checklist and links to all topics
- `01-08` - Topic folders (one per exam topic), each containing an `Overview.md` with concepts, formulas, MATLAB examples, and practice problems
- `09-Practice-Problems/` - Index linking to practice problem sets by topic (uses `[[wikilinks]]`)
- `10-MATLAB-Reference/` - Index linking to MATLAB function references by topic
- `11-Cheat-Sheets/` - Two condensed reference sheets: `Formula-Sheet.md` (all key formulas) and `Algorithm-Sheet.md` (step-by-step algorithms)
- `Progress.md` - Study schedule and exam strategy
- `Notes.md` - Scratch/quick notes

Topics covered in order: Image Formation, Image Filtering, Edge Detection, Hough Transform, Corner Detection, Image Pyramids, SIFT, RANSAC.

## Conventions

- **Internal links** use Obsidian `[[wikilink]]` syntax, not standard Markdown links
- **Math notation** uses inline code blocks with Unicode symbols (e.g., `Iₓ²`, `λ₁λ₂`, `ρ`, `θ`) rather than LaTeX
- **Formulas and matrices** are rendered in fenced code blocks for monospace alignment
- **Study checklists** use `- [ ]` / `- [x]` task syntax
- Files do not use emojis
- Each topic Overview follows a consistent pattern: Key Concepts sections, MATLAB Quick Reference, Study Checklist, Practice Problems, Related Topics

## Important Context

- Exam date: February 11, 2026
- Exam allows 3 sheets of handwritten/printed notes + calculator
- Content emphasis is on hand calculations (Sobel convolution, Hough transform ρ-θ, Harris corner response R, perspective projection)
- The cheat sheets in `11-Cheat-Sheets/` are designed to be printed as exam reference sheets
