# Java Ecosystem Configuration Guide (VS Code + AI)

> **Purpose**: This file defines how the **Java Developer Ecosystem Setup** should behave for both **new** and **existing** Java projects.  
AI assistants must read this file **first** before modifying any build files, configs, or workflows.

---

> ⚠️ **AI FILE BEHAVIOR OVERRIDE (DO NOT CREATE DUPLICATE FILES)**  
> - This file (`CONFIGURATION.md`) is the **primary, runtime guide**, not a template.  
> - AI assistants **MUST NOT** create new variants like `CONFIGURATION_java.md`, `CONFIGURATION_generated.md`, or similar.  
> - AI MUST use and update the following existing files **as the single source of truth** (never create renamed copies of them):  
>   - `JAVA_ECOSYSTEM.md`  
>   - `CONFIGURATION.md`  
>   - `AI_INSTRUCTIONS.md`  
>   - `SETUP_STEPS.md`  
> - If AI is about to create any new `*_java.md` or `*_generated.md` files, it MUST **stop** and ask the user for confirmation instead.

---

## 📖 About This File
This file controls the behavior of the **Java Developer Ecosystem Setup Framework**.

It defines:

- Project type (new, existing, auto-detect)
- Java version handling
- Enabled developer tools
- Git hook automation
- IDE-specific behaviors
- Context file generation

**AI Instruction:**  
AI must treat this file as the **source of truth**.

---

## 🧩 IDE Configuration

This setup fully supports **VS Code** and **IntelliJ IDEA**.

```yaml
ide_active: intellij # options: vscode | intellij
```

**Rules for AI**:
 - If vscode → modify .vscode/settings.json, extensions.json, and launch configs.
 - If intellij → modify .idea/* structure and code style configs.
 - Never overwrite existing IDE configs without merging or explicit user approval.
---

## 🎯 Project Type Configuration

This controls how setup behaves for **new vs existing** Java projects.

```yaml
# Project Type – Determines setup approach
project_type: "auto-detect"   # Options: "new" | "existing" | "auto-detect"
```

**Rules for AI**:

- **`"new"`**
  - Creates a **new Java project** (Maven or Gradle) from scratch
  - Defaults to **Java 21** for language level and compiler
  - Applies full ecosystem (static analysis, tests, CI, IDE config)

- **`"existing"`**
  - Treats repo as an **existing Java project**
  - **Never overwrites** build files (`pom.xml`, `build.gradle`) without merging
  - Respects existing Java version and compiler settings
  - Only adds missing tools/configs as per flags

- **`"auto-detect"`** (recommended)
  - AI checks for presence of:
    - `pom.xml`
    - `build.gradle` / `build.gradle.kts`
    - `src/main/java`, `src/test/java`
  - If none exist → treat as `"new"` (create Maven or Gradle project)
  - If they exist → treat as `"existing"` and enhance non‑destructively

---

## 🔢 Java Version & Build Tool Configuration

```yaml
# Java Version Strategy
java_version_default: "21"          # Default for brand‑new projects
respect_existing_java_version: true # Do NOT downgrade existing projects
minimum_supported_version: "8"      # Used only for validation/warnings

# Build Tool Strategy
build_tool_default: "maven"         # DEFAULT for new projects
respect_existing_build_tool: true   # Use existing tool for existing repos
allow_gradle_for_new_projects: false  # AI must NOT auto-select Gradle unless user explicitly says so
auto_detect_build_tool: true       # Only for existing projects
```

**Rules for AI**:

- For **new** projects:
  - Configure compiler / language level to **Java 21**
  - Use 21 for CI matrix baseline (can add 17 optionally)
  - AI MUST use build_tool_default for NEW projects.

- For **existing** projects:
  - Detect version from:
    - Maven: `maven-compiler-plugin` (`release`, `source`, `target`)
    - Gradle: `java { sourceCompatibility / targetCompatibility }`
  - **Never** switch existing project to Java 21 automatically
  - You may suggest upgrade, but do not perform it unless explicitly requested

---

## ⚙️ Setup Mode & Automation Flags

These flags control how much can be done automatically.

```yaml
# Setup Mode Configuration
setup_mode: "auto"                    # auto | step_by_step | manual

# Automation
auto_setup_hooks: true                 # Enable .git/hooks generation
create_context_files: true             # CONTEXT_GUIDE.md, AI_USAGE_POLICY.md, prompts.md
auto_setup_ci: false                   # Disable CI setup (Snyk/CodeQL unused)
```

**Behavior**:

- `"auto"` – AI can apply all enabled flags (good for greenfield)  
- `"step_by_step"` – AI must show each step and wait for confirmation before changing files  
- `"manual"` – AI should only generate snippets and instructions, not apply changes automatically

---

## 🧰 Tool & Quality Configuration (Inline Flags)

These booleans drive which tools are provisioned.

```yaml
# Active tools
enable_checkstyle: true
enable_spotless: true
enable_snyk: true
```

AI must enable only what is explicitly `true`. For existing projects, check whether a tool is already present before re‑adding it.

---

## 💻 VS Code Extensions Configuration (if ide_active = "vscode")

Inline flags for core extensions:

```yaml
extensions:
  java_core: true          # redhat.java
  java_pack: true          # vscjava.vscode-java-pack
  spring_boot: false       # Disabled unless Spring Boot project detected
  java_test_runner: false  # Disabled (JUnit is off)
  maven: true              # vscjava.vscode-maven
  gradle: true             # vscjava.vscode-gradle
  gitlens: true
  copilot: true
  sonarlint: true
  snyk: false              # Disabled
```

**Extension ID Reference** (for `.vscode/extensions.json`):

- Java Language Support: `redhat.java`
- Java Extension Pack: `vscjava.vscode-java-pack`
- Spring Boot Extension Pack: `vscjava.vscode-spring-boot`
- Java Test Runner: `vscjava.vscode-java-test`
- Maven: `vscjava.vscode-maven`
- Gradle: `vscjava.vscode-gradle`
- GitLens: `eamodio.gitlens`
- Copilot: `github.copilot`
- SonarLint: `sonarsource.sonarlint-vscode`
- Snyk: `snyk-security.snyk-vulnerability-scanner`

---

## 📝 Git Hooks Usage (Important)

Java projects must use **native Git hooks**.

Tools enforced via `.git/hooks/pre-commit`:

- Checkstyle  
- Spotless    
- Snyk

---

## 🔗 Related Files

- **`JAVA_ECOSYSTEM.md`** – Overview & navigation  
- **`AI_INSTRUCTIONS.md`** – Mandatory AI rules and merging strategy  
- **`SETUP_STEPS.md`** – Step‑by‑step setup instructions  
- **`CONTEXT_GUIDE.md`** – Project architecture & conventions (created by setup)  
- **`AI_USAGE_POLICY.md`** – How to safely use AI for Java (created by setup)

---

## 📝 Note for AI (Execution Order & Safety Rules)

1. **First**: Read the `project_type` setting in `CONFIGURATION.md`.
2. **Second**: Use all inline configuration values from `CONFIGURATION.md`  
   as the **single source of truth** for project setup.
3. **IDE-specific handling**:  
   - Respect the `ide_active` value:  
     - `vscode` → Apply/modify `.vscode/settings.json` and related VS Code configs.  
     - `intellij` → Do **not** modify `.idea/**` or IntelliJ project files; rely on CLI tools instead.  
   - Do **not** overwrite any existing IDE settings without merging or explicit user confirmation.
4. **Always inspect existing project configurations** before making changes:  
   - `pom.xml` / `build.gradle`  
   - `.vscode/settings.json` or `.idea/*`  
   - `config/checkstyle/*`  
   - `tools/` (Checkstyle, Spotless, Snyk binaries)  
   - `.git/hooks/*`
5. **For existing projects (`project_type = existing`)**:  
   - Read → compare → merge (never overwrite).  
   - Add only missing configurations.  
   - Preserve all existing Java versions, build settings, and custom tool rules.
6. **Conflict handling**:  
   - If a user-defined config conflicts with a recommended default:  
     - **Stop and ask the user how to proceed**.  
     - Provide choices: *overwrite*, *merge*, or *skip*.
7. **Safety rule**:  
   - At every step, validate before writing changes.  
   - If ambiguity is detected, AI must request user clarification.

### AI Safety Rules
- Do **NOT** add or enable tools explicitly disabled in config  
  (PMD, SpotBugs, JUnit, Mockito, JaCoCo, Snyk, CodeQL).  
- Do **NOT** upgrade Java version automatically for existing projects.  
- Do **NOT** delete or overwrite user files without permission.  

---