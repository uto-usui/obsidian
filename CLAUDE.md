# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an **Obsidian vault** - a sophisticated personal knowledge management and creative writing system. The repository serves dual purposes:
- Technical documentation and memo writing about Obsidian usage and development practices
- Creative writing in Japanese following the "Ironic Void" literary style
- Project management using the P.A.R.A. methodology (Projects, Areas, Resources, Archive)

## Repository Structure

### Content Directories
- `/memo/obsidian/` - Technical documentation about Obsidian features, plugins, and methodologies following the Diataxis framework (tutorials, how-to guides, explanations, reference)
- `/tales/` - Creative writing pieces (short stories) written in Japanese using sophisticated literary techniques
- `/projects/` - Active project management with structured tracking, status updates, and detailed task breakdowns

### Configuration Directories
- `.obsidian/` - Obsidian application configuration and plugin settings (modify with caution)
- `.cursor/rules/` - AI assistant writing guidelines, including the "Ironic Void" creative writing style guide
- `.claude/` - Claude AI permissions and operational settings

## Technology Stack

**Primary Platform:** Obsidian knowledge management application with extensive plugin ecosystem

**Key Obsidian Plugins:**
- `obsidian-git` - Automatic git integration and version control
- `dataview` - Database-like queries for dynamic content organization
- `cursor-bridge` - Integration with Cursor editor
- `table-editor-obsidian` - Enhanced table editing capabilities
- `mermaid-tools` - Diagram and flowchart creation
- `obsidian-excalidraw-plugin` - Drawing and visual sketching

**Version Control:** Git with automated commits via obsidian-git plugin

## Content Standards and Conventions

### YAML Front Matter
All content uses structured YAML front matter with properties:
- `alias` - Alternative names/titles
- `tags` - Categorical organization
- `date` - Creation/modification timestamps
- Custom properties for specific content types

### Technical Documentation
Follows the **Diataxis framework**:
- **Tutorials** - Step-by-step learning guides
- **How-to guides** - Problem-solving focused instructions  
- **Explanation** - Conceptual understanding
- **Reference** - Technical specifications and details

### Creative Writing Standards
Creative content follows the **"Ironic Void" style** defined in `.cursor/rules/101_ironic_void.mdc`:
- Sophisticated literary quality emphasizing irony, psychology, and subtle humor
- Influenced by Japanese authors (成田祐輔, 村上春樹, 又吉直樹) without direct imitation
- Focus on capturing existential themes and everyday contradictions
- Avoids clichés, stereotypes, and formulaic storytelling

## Development Guidelines

### Content Creation
- Maintain existing metadata patterns and organizational structure
- Respect the multi-language nature (Japanese/English) of the content
- Follow established naming conventions for files and directories

### Configuration Changes
- Exercise caution when modifying `.obsidian/` configurations
- Respect existing plugin settings and workflows
- Test changes in development context before committing

### Version Control
- Git operations are primarily handled through the obsidian-git plugin
- Manual git operations should respect the existing .gitignore patterns
- Commit messages should be descriptive of content changes

## Important Notes

This is a content repository, not a traditional software project - there are no build processes, test suites, or deployment pipelines. The focus is on content quality, organization, and the seamless integration of technical documentation with creative writing within the Obsidian ecosystem.

When working with creative content, particularly in `/tales/`, maintain the high literary standards established by the "Ironic Void" style guide and avoid any modifications that would compromise the artistic integrity of the work.