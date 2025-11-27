# AI Instructions & Behavior Rules – Java Ecosystem

> **Purpose**: This file defines **mandatory rules** for AI assistants (GitHub Copilot Chat, Cursor, etc.) when setting up or modifying a **Java backend ecosystem** using this setup package.  
These rules prevent destructive changes, hallucinations, and incorrect assumptions.

---

> ⚠️ **AI FILE BEHAVIOR OVERRIDE (DO NOT CREATE DUPLICATE FILES)**  
> - This file (`AI_INSTRUCTIONS.md`) is the **primary, runtime guide**, not a template.  
> - AI assistants **MUST NOT** create new variants like `AI_INSTRUCTIONS_java.md`, `AI_INSTRUCTIONS_generated.md`, or similar.  
> - AI MUST use and update the following existing files **as the single source of truth** (never create renamed copies of them):  
>   - `JAVA_ECOSYSTEM.md`  
>   - `CONFIGURATION.md`  
>   - `AI_INSTRUCTIONS.md`  
>   - `SETUP_STEPS.md`  
> - If AI is about to create any new `*_java.md` or `*_generated.md` files, it MUST **stop** and ask the user for confirmation instead.

---

## 📖 About This File

This file defines:

- Critical AI behavior rules
- How AI must read and interpret configuration files
- New vs Existing project handling
- Execution safety requirements
- What AI must NOT do
- What AI MUST do
- Decision tree for determining setup flow
- Verification and error-handling guidelines

**⚠ AI MUST read this file BEFORE executing any setup steps.**

---

## 💻 IDE Detection & Handling Rules (MANDATORY)

AI MUST determine the IDE using the **`ide_active`** flag from `CONFIGURATION.md` as the *primary source of truth*.

### 🧠 IDE Detection Order (MUST FOLLOW)
1. **If `ide_active` is set in `CONFIGURATION.md` → use it immediately.**
2. If user explicitly states an IDE in the chat → override everything and use that.
3. If no flag and no user input:
   - If `.idea/` exists → IDE = IntelliJ
   - If `.vscode/` exists → IDE = VS Code
4. If both `.idea/` and `.vscode/` exist → support **both**.
5. If still unclear → AI MUST ask:
   > “Which IDE do you use — IntelliJ or VS Code?”

**Important:**  
`ide_active` flag **overrides** folder-based detection and must be respected ALWAYS.

---

## 🏗 IDE Configuration Rules

### If IDE = **IntelliJ** (or `ide_active: intellij`)
- ❌ Do NOT create, modify, or delete IntelliJ-specific files:
  - `.idea/**`, `*.iml`, `.ipr`, `.iws`
- ❌ Do NOT generate IntelliJ code-style or workspace configs.
- ✔ Provide **instructions only**, not files.
- ✔ Ensure formatting, linting, and quality checks are enforced through:
  - `.git/hooks/pre-commit`
  - `scripts/*.sh` or `scripts/*.bat`
- ✔ Recommend IntelliJ plugins rather than generating configuration.

---

### If IDE = **VS Code** (or `ide_active: vscode`)
- ✔ Generate `.vscode/settings.json` and `.vscode/extensions.json`
- ✔ Apply configuration from `CONFIGURATION.md` (extensions, Java options, formatting rules)
- ✔ Validate JSON before saving
- ✔ Never modify unrelated or user-created VS Code files

---

### If BOTH IDEs detected
- AI MUST support **both environments** without overwriting either.
- Do NOT delete, modify, or replace:
  - `.idea/`
  - `.vscode/`
- VS Code configs must be generated safely; IntelliJ must remain untouched.

---

## 🧪 IDE Validation Requirements

Before completing IDE setup, AI MUST validate:

- Git hooks run successfully (`.git/hooks/pre-commit`)
- Checkstyle, Spotless, and Snyk commands run without errors
- VS Code JSON files pass JSON validation (if generated)
- IntelliJ users receive **manual setup instructions**, not generated config files

---

## 🚫 IDE Safety Rules

- ❌ Do NOT auto-generate IntelliJ project files under any circumstances
- ❌ Do NOT delete `.vscode` even if IntelliJ is the active IDE
- ❌ Do NOT assume IDE support without following detection rules
- ❌ Do NOT rewrite or modify user-created IDE files (IntelliJ OR VS Code)

---

## 🤖 Critical AI Behavior Rules

**MANDATORY INSTRUCTIONS FOR AI ASSISTANTS**: Follow these rules strictly. Violation of these rules can cause project setup failures.

### 🤖 Core Principles (MANDATORY)

1. **NO HALLUCINATIONS**
   - Do **NOT** invent:
     - Maven/Gradle plugins or coordinates that are not standard or not requested
     - File paths that do not exist
     - Non‑existent VS Code settings or extensions
   - Use only:
     - The configuration / instructions in these Java setup files, and
     - Actual content detected from the repository (pom.xml, build.gradle, etc.)
   - If something is unclear or missing, **ask the user** instead of guessing.

2. **STAY ON TRACK**
   - Follow the steps in **`SETUP_STEPS.md`** in the order given.
   - Do not introduce additional tools or steps unless:
     - They are explicitly mentioned in config, or
     - The user explicitly asks for them.
   - Use configuration from:
	 - `.java-ecosystem.config.yaml` (highest priority)
	 - `CONFIGURATION.md` (secondary)
	 - Default instructions from `SETUP_STEPS.md`

3. **NO “CLEVER” REWRITES**
   - Do **NOT**:
     - Rewrite entire `pom.xml` or `build.gradle` from scratch.
     - Remove existing plugins or dependencies unless the user explicitly approves.
     - Change the project’s Java version silently.
   - Do:
     - Merge, append, and enhance configurations.
     - Preserve existing behavior unless instructed to refactor.

4. **STEP‑BY‑STEP EXECUTION**
   - Complete one step fully before moving to the next.
   - After each step:
     - Verify commands ran successfully.
     - Verify files exist and contain expected content.
   - If `setup_mode: "step_by_step"`:
     - Show a summary of the step.
     - Ask the user before applying changes.

5. **ERROR HANDLING**
   - If any command fails or configuration is invalid:
     - ❌ **STOP** immediately.
     - Report the exact error and which step failed.
     - Propose a fix, but wait for user confirmation before applying.

6. **VERIFICATION REQUIREMENTS**
   - Treat each step as incomplete until:
     - Build or check commands succeed (e.g., `mvn verify`, `./gradlew check`).
     - New configuration files are syntactically valid (XML/YAML/JSON).
	 - Validate Git hooks exist and executable
	 - **For VS Code**
		- Validate JSON syntax and ensure only Java-related settings
		- Validate recommended extension list
	 - **For IntelliJ**
		- Do **NOT** create/modify `.idea/**`
		- Formatting, Checkstyle, Spotless, Snyk must run via command line

### 📋 EXECUTION CHECKLIST (MANDATORY)

Before moving to next step:

- [ ] Step executed successfully  
- [ ] Files created/updated as expected  
- [ ] Build tool validation succeeded  
- [ ] No syntax errors in XML/YAML/JSON updates  
- [ ] IDE-specific checks:
	- [ ] VS Code configs validated if VS Code detected
	- [ ] IntelliJ settings not modified automatically
- [ ] Git hooks created and executable (if enabled)  
- [ ] User notified (if step-by-step mode)

### 🚫 What NOT to Do (Critical) — Expanded

- ❌ **Do NOT proceed** if the previous step produced errors or warnings. Stop and report.
- ❌ **Do NOT assume** steps succeeded without validating Maven/Gradle and script exit codes.
- ❌ **Do NOT create files, folders, or configurations** that are not defined in `SETUP_STEPS.md`.
- ❌ **Do NOT overwrite** these critical files under any circumstances unless user explicitly approves:
  - `pom.xml`, `build.gradle`
  - `.vscode/settings.json` (unless IDE = VS Code and user approved a merge)
  - Any existing Checkstyle, Spotless, or dependency-check configs
  - `.idea/**`, `*.iml`, `workspace.xml`, or other IntelliJ workspace files
- ❌ **Do NOT auto-upgrade Java version** or change project language level for existing projects.
- ❌ **Do NOT modify or “improve” instructions** in `SETUP_STEPS.md` — follow them exactly.
- ❌ **Do NOT install or configure tools flagged as disabled** in the configuration.
- ❌ **Do NOT guess project structure** — detect it programmatically before acting.
- ❌ **Do NOT continue execution** after any of:
  - validation failure
  - config conflict
  - missing dependency or required binary
  - unresolved ambiguity (ask user)
- ❌ **Do NOT remove existing build plugins, CI jobs, or user-defined configurations.**
- ❌ **Do NOT download or run arbitrary binaries** from untrusted sources. If a required binary is missing (jar/cli), STOP and instruct the user to download and place it in the correct folder.
- ❌ **Do NOT change CI configuration** (GitHub Actions, Jenkins, etc.) without explicit approval.
- ❌ **Do NOT auto-install IDE plugins** (VS Code or IntelliJ); only recommend and document them.
- ❌ **Do NOT perform destructive filesystem operations** (mass deletes, chmod recursive) without explicit user approval.
- ❌ **Do NOT perform any background or asynchronous work** — all actions must be reported and completed within the current session.

---

### ✅ What TO Do (Mandatory AI Behavior)

#### Preparation & Detection
- ✅ **Determine IDE using the correct priority order:**
  1. If `ide_active` is set in `CONFIGURATION.md` → **use it immediately**.
  2. If the user explicitly states an IDE → override everything and use that.
  3. If `ide_active` is not set:
     - If `.idea/` exists → IntelliJ
     - If `.vscode/` exists → VS Code
     - If both exist → support BOTH environments
     - If unclear → ask:
       > “Which IDE do you use — IntelliJ or VS Code?”

- ✅ **Always check for `.java-ecosystem.config.yaml` first**  
  If present → it overrides all inline settings.  
  Then read `CONFIGURATION.md`, then `SETUP_STEPS.md`.

- ✅ **Validate OS / shell** to determine correct script types:
  - Linux/macOS → `.sh`
  - Windows → `.bat` (or `.sh` via Git Bash if configured)

#### Backups & Safe Edits
- ✅ **Backup any file** before modifying it. Save a `.bak` or copy with timestamp in a `backup/` folder.
- ✅ **When updating existing config files**:
  - Read file fully, compute precise diffs.
  - Merge intelligently (preserve user rules) — do not overwrite.
  - If merge rules ambiguous → STOP and ask user.
- ✅ **Validate syntax** after edits:
  - XML → XML parser
  - YAML → YAML parser
  - JSON → JSON parser
  - Shell scripts → basic lint or `bash -n` check

#### Tools & Scripts
- ✅ **Verify required tool files exist** before running:
  - `tools/checkstyle/checkstyle.jar`
  - `tools/spotless/google-java-format.jar`
  - `tools/snyk/snyk.exe` or `tools/snyk/snyk`
- ✅ **If a required binary is missing**: STOP and instruct user with exact download URL and destination path. Do not attempt to fetch it.
- ✅ **Ensure script files exist and are executable**:
  - `scripts/spotless-check.sh` / `.bat`
  - `scripts/checkstyle.sh` / `.bat`
  - `scripts/snyk-scan.sh` / `.bat`
  - If missing or empty → recreate using the exact approved content and mark executable (Unix `chmod +x`).
- ✅ **Run all commands one-by-one**; check exit code and output. If a command fails → STOP and report the error and suggested fix.
- ✅ **Prefer non-destructive verification commands** first (e.g., `mvn -q validate`, `./gradlew help`) before heavier actions.

#### IDE-specific behavior (Updated for `ide_active`)

- ✅ **If IDE = VS Code (from `ide_active` or fallback detection):**
  - Generate or merge:
    - `.vscode/settings.json`
    - `.vscode/extensions.json`
  - Apply configuration from `CONFIGURATION.md`
  - Validate JSON before writing
  - Do NOT modify IntelliJ files

- ✅ **If IDE = IntelliJ (from `ide_active` or fallback detection):**
  - Do NOT create or modify:
    - `.idea/**`, `*.iml`, `.ipr`, `.iws`
  - Provide **instructional guidance only** (no files generated)
  - Recommend plugins (Checkstyle-IDEA, Google Java Format)
  - Enforce formatting/checks through Git hooks and CLI tools

- ✅ **If BOTH IDEs detected (only via auto-detection):**
  - Support both environments safely
  - Do not delete or overwrite `.idea/` or `.vscode/`

#### Git Hooks & CI
- ✅ **Create `.git/hooks/pre-commit` only if enabled** in configuration and merge if exists (append safe checks).
- ✅ **Ensure hooks call platform-appropriate scripts** (`.bat` on Windows, `.sh` on Unix) with OS detection logic.
- ✅ **Validate hooks are executable** (Unix `chmod +x`) and, if necessary, provide Windows guidance.
- ✅ **Do not change CI workflows unless auto_setup_ci: true** and user approved.

#### Security & Safety
- ✅ **Never log or expose secrets**. If a file seems to contain secrets (keys/tokens), stop and alert the user.
- ✅ **Do not paste secrets into prompts** or include them in generated config files.
- ✅ **Validate Snyk / dependency-check behavior** only if the tool is enabled; do not enable Snyk automatically if flagged disabled.

#### Reporting & User Interaction
- ✅ **After each step**: produce a concise summary of:
  - What was changed (with file paths)
  - Commands executed and their results
  - Any warnings or manual steps needed
- ✅ **If step_by_step mode**: show proposed changes and ask user to confirm before applying.
- ✅ **If conflicts or unknowns**: report exact lines and the reason for conflict; provide choices and recommended resolution.
- ✅ **Always ask for explicit approval** before destructive or irreversible changes.

#### Final Validation
- ✅ Run and verify:
  - Spotless check/format
  - Checkstyle
  - Snyk scan (if enabled)

- ✅ Validate:
  - Git pre-commit hooks are running
  - VS Code files are valid JSON
  - IntelliJ instructions are correct

- ✅ Provide final summary of:
  - Applied changes  
  - Tool results  
  - Any follow-up actions needed  

---

## 📋 Configuration Reading Process

When the user adds `JAVA_ECOSYSTEM.md` to context, the AI MUST follow this order:

1. **First** – Read `CONFIGURATION.md`
Extract and understand:

- Extract `ide_active` (if present) and all active flags:
- `project_type`, `setup_mode`
- `java_version_default`, `build_tool_default`
- `enable_checkstyle`, `enable_spotless`, `enable_snyk`
- `auto_setup_hooks`, `auto_setup_ci`, `create_context_files`, `use_custom_configs`
- This file is the **single source of truth** for inline configuration unless YAML override exists.


2. **Second** – IDE AUTO-DETECTION (run first)
Determine the IDE to use for subsequent actions **in this exact order**:

1. **If the user explicitly states an IDE in the current chat/context → use that.**  
 (A direct user statement overrides all other sources.)

2. **Else if `ide_active` is set in `CONFIGURATION.md` → use that.**  
 (`ide_active` is authoritative when provided.)

3. **Else fall back to folder-based auto-detection:**
 - If `.idea/` exists → `detected_ide = "intellij"`
 - Else if `.vscode/` exists → `detected_ide = "vscode"`
 - If both folders exist → `detected_ide = "both"`
 - If still unclear → **ask the user**:  
   > "Which IDE are you using — IntelliJ or VS Code?"

**Important:** `ide_active` **overrides** folder heuristics but **not** an explicit user statement.

Use `detected_ide` to gate all subsequent IDE actions:
- If `detected_ide == "intellij"` → DO NOT create or modify `.vscode/*`
- If `detected_ide == "vscode"` → DO NOT create or modify `.idea/*`
- If `detected_ide == "both"` → safely support both (never delete either)

3. **Third** – Check for `.java-ecosystem.config.yaml`
If this file exists:

- Treat it as **highest-priority configuration**
- YAML overrides inline configuration
- Always merge (NEVER overwrite user-defined settings)
- YAML controls:
  - Java version  
  - Build tool preference  
  - Active tool configuration  
  - VS Code extension preferences  
  - Git hook rules  
  - CI settings (if enabled)

4. **Fourth** – Detect Project State
For `project_type = "existing"` or `auto-detect`, inspect repository contents:

- Build tool files:
- `pom.xml`, `build.gradle`, `build.gradle.kts`
- Java source layout:
- `src/main/java`, `src/test/java`
- Active tool configs:
- `config/checkstyle/*.xml`, Spotless sections, `.snyk`, etc.
- Workspace / IDE configs — **respect the resolved IDE** from step 2:
- If resolved IDE = `vscode` or `ide_active: vscode`:
  - Inspect `.vscode/settings.json`, `.vscode/extensions.json` (safe to read & merge)
- If resolved IDE = `intellij` or `ide_active: intellij`:
  - **Read** `.idea/*` only for detection (do NOT modify `.idea` files)
- If `both` → inspect both- but do not delete or overwrite either
- Git hooks:
- `.git/hooks/pre-commit`
- CI pipelines:
- `.github/workflows/*.yml`, other CI config

Use these findings to determine what already exists and what needs adding.

5. **Fifth** – Determine Build Tool
- If `pom.xml` exists → **Maven project**  
- If `build.gradle` or `build.gradle.kts` exists → **Gradle project**  
- If neither exists **and** `project_type = "new"`:
  - Use build tool preference defined in `.java-ecosystem.config.yaml`  
  - If no preference found → **default to Maven**

---

6. **Sixth** – Build an Execution Plan
AI must construct a step-by-step plan based on:

- `project_type` (`new` | `existing` | `auto-detect`)
- Build tool (Maven vs Gradle)
- Enabled active tools and automation flags
- Whether `.java-ecosystem.config.yaml` overrides inline config
- Resolved IDE (from Step 2) and its implications for file edits:
- If IDE = `vscode` → plan may include writing `.vscode/*` files (validate JSON)
- If IDE = `intellij` → plan must only include guidance for IntelliJ (no generated `.idea` files)
- If both → plan must support both without overwriting either
- Java version strategy (`java_version_default`, `respect_existing_java_version`)

Finally, present the plan (or apply it immediately) according to `setup_mode`:
- `auto` → apply non-destructive changes automatically
- `step_by_step` → show each step and ask for confirmation
- `manual` → generate snippets and instructions only

---

## 🆕 New vs Existing Projects – AI Strategy

This guide works for **both new and existing Java projects**, but the approach differs.

**AI Instructions:**  
AI must decide the correct workflow by reading:

- `project_type` (from CONFIGURATION.md)
- `ide_active` (from CONFIGURATION.md)

### 🔍 Decision Tree for AI

```
START
│
├─ Read project_type from CONFIGURATION.md
│
├─ If project_type == "new"
│ └─ NEW PROJECT PATH (skip detection, create all files)
│
├─ If project_type == "existing"
│ └─ EXISTING PROJECT PATH (detect, merge safely)
│
└─ If project_type == "auto-detect"
│
├─ Check: Does project already contain Java build/config files?
│ Look for:
│ - pom.xml
│ - build.gradle / build.gradle.kts
│ - src/main/java
│ - Existing Checkstyle config
│ - Existing Spotless config
│ - Existing .git/hooks
│ - Existing CI workflows (.github/workflows)
│
├─ NO Java configs found → NEW PROJECT PATH
│ └─ Follow: "For New Projects" steps below
│
└─ YES Java configs found → EXISTING PROJECT PATH
└─ Follow: "For Existing Projects" steps below
```

### New Projects

**When to use:**  
- `project_type: "new"`  
- OR auto-detect finds **no Java build files** (`pom.xml` / `build.gradle*`)

**AI Instructions:**

- Use `setup_mode: "auto"` (unless user overrides)
- Create **all project structure and configuration files** from scratch
- No need to inspect existing files
- Install and configure all **active tools**

#### IDE Behavior for New Projects

1. If `ide_active: vscode`
Generate:
- `.vscode/settings.json`
- `.vscode/extensions.json`
- Any VS Code defaults required for Java tooling

2. If `ide_active: intellij`
- **Do NOT** create `.idea/**` or `*.iml`
- Provide **IntelliJ setup guidance only**
- Enforce tooling through Git hooks and CLI tools, not IDE config files

3. If no `ide_active` set
Fallback to detection:
- `.idea/` → IntelliJ (instructions only)
- `.vscode/` → VS Code (generate settings)
- Both → support both
- None → ask user for IDE preference

#### Files Generated for New Projects
| File | Created When |
|------|--------------|
| `.vscode/settings.json` | Only if IDE = VS Code |
| `.vscode/extensions.json` | Only if IDE = VS Code |
| IntelliJ guidance (not config files) | If IDE = IntelliJ |
| `tools/*`, `scripts/*.sh/.bat` | Based on flags |
| `.git/hooks/pre-commit` | If `auto_setup_hooks: true` |
| Context files | If `create_context_files: true` |

**Execution Path:**  
Follow **all steps (1–10)** in sequence, generating **fresh files**, no merging required.


### Existing Projects

**When to use:**  
- `project_type: "existing"`  
- OR auto-detect finds existing Java project files  

**AI Instructions:**

	1. **FIRST**: — Detect Existing Files
	Look for:
		- `pom.xml`  
		- `build.gradle` / `build.gradle.kts`  
		- Existing `config/checkstyle/*`  
		- Existing Spotless sections inside pom/gradle  
		- Existing Snyk plugin  
		- `.git/hooks/pre-commit`  
		- Existing CI workflows `.github/workflows/*.yml`  
		- **IDE behavior based on CONFIGURATION.md**
		  - If `ide_active: intellij` → IntelliJ rules apply  
		  - If `ide_active: vscode` → VS Code rules apply  
		  - If not provided → fallback to folder detection  
			- `.idea/` → IntelliJ  
			- `.vscode/` → VS Code  
			- Both → support both  
			- None → ask user

	2. **For EACH Step**:
		- **If file/config exists:**  
		  → **READ it**, then **MERGE** updates (DO NOT overwrite)

		- **If file/config does NOT exist:**  
		  → **CREATE** it using recommended config

	3. **Respect Existing Patterns**
	AI must adapt to project’s established conventions:

		- Preserve existing Maven/Gradle structure  
		- Preserve custom formatting and Checkstyle rules  
		- Preserve existing Spotless preferences  
		- Respect existing folder structure  
		- **Follow IDE rules:**
		  - If IntelliJ → do NOT modify `.idea/**`  
		  - If VS Code → deep-merge `.vscode/*.json`  
		  - Do NOT delete or replace existing IDE settings
	4. **Use Selective Flags**
	AI must only:

		- Add missing sections  
		- Enable active tools  
		- Avoid disabled tools  
		- Avoid removing anything  

	5. **Backup Before Updating**
	If a config exists (example: `checkstyle.xml`, `.vscode/settings.json`, `.snyk`, `pom.xml`):
	- AI must create backup:
	  - `filename.backup-ai` 
	  - OR ask for user approval before large merges

	6. **Validate After Every Change**  
	Run:

		- `mvn -q validate` OR  
		- `./gradlew help`

Stop immediately on errors.


**Execution Path**: 
- Step 1: Detect existing files → Read → Merge → Create only missing ones
- Step 2: Check existing dependencies → Install only missing ones
- Step 3-10: Apply same pattern - check, merge if exists, create if missing

**Common Scenarios & Handling**:
- **Existing Checkstyle**: 
	- Read existing rules  
	- Merge gently  
	- Preserve custom checks  
	- Only add missing recommended rules if non-conflicting
- **Existing Spotless**:
	- Detect formatting engine  
	- Preserve custom formatting config  
	- Add only missing sections
- **Existing CI workflow**: 
	- Read YAML  
	- Add missing jobs only (e.g., formatting check)  
	- Preserve all custom logic 
- **Existing Git hooks**: 
	- Read hook  
	- Append required commands (Spotless, Checkstyle, Snyk)  
	- Do NOT replace entire hook
- **Existing Snyk Configuration**: 
	- Do not overwrite .snyk policy file  
	- Add missing recommended settings only (severity threshold, ignore rules, test/monitor commands)
	- Do not remove existing “ignored” vulnerabilities or custom policies
- **Existing VS Code Settings** (If IDE = VS Code): 
	- Deep merge JSON  
	- Preserve user values  
	- Only add missing Java-related config  
- **Existing IntelliJ Settings** (If IDE = IntelliJ): 
	- Only guide; do not modify files
	
**Merging Strategy for Existing Configs**:
- **Checkstyle**: 
	- Merge rule definitions  
	- Preserve custom severity levels  
	- Do NOT replace user-defined rules
- **Spotless**: 
	- Merge only formatting engine or missing fields  
	- Do NOT override custom formatting settings  
- **Git Hooks**: 
	- Append commands  
	```
	mvn spotless:check
	mvn checkstyle:check
	tools/snyk/snyk test
	```
	- Preserve existing hook logic
- **VS Code Settings** (If IDE = VS Code): Deep merge settings object (user settings override defaults)
- **IntelliJ Settings** (If IDE = IntelliJ): Never modify `.idea/**` files
- **Extensions**: Merge arrays, remove duplicates, preserve user-added extensions

**⚠️ Critical Rules for Existing Projects**:
- NEVER overwrite existing configs without reading them first
- ALWAYS detect IDE using `ide_active` first 
- ALWAYS preserve custom rules/settings unless explicitly asked to replace
- ALWAYS verify file exists before attempting to read/edit
- STOP and ask user if conflict between existing and recommended configs
- Report what will be changed before making changes
- ALWAYS validate changes using Maven or Gradle
- ONLY install **active tools**

---

## 🔄 Error Handling Protocol

**On ANY error:**
1. **STOP immediately** — do NOT continue or run next steps.
2. **Report the exact error message** with context.
3. **Wait for user instruction** before retrying or applying fixes.
4. **Retry only after user approval.**
5. **Record** which steps succeeded and which failed.

**Error Reporting Format**:

```text
❌ Error in Step [N]: [Step Name]
Error Message: [exact error]
Context: [what was being done]
Files Affected: [list or "none"]
Suggested Fix: [if applicable]
```

---

## ✅ Execution Checklist per Step

Before marking a step as **completed**, ensure:

- [ ] All intended files are created/updated.
- [ ] Build tool configuration is syntactically valid.
- [ ] `mvn -q validate` or `./gradlew help` succeeds (no build config errors).
- [ ] Quality commands run successfully if configured:
  - Maven: `mvn verify` (or at least `mvn test`)
  - Gradle: `./gradlew check test`
- [ ] If IDE = VS Code:
	- [ ] `.vscode/settings.json` exists only if VS Code recognized.
	- [ ] `.vscode/extensions.json` created/merged only if VS Code is active
	- [ ] No `.idea` files created or edited
- [ ] If IDE = IntelliJ:
	- [ ] No files inside `.idea/` or `*.iml` were autogenerated or modified by AI

---

## 🔐 Security & Secrets

- Do **NOT**:
  - Check in passwords, API keys, or connection strings.
  - Suggest adding secrets into `application.properties` or `application.yml` in plain text for real environments.
- Do:
  - Recommend use of:
    - Environment variables
    - Secret stores (Key Vault / Secrets Manager, etc.)
    - Local `.env` / `.properties` with `.gitignore` protection for dev only
  - Integrate security scanning tools only if enabled in config, e.g.:
    - Snyk
    - CodeQL

---

## 🧪 Test & Coverage Expectations

When `enable_junit` or `enable_jacoco` is true:

- Ensure default test tasks are correctly wired:
  - Maven: `mvn test` and `mvn verify` should run tests and coverage.
  - Gradle: `./gradlew test` and `./gradlew jacocoTestReport` (if configured).
- Encourage:
  - Unit tests at service / controller layer
  - Coverage thresholds to be enforced in build (if team agrees)

---

## 📝 Quick Reference for AI

**Before Starting Setup:**
1. Read `CONFIGURATION.md` — understand `project_type`, flags, and enabled active tools.
2. Read `AI_INSTRUCTIONS.md` — follow all mandatory rules.
3. Read `SETUP_STEPS.md` — understand the execution sequence.

**During Setup:**
1. Follow steps in EXACT order from `SETUP_STEPS.md`.
2. Verify each step succeeds before continuing.
3. Check for errors after every command.
4. STOP on errors — report and wait for instructions.
5. Respect `setup_mode` (`auto` / `step_by_step` / `manual`).

**Configuration Priority:**
1. `.java-ecosystem.config.yaml` (if exists) — highest priority.
2. Inline configuration from `CONFIGURATION.md`.
3. Default settings from `SETUP_STEPS.md`.

---

## 🔗 Related Files

- **CONFIGURATION.md** — Configuration options (read first)
- **SETUP_STEPS.md** — Step-by-step execution guide
- **JAVA_ECOSYSTEM.md** — Overview and navigation

---

**Last Updated**: [AI: Insert date]  
**Maintained By**: Backend / Platform Team
