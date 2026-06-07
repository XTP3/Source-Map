---
name: source-map
description: Informs/instructs the agent to use the top level directory “Source Maps” within the active Git repository root (or repository top level) to ensure persistence, and to begin this process by reading the INDEX.md file to determine which source map files are most likely to contain the information relevant to the agent.
---

# Source Map

## Overview
This skill guides the agent in using the `Source Maps` directory located at the root of the repository to quickly locate files and understand codebase features without relying on slow or extensive search queries (e.g. grep, find).

## Operating Workflow

1. **Identify Git Repository Root and Locate INDEX.md**
   - Check if a Git repository is in use (e.g. by looking for a `.git` folder in current or parent directories, or running `git rev-parse --show-toplevel`).
   - If a Git repository is active, locate its root directory. The `Source Maps` directory must reside within this repository root directory to ensure it is persisted and tracked by Git.
   - If no Git repository is active, use the workspace root directory.
   - Open and read the `INDEX.md` file within the `Source Maps` directory in the resolved root. Do not perform any wide codebase searches initially.
   
2. **Identify Relevant Map Files**
   - Read the index entries. Each entry describes a specific map file and its target feature or functionality.
   - Find the map file most likely to contain information relevant to your task (e.g. `HOMEPAGE.md`, `AUTH.md`). Map files are always named in ALL CAPS with the `.md` extension.

3. **Read the Map File**
   - Open the chosen map file in the `Source Maps` directory.
   - Review the listed file paths and their short, information-dense explanations to identify which exact files implement or contain the logic for the feature you are working on.

4. **Navigate to Code**
   - Use the resolved file paths to directly view or edit the relevant source code.
