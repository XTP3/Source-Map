---

name: source-map
description: Requires the agent to use Source Maps as its first and primary method for circumnavigating a codebase before using native tools, shell search commands, IDE search, semantic search, or broad filesystem inspection. First checks whether the user has already referenced or specified one or more Source Maps INDEX.md locations; if so, opens those indexes directly and immediately begins consulting their relevant source maps without performing registry, Git-root, filesystem, or INDEX.md discovery. Otherwise, locates the Source Maps directory at the root of the established Git repository containing the target files or, when no Git repository exists, at the root of the specific project, component, or codebase being worked on. Reads the relevant map files, navigates directly to their listed paths, and maintains the maps when codebase structure changes.
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Source Map

## Overview

This skill governs how an agent uses and maintains Source Maps while working within an existing codebase.

Source Maps are the mandatory first navigation layer. The agent must consult them before attempting to circumnavigate the codebase with native tools, shell commands, recursive listings, `grep`, `find`, `rg`, IDE search, semantic search, repository-wide search, or similar discovery mechanisms.

A Source Maps `INDEX.md` path explicitly referenced or supplied by the user is authoritative. When the user has already identified one or more index locations, the agent must use those locations directly instead of attempting to rediscover the indexes through the global registry, Git inspection, directory traversal, or filesystem search.

The objective is to:

* Reach relevant implementation files directly.
* Avoid unnecessary repository-wide searches.
* Respect Source Maps locations already supplied by the user.
* Reduce repeated codebase discovery across agent instances.
* Preserve durable architectural knowledge with the repository.
* Keep maps accurate as implementation files change.
* Maintain a global registry of known source map indexes.

---

## Mandatory Navigation Priority

For every codebase task, use the following navigation order:

```text
1. INDEX.md path or paths already referenced or specified by the user
2. Relevant map files linked from those user-specified indexes
3. Direct inspection of paths listed in those maps
4. Global Source Maps registry, only when no index was specified
5. Project Source Maps/INDEX.md discovery, only when no index was specified
6. Relevant feature map files
7. Direct inspection of paths listed in those maps
8. Targeted native navigation or search for missing information
9. Broad search only as a last resort
```

When the user has supplied one or more `INDEX.md` locations, the effective workflow is:

```text
User-specified INDEX.md
→ Relevant linked source maps
→ Direct inspection of mapped paths
→ Targeted native navigation or search only for missing information
→ Broad search only when necessary
```

The agent must not perform the following before consulting a user-specified index:

* Search the global Source Maps registry.
* Run `git rev-parse` to locate the repository root.
* Search parent directories for `.git`.
* Search the filesystem for `INDEX.md`.
* Search for directories named `Source Maps`.
* Infer a different Source Maps location.
* Run broad codebase discovery commands.

The agent must not begin by running broad or exploratory commands such as:

```bash
find .
rg "<term>" .
grep -R "<term>" .
ls -R
tree
```

or equivalent IDE, language-server, semantic-search, indexing, or repository-search operations when usable Source Maps exist.

Native tools and searching commands are fallback mechanisms used only after the Source Maps have been read and found insufficient, incomplete, stale, or missing for the specific task.

---

## Operating Workflow

### 1. Determine the Target Scope

Identify the project, repository, component, service, package, application, or codebase involved in the task.

Review the user's current instruction and relevant preceding instructions for:

* An explicit path to an `INDEX.md` file.
* A path to a `Source Maps` directory containing `INDEX.md`.
* Multiple `INDEX.md` paths.
* A statement directing the agent to use particular source maps.
* A repository or project path accompanied by a known index location.
* An already established Source Maps path from the current working context.

Do not assume that the current working directory is the actual project root.

---

### 2. Check for User-Specified INDEX.md Locations

Before checking the global registry, resolving a Git repository, searching directories, or attempting any INDEX discovery, determine whether the user has already referenced or specified one or more `INDEX.md` locations.

A user-specified location may be:

* An absolute path.

  ```text
  /home/coder/Vexil/Source Maps/INDEX.md
  ```

* A path relative to an explicitly identified project or workspace.

  ```text
  Vexil/Source Maps/INDEX.md
  ```

* A `Source Maps` directory path whose index is unambiguous.

  ```text
  /home/coder/Vexil/Source Maps/
  ```

* Multiple index paths for a task spanning several projects or repositories.

* A path established in an earlier instruction that remains applicable to the current task.

#### When one or more locations have been specified

1. Resolve relative paths against the project, repository, or workspace context supplied by the user.
2. Open each specified `INDEX.md` directly.
3. Read the index entries.
4. Select the map files relevant to the task.
5. Open those map files immediately.
6. Use their listed source paths to navigate directly to the implementation.

Do not execute the normal registry, Git-root, Source Maps-directory, or `INDEX.md` search protocols first.

Do not search for a potentially newer, closer, or alternative index merely because another location might exist. The user's specified location is authoritative unless:

* The path does not exist.
* The path cannot be accessed.
* The file is not a Source Maps index.
* The path clearly belongs to a different project than the task.
* The user explicitly instructs the agent to locate an alternative.
* The index itself explicitly redirects to another canonical index.

#### When multiple locations have been specified

* Read each applicable index.
* Determine which index or indexes cover each part of the task.
* Consult the relevant maps from all applicable indexes.
* Keep repository-specific paths and map updates separated.
* Do not collapse multiple independent Source Maps structures into one.

#### When a specified path is invalid

Attempt only the minimal path resolution needed to account for:

* A relative versus absolute path.
* A directory path that implies `<directory>/INDEX.md`.
* Minor path-context ambiguity caused by the established workspace location.

If the index still cannot be opened:

1. State internally that the supplied path is unavailable or invalid.
2. Continue with the normal global registry and root-resolution workflow below.
3. Do not abandon Source Maps-first navigation merely because the supplied path failed.

---

### 3. Consult the Global Source Maps Registry Only When Necessary

Perform this step only when the user has not supplied a usable `INDEX.md` location.

Resolve the user's home directory dynamically and check:

```text
~/.agents/SOURCE_MAPS_REGISTRY.md
```

Do not hardcode a username or home path.

Read the registry table and determine whether the active project or repository is already listed.

Expected structure:

```markdown
# Source Maps Registry

| Project Name | Path to INDEX.md | Description / Scope |
| :--- | :--- | :--- |
| Example Project | `/absolute/path/to/project/Source Maps/INDEX.md` | Example project source maps. |
```

If a matching and valid entry exists:

1. Open the exact `Path to INDEX.md` listed.
2. Continue directly to the index-reading workflow.
3. Do not independently search the repository for another Source Maps directory unless the registered path is invalid, stale, or clearly belongs to a different project.

If multiple entries could match, use the target scope and repository path to choose the correct one.

---

### 4. Resolve the Source Maps Root When No Valid Index Is Known

Perform this step only when:

* The user did not supply a usable index location.
* The global registry did not contain a valid matching entry.

Determine whether the target files belong to an established Git repository.

Recommended command:

```bash
git -C "<target-path>" rev-parse --show-toplevel
```

Apply these rules in order:

#### A. Target belongs to an established Git repository

Use the Git repository's top-level root.

The Source Maps directory must be located at:

```text
<git-repository-root>/Source Maps/
```

The index must be located at:

```text
<git-repository-root>/Source Maps/INDEX.md
```

Do not place or seek Source Maps in:

* The agent's current subdirectory.
* A package subdirectory merely because work is occurring there.
* A parent workspace above the repository.
* A temporary directory.
* The global `.agents` directory.
* A sibling repository.

#### B. Target spans multiple Git repositories

Treat each repository independently.

Each repository must use:

```text
<repository-root>/Source Maps/INDEX.md
```

Do not use one shared Source Maps directory above multiple independent repositories unless that parent directory is itself their containing Git repository.

#### C. No Git repository exists

Use the root directory of the specific project, component, application, service, package, or codebase involved in the task.

The Source Maps directory must be located at:

```text
<mapped-scope-root>/Source Maps/
```

Do not automatically select an unrelated or overly broad workspace root.

---

### 5. Read INDEX.md Before Searching the Codebase

Open the resolved or explicitly supplied `INDEX.md`.

Read the entire index before using native tools or searching commands to locate implementation files.

The index describes each available map and the feature or functionality it covers.

Example:

```markdown
| Map | Scope |
| :--- | :--- |
| `AUTH.md` | Authentication UI, sessions, authorization middleware, and identity persistence. |
| `WORKFLOWS.md` | Workflow management, editor behavior, execution, persistence, and API integration. |
```

Use the descriptions to determine which map or maps are relevant to the task.

Do not assume only one map is relevant. Cross-cutting work may require several maps, such as:

```text
AUTH.md
API.md
DATABASE.md
SETTINGS.md
TESTING.md
```

Paths to map files are normally resolved relative to the directory containing the `INDEX.md` unless the index explicitly supplies another path.

---

### 6. Read Relevant Map Files

Open the map files most likely to cover the requested feature or functionality.

Map files are normally located in the same `Source Maps` directory as the index.

Map filenames must normally use uppercase letters, underscores where necessary, and the `.md` extension.

Examples:

```text
HOMEPAGE.md
AUTH.md
WORKFLOW_EDITOR.md
API_AUTHENTICATION.md
```

Review:

* Exact repository-relative file paths.
* The responsibility assigned to each file.
* Relationships between components, services, routes, schemas, state, configuration, tests, and utilities.
* Any notes concerning generated files, conditional implementations, external dependencies, or known limitations.

When the index links directly to a map, follow the link or listed path rather than searching for the map by filename.

---

### 7. Navigate Directly to Mapped Code

Use the paths listed in the relevant map files to open the exact implementation files.

Do not search for files that the map has already identified.

Resolve repository-relative paths against the repository or mapped-scope root associated with that index.

From the mapped files, follow direct structural references such as:

* Imports and exports.
* Function or type references.
* Route registrations.
* Component composition.
* Dependency injection.
* Configuration references.
* Database model usage.
* Test imports.
* Build or runtime entry points.

This direct-reference navigation remains preferable to broad search.

---

### 8. Use Native Tools Only for Missing Information

Native navigation or search tools may be used when:

* No Source Maps index exists.
* A supplied index path is invalid or inaccessible.
* No map covers the requested feature.
* A relevant map is incomplete.
* A listed path no longer exists.
* The code has changed without corresponding map maintenance.
* The task requires tracing a relationship not represented in the maps.
* The maps identify an initial file but not all downstream dependencies required for the task.

Use the narrowest search possible.

Preferred fallback order:

1. Inspect adjacent files and direct imports.
2. Search for an exact filename, symbol, route, type, component, or configuration key.
3. Search only the relevant source directory.
4. Expand to the relevant package or repository section.
5. Search the complete repository only when necessary.

Avoid generated, cached, vendored, dependency, or build-output directories unless directly relevant.

Common exclusions include:

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

The existence of incomplete maps is not permission to ignore them. Use the maps for initial orientation, then supplement them.

---

### 9. Handle Missing Source Maps

If no usable `Source Maps/INDEX.md` can be found after:

* Checking user-specified locations.
* Checking the global registry.
* Resolving the appropriate Git or non-Git project root.

do not invent map contents.

Determine whether the task permits initializing Source Maps.

When initialization or mapping is within scope, follow the `source-map-create` skill.

When initialization is outside the requested task:

* Continue with targeted native navigation.
* Avoid unnecessary broad searches.
* Do not claim that Source Maps were consulted when none existed.
* Report the absence only when materially relevant to the result.

---

### 10. Maintain Source Maps After Code Changes

After making codebase changes, determine whether any map has become inaccurate.

Update the corresponding map when files are:

* Added.
* Removed.
* Renamed.
* Moved.
* Split.
* Consolidated.
* Repurposed.
* Given substantially different responsibilities.
* Added to or removed from a mapped feature's implementation path.

Also update a map when:

* A new route, service, component, schema, hook, utility, test, or configuration file becomes materially involved in the feature.
* A previously documented implementation relationship is no longer valid.
* The agent discovers a relevant existing file omitted from the map.
* An explanation is materially inaccurate or too vague to support direct navigation.

Do not rewrite maps merely because code inside a correctly mapped file changed. Update them when the file's role, path, relationships, or relevance changed.

When the user supplied the index location, update that Source Maps structure rather than creating or modifying a separately discovered one.

---

### 11. Maintain INDEX.md

Update the applicable `INDEX.md` when:

* A new map is created.
* A map is renamed.
* A map is deleted.
* Maps are split.
* Maps are consolidated.
* A map's described scope materially changes.

Requirements:

* Every active map file must have one index entry.
* Every index entry must refer to an existing map file.
* Map names must match exactly.
* Descriptions must remain concise and information-dense.
* Do not include stale, duplicate, or placeholder entries.
* Do not index `INDEX.md` itself.

For tasks using multiple indexes, update only the indexes affected by the code changes.

---

### 12. Register or Update the Project Globally

Whenever the agent successfully locates, initializes, or interacts with a valid Source Maps index, ensure it is registered in:

```text
~/.agents/SOURCE_MAPS_REGISTRY.md
```

This registration occurs after the supplied or resolved index has been consulted. It must not delay immediate use of an index path already provided by the user.

If the `.agents` directory does not exist, create it.

If the registry file does not exist, initialize it with:

```markdown
# Source Maps Registry

| Project Name | Path to INDEX.md | Description / Scope |
| :--- | :--- | :--- |
```

Add or update the project row.

Requirements:

* Use the absolute path to `INDEX.md`.
* Use a stable project or repository name.
* Include a concise scope description.
* Update an existing row instead of creating duplicates.
* Correct stale paths when the correct location is known.
* Register each independent Git repository separately.
* Do not treat the global registry as a substitute for the repository-local `INDEX.md`.
* Do not override a valid user-specified index path with a conflicting registry entry.

Example:

```markdown
| Vexil | `/home/coder/Vexil/Source Maps/INDEX.md` | Vexil frontend, backend, workflow platform, supporting services, and repository infrastructure. |
```

---

## Source Maps Location Rules

A location explicitly supplied by the user takes precedence for the current task.

When no location is supplied, the normal placement order is:

```text
Established Git repository root containing the mapped files
→ otherwise, root of the specific non-Git project or codebase being mapped
```

Examples:

### User-specified index

```text
User instruction:
Use `/home/coder/Vexil/Source Maps/INDEX.md`.

Required behavior:
Open that file directly
→ read relevant maps
→ navigate to mapped code
→ skip registry and repository-root discovery
```

### Git repository

```text
/home/coder/Vexil/
├── .git/
├── Source Maps/
│   ├── INDEX.md
│   ├── AUTH.md
│   └── WORKFLOWS.md
├── src/
└── package.json
```

### Non-Git project

```text
/home/coder/prototype/
├── Source Maps/
│   ├── INDEX.md
│   └── APPLICATION.md
├── src/
└── package.json
```

### Workspace containing independent repositories

```text
/home/coder/workspace/
├── frontend/
│   ├── .git/
│   ├── Source Maps/
│   │   └── INDEX.md
│   └── src/
└── backend/
    ├── .git/
    ├── Source Maps/
    │   └── INDEX.md
    └── src/
```

Do not create:

```text
/home/coder/workspace/Source Maps/
```

for the independent `frontend` and `backend` repositories unless `/home/coder/workspace/` is itself their containing Git repository and the requested maps intentionally cover that repository as a whole.

---

## Completion Requirements

Before completing a codebase task governed by this skill, verify:

* The current and relevant preceding user instructions were checked for supplied `INDEX.md` locations.
* Any user-specified index was opened directly before registry or repository discovery.
* Registry and INDEX search protocols were skipped when a usable index path had already been supplied.
* Every applicable user-specified index was consulted when multiple indexes were provided.
* The correct project or repository was identified.
* The global registry was checked only when no usable index location was supplied.
* The applicable `INDEX.md` was read before broad code searching.
* Relevant map files were read.
* Mapped paths were used for direct navigation.
* Native tools were used only to supplement missing or stale map information.
* Structural code changes were reflected in the relevant maps.
* Each affected `INDEX.md` remains synchronized with its map files.
* The project has an accurate global registry entry.
* Source Maps are located at the user-specified location or at the appropriate Git repository root or non-Git mapped-scope root.
