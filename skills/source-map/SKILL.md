---
name: source-map
description: Informs/instructs the agent to use the top level directory “Source Maps” within the active Git repository root (or repository top level) to ensure persistence, and to begin this process by reading the INDEX.md file to determine which source map files are most likely to contain the information relevant to the agent.
---

# Source Map

## Overview
This skill guides the agent in using the `Source Maps` directory located at the root of the repository to quickly locate files and understand codebase features without relying on slow or extensive search queries (e.g., grep, find). It also outlines the procedures for maintaining the accuracy of these source maps and tracking all known project indexes using a centralized, cross-platform markdown registry file.

## Operating Workflow

1. **Consult the Global Source Maps Registry and Locate INDEX.md**
   - Check the global `SOURCE_MAPS_REGISTRY.md` file first. It is located at `~/.agents/SOURCE_MAPS_REGISTRY.md` (resolved dynamically using the user's home folder).
   - Review the registry table to see if the active project is listed. If a matching entry is found, open and read the specific `Path to INDEX.md` file path listed.
   - If the project is not yet listed in the global registry, determine if a Git repository is active (e.g., by looking for a `.git` folder in parent directories, or running `git rev-parse --show-toplevel`).
   - If a Git repository is active, locate its root. The `Source Maps` directory must reside within this root directory to ensure it is persisted and tracked by Git.
   - If no Git repository is active, use the workspace root directory.
   - Open and read the `INDEX.md` file within the `Source Maps` directory in the resolved root. Do not perform any wide codebase searches initially.
   
2. **Identify Relevant Map Files**
   - Read the index entries. Each entry describes a specific map file and its target feature or functionality.
   - Find the map file most likely to contain information relevant to your task (e.g., `HOMEPAGE.md`, `AUTH.md`). Map files are always named in ALL CAPS with the `.md` extension.

3. **Read the Map File**
   - Open the chosen map file in the `Source Maps` directory.
   - Review the listed file paths and their short, information-dense explanations to identify which exact files implement or contain the logic for the feature you are working on.

4. **Navigate to Code**
   - Use the resolved file paths to directly view or edit the relevant source code.

5. **Maintain and Update Source Maps**
   - If changes are made to the codebase that add, remove, rename, or significantly alter files, update the corresponding map file to reflect these changes.
   - If a new map file is created or an existing one is deleted/consolidated, update the `INDEX.md` file within the `Source Maps` directory to keep the source map registry up to date.

6. **Register and Update the Global Source Maps Registry**
   - Whenever you interact with or initialize a project containing a source map index, ensure it is tracked in the global `SOURCE_MAPS_REGISTRY.md` file.
   - If the registry file does not exist, initialize it in the user's home `.agents` directory as a Markdown file with the registry table structure below.
   - Add or update the project's row in the registry table to match the specification.

---

## Global Registry Configuration

To prevent duplicate indexes and maintain consistency across multiple agent instances and workspaces, all active source map paths are tracked globally:

**Registry Path:** `~/.agents/SOURCE_MAPS_REGISTRY.md`

### Registry Structure and Example Content

The file must contain a markdown table tracking the paths to all active indexes:

| Project Name | Path to INDEX.md | Description / Scope |
| :--- | :--- | :--- |
| [Placeholder Project] | `/path/to/repo/Source Maps/INDEX.md` | [Placeholder description] |
