---
name: source-map-create
description: Determines if there is a Source Maps directory within the top level of the active Git repository root (or repository top level) to ensure persistence. If not, creates one along with an INDEX.md file to start. If so, reads the INDEX.md to determine what currently exists. Determines if a new map file will need to be created or one or more existing ones will need to be edited. Iterates over all relevant source code, and creates/modifies the source map file(s), and then updates the INDEX.md.
---

# Source Map Create

## Overview
This skill guides the agent in creating, modifying, and maintaining the root-level `Source Maps` directory, its index (`INDEX.md`), and the individual feature map files (`[FEATURE].md`).

## Operating Workflow

### 1. Check and Initialize Directory
- Identify if a Git repository is in use (e.g. by checking for a `.git` folder in current or parent directories, or running `git rev-parse --show-toplevel`).
- **If a Git repository is active**:
  - Locate its root directory. All `Source Maps` files and directories must reside within this repository root directory to ensure they are tracked and persisted by Git.
- **If no Git repository is active**:
  - Use the workspace root directory.
- Check if the `Source Maps` directory exists within the resolved root directory.
- **If the directory does not exist**:
  - Create the `Source Maps` directory inside the resolved root.
  - Create the `INDEX.md` file inside the `Source Maps` directory to initialize it.
- **If the directory does exist**:
  - Read `Source Maps/INDEX.md` within the resolved root to check what maps currently exist.

### 2. Formulate Map Action
- Identify if the task requires:
  - Creating a brand new map file.
  - Editing one or more existing map files.

### 3. File Creation and Modification Guidelines

#### Map Files
- **Naming Rule**: All capitals + the `.md` extension (e.g. `HOMEPAGE.md`, `DASHBOARD.md`). The filename must refer directly to the feature or functionality being mapped.
- **Content Format**:
  - Must list specific file paths.
  - Must include a short, information-dense sentence explaining what each individual file does and/or contains.
  - **Important**: Focus on high information density; proper grammar and sentence structure are secondary to clarity and completeness of the data.

#### INDEX.md File
- **Naming Rule**: Always named exactly `INDEX.md`.
- **Content Format**:
  - Serves as a literal index for all individual map files.
  - Contains the filename of each map file and a short, information-dense sentence explaining what that individual map file maps.
  - **Important**: Focus on high information density; proper grammar and sentence structure are secondary to clarity and completeness of the data.

### 4. Codebase Iteration
- Iterate over all relevant source code files that need mapping to gather their paths and details.
- Write/update the map file(s) and then update the `INDEX.md` to match.
