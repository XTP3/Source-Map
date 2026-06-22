---

name: source-map-create
description: Creates, expands, or repairs Source Maps for a defined codebase scope. Resolves the Git repository root containing the mapped files and places the Source Maps directory there; if no Git repository exists, uses the root directory of the specific project, component, or codebase being mapped. Before using native code-search tools or broad searching commands, consults any existing Source Maps INDEX.md and relevant map files. Iterates over all relevant source code, creates or updates feature map files, and synchronizes INDEX.md.
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Source Map Create

## Overview

This skill guides the agent in creating, modifying, repairing, and maintaining:

* The root-level `Source Maps` directory.
* The source map index at `Source Maps/INDEX.md`.
* Individual feature or functionality map files such as `AUTH.md`, `WORKFLOWS.md`, or `API.md`.
* The global source map registry at `~/.agents/SOURCE_MAPS_REGISTRY.md`.

Source Maps must be treated as the primary codebase-navigation mechanism. Existing Source Maps must be consulted before the agent attempts to circumnavigate the codebase using native tools, filesystem searches, recursive directory listings, `grep`, `find`, `rg`, IDE search, semantic search, or similar discovery methods.

---

## Operating Workflow

### 1. Resolve the Mapping Scope

Determine exactly what project, repository, component, package, service, application, or codebase the agent has been directed to map.

Do not assume the current working directory is necessarily the correct mapping root.

The mapping scope may be:

* An entire repository.
* One repository within a multi-repository workspace.
* A standalone project without Git.
* A specific component or codebase contained within a larger non-Git directory.
* Multiple Git repositories, when the requested mapping scope spans more than one repository.

---

### 2. Resolve the Source Maps Root

Determine whether the files being mapped belong to an established Git repository.

Recommended checks include:

```bash
git -C "<target-path>" rev-parse --show-toplevel
```

or inspecting the target path and its parent directories for a `.git` directory or Git worktree configuration.

Apply the following root-resolution rules in order:

1. **Target belongs to an established Git repository**

   * Use that Git repository's top-level root directory.
   * Place `Source Maps` directly inside that repository root.
   * The required location is:

   ```text
   <git-repository-root>/Source Maps/
   ```

2. **Target spans multiple established Git repositories**

   * Maintain a separate `Source Maps` directory at the root of each affected Git repository.
   * Do not place one shared `Source Maps` directory above multiple independent repositories unless that parent directory is itself the root of a Git repository containing them.
   * Map each repository independently and register each repository's `INDEX.md`.

3. **No Git repository exists**

   * Use the root directory of the specific project, component, application, service, package, or other codebase the agent was directed to map.
   * Do not automatically use an unrelated parent workspace directory merely because it contains the target.
   * The required location is:

   ```text
   <mapped-scope-root>/Source Maps/
   ```

All Source Maps files must remain inside the resolved root so they persist with the codebase and, when Git exists, can be tracked by Git.

---

### 3. Consult Existing Source Maps Before Searching

Before using native code-navigation tools or broad search commands:

1. Check the global registry:

   ```text
   ~/.agents/SOURCE_MAPS_REGISTRY.md
   ```

2. Look for a registry entry matching the resolved repository or mapped scope.

3. Check for:

   ```text
   <resolved-root>/Source Maps/INDEX.md
   ```

4. If `INDEX.md` exists:

   * Read it first.
   * Identify and read all existing map files that may be relevant to the requested mapping scope.
   * Use their listed paths to navigate directly to known source files.

5. Only after the existing Source Maps have been exhausted may native tools or searching commands be used to discover information missing from the maps.

The required navigation order is:

```text
Global registry
→ Source Maps/INDEX.md
→ Relevant existing map files
→ Direct inspection of paths listed in those maps
→ Targeted native navigation or search
→ Broad codebase search only when necessary
```

Do not begin with recursive searches, repository-wide `grep`, `find`, `rg`, IDE search, semantic search, or exhaustive directory traversal when usable Source Maps already exist.

---

### 4. Initialize the Source Maps Directory

Check whether the following directory exists in the resolved root:

```text
Source Maps/
```

#### If the directory does not exist

Create:

```text
Source Maps/
Source Maps/INDEX.md
```

Initialize `INDEX.md` with an appropriate title and an empty or initial map index.

Example:

```markdown
# Source Maps Index

| Map | Scope |
| :--- | :--- |
```

#### If the directory exists

* Read `Source Maps/INDEX.md`.
* Verify that every indexed map file exists.
* Inspect relevant map files before scanning the codebase.
* Identify stale, missing, duplicated, or incorrectly indexed maps.
* Repair inconsistencies as part of the task when practical.

#### If `Source Maps` exists but `INDEX.md` does not

Create `INDEX.md`, inspect the existing map files, and populate the index from their actual contents.

---

### 5. Formulate the Map Action

Determine whether the task requires:

* Creating a new map file.
* Updating one existing map file.
* Updating multiple existing map files.
* Splitting an overly broad map into focused maps.
* Consolidating overlapping or duplicate maps.
* Removing obsolete entries.
* Repairing inaccurate file paths or descriptions.
* Creating maps for previously undocumented functionality.

Prefer feature-oriented or functionality-oriented maps rather than arbitrary directory mirrors.

Examples:

```text
AUTH.md
WORKFLOWS.md
API.md
DATABASE.md
EDITOR.md
DEPLOYMENT.md
SETTINGS.md
```

Avoid vague names such as:

```text
MISC.md
OTHER.md
FILES.md
CODE.md
```

unless the mapped scope genuinely requires them.

---

### 6. Discover Missing Information

After consulting all existing Source Maps, inspect the source code required to complete or correct the maps.

Use the narrowest effective discovery method.

Preferred order:

1. Open paths already identified by Source Maps.
2. Inspect imports, exports, references, route registrations, component composition, configuration, manifests, and adjacent files.
3. Use targeted filename or symbol searches.
4. Use narrowly scoped `grep`, `rg`, `find`, IDE search, semantic search, or equivalent tools.
5. Use broad repository-wide discovery only when narrower methods cannot establish the full implementation scope.

Do not search generated, vendored, cached, dependency, build-output, or binary directories unless they are explicitly part of the requested mapping scope.

Common directories to exclude unless directly relevant include:

```text
.git
node_modules
dist
build
target
coverage
.cache
.next
vendor
__pycache__
```

---

### 7. Map File Creation and Modification Guidelines

#### Naming Rule

Each individual map filename must:

* Use uppercase letters.
* Use underscores when multiple words are necessary.
* End in `.md`.
* Refer directly to the feature or functionality being mapped.

Examples:

```text
HOMEPAGE.md
USER_SETTINGS.md
WORKFLOW_EDITOR.md
API_AUTHENTICATION.md
```

Do not use lowercase, mixed-case, or ambiguous filenames.

---

#### Required Map Content

Each map file must:

* Clearly identify the feature, system, or functionality being mapped.
* List specific repository-relative file paths.
* Include a short, information-dense explanation of what each file does or contains.
* Describe relationships between files when that relationship is necessary to navigate or understand the implementation.
* Include supporting files such as types, schemas, hooks, routes, tests, configuration, state management, utilities, and styles when they materially participate in the mapped feature.

Prefer repository-relative paths so maps remain portable across machines and workspaces.

Example:

```markdown
# Workflow Editor

## Primary Implementation

- `src/features/workflows/WorkflowEditor.tsx` — Main editor surface; composes the canvas, toolbar, node palette, and inspector.
- `src/features/workflows/useWorkflowEditor.ts` — Owns editor state transitions, selection state, mutations, and persistence coordination.
- `src/features/workflows/workflowTypes.ts` — Defines workflow, node, edge, port, and editor-state types.

## Persistence

- `src/api/workflows.ts` — Client API functions for loading, creating, updating, and deleting workflows.
- `server/routes/workflows.ts` — HTTP route definitions and request validation for workflow operations.
- `server/services/workflowService.ts` — Business logic and database coordination for workflow persistence.

## Tests

- `src/features/workflows/WorkflowEditor.test.tsx` — Component behavior tests covering loading, selection, editing, and save operations.
```

High information density is more important than prose quality. Explanations should remain concise but must contain enough detail to distinguish each file's responsibility.

---

### 8. INDEX.md Requirements

The index must always be named exactly:

```text
INDEX.md
```

It must serve as the literal directory of all active map files in that `Source Maps` directory.

Each index entry must contain:

* The exact map filename.
* A short, information-dense explanation of what the map covers.

Recommended format:

```markdown
# Source Maps Index

| Map | Scope |
| :--- | :--- |
| `AUTH.md` | Authentication UI, session handling, authorization middleware, identity persistence, and related tests. |
| `WORKFLOWS.md` | Workflow listing, creation, editor behavior, execution, persistence, and supporting API routes. |
```

Requirements:

* Every active map file must appear in `INDEX.md`.
* Every `INDEX.md` entry must refer to an existing map file.
* Deleted, renamed, split, or consolidated maps must be reflected immediately.
* Do not index `INDEX.md` itself.
* Do not retain placeholder rows after real maps have been added.

---

### 9. Write Maps Before Updating the Index

Use this update order:

1. Create or update individual map files.
2. Verify the mapped paths and descriptions.
3. Update `INDEX.md` to reflect the final map set.
4. Verify that the index and map files agree.
5. Update the global registry.

This prevents `INDEX.md` from temporarily or permanently referencing files that were never created.

---

### 10. Register the Source Maps Index Globally

Ensure the resolved project or repository is tracked in:

```text
~/.agents/SOURCE_MAPS_REGISTRY.md
```

If the `.agents` directory does not exist, create it.

If the registry does not exist, initialize it with:

```markdown
# Source Maps Registry

| Project Name | Path to INDEX.md | Description / Scope |
| :--- | :--- | :--- |
```

Add or update the project's row.

Requirements:

* Use an absolute path to `INDEX.md`.
* Use a stable, recognizable project or repository name.
* Describe the codebase scope concisely.
* Update an existing row rather than creating duplicates.
* Remove or repair stale entries when their invalidity is confirmed.
* Register each repository separately when the mapping scope spans multiple independent repositories.

Example:

```markdown
| Vexil | `/home/coder/Vexil/Source Maps/INDEX.md` | Vexil application frontend, backend, workflow system, platform services, and development infrastructure. |
```

---

### 11. Final Verification

Before completing the task, verify:

* The correct Git repository root or non-Git mapped-scope root was used.
* `Source Maps` is located directly inside that root.
* Existing Source Maps were consulted before native or broad searching.
* Every relevant source file was considered.
* All mapped paths exist, unless explicitly marked as generated, planned, conditional, or external.
* No obsolete paths remain.
* Map filenames follow uppercase naming requirements.
* `INDEX.md` exactly represents the current map files.
* The global registry contains the correct absolute `INDEX.md` path.
* Multiple repositories were mapped independently when applicable.
