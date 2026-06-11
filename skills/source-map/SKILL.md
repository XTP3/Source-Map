---
name: source-map
description: Informs/instructs the agent to use the top level directory “Source Maps” within the active Git repository root (or repository top level) to ensure persistence, and to begin this process by reading the INDEX.md file to determine which source map files are most likely to contain the information relevant to the agent.
---

# Source Map

## Overview
This skill guides the agent in using the `Source Maps` directory located at the root of the repository to quickly locate files and understand codebase features without relying on slow or extensive search queries (e.g., grep, find). It also outlines the procedures for maintaining the accuracy of these source maps and tracking all known project indexes.

## Operating Workflow

1. **Consult Master Index of Indexes and Locate INDEX.md**
   - Review the "Index of Indexes" section located at the bottom of this skill file first.
   - Determine if the scope of the current task falls under any of the projects listed in the index. If so, open and read the specific `INDEX.md` file path listed for that project.
   - If the relevant aspects are not covered by any entry in the master index, proceed to check if a Git repository is in use locally (e.g., by looking for a `.git` folder in current or parent directories, or running `git rev-parse --show-toplevel`).
   - If a Git repository is active, locate its root directory. The `Source Maps` directory must reside within this repository root directory to ensure it is persisted and tracked by Git.
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

6. **Maintain the Master Index of Indexes**
   - Whenever you interact with or initialize a project containing a source map index, ensure it is tracked in this skill file. 
   - Manually update this `SKILL.md` file directly to specify the file path of any new or missing source map `INDEX.md` files and specify what project each is for in the designated section below.

---

## Index of Indexes

*This section tracks the file paths of all known source map `INDEX.md` files across various projects. Update this table directly when new project source maps are identified.*

| Project Name | Path to INDEX.md | Description / Scope |
| :--- | :--- | :--- |
| [Placeholder Project] | `/path/to/repo/Source Maps/INDEX.md` | [Placeholder description] |
