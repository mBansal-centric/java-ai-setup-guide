# Java Developer Ecosystem Setup Guide (VS Code/IntelliJ + AI‑Driven)

> **Purpose**: This guide helps you set up a consistent **Java backend / Spring Boot** development environment (with **VS Code** or **IntelliJ** and AI tools like Copilot / Cursor) for **new Java projects** OR **existing Java projects**.  
> When you add this file to AI chat context, the assistant must read the other Java setup files and follow them step‑by‑step.

---

> ⚠️ **AI FILE BEHAVIOR OVERRIDE (DO NOT CREATE DUPLICATE FILES)**  
> - This file (`JAVA_ECOSYSTEM.md`) is the **primary, runtime guide**, not a template.  
> - AI assistants **MUST NOT** create new variants like `JAVA_ECOSYSTEM_java.md`, `JAVA_ECOSYSTEM_generated.md`, or similar.  
> - AI MUST use and update the following existing files **as the single source of truth** (never create renamed copies of them):  
>   - `JAVA_ECOSYSTEM.md`  
>   - `CONFIGURATION.md`  
>   - `AI_INSTRUCTIONS.md`  
>   - `SETUP_STEPS.md`  
> - If AI is about to create any new `*_java.md` or `*_generated.md` files, it MUST **stop** and ask the user for confirmation instead.

---

## ⚠️ CRITICAL INSTRUCTIONS FOR AI ASSISTANTS

**MANDATORY**: This guide is split into multiple files for better organization.  
When this file is referenced in chat, you MUST immediately read ALL related files in the same directory:

1. **IMMEDIATELY read `CONFIGURATION.md`** – Contains:
	- `project_type`  
	- Java version strategy  
	- Enabled active tools  
	- Automation flags  
	- **IDE selection via `ide_active`** (vscode / intellij)  
2. **IMMEDIATELY read `AI_INSTRUCTIONS.md`** – Contains:
	- Mandatory behavior rules  
	- What AI MUST do  
	- What AI MUST NOT do  
	- Decision trees  
	- Execution safety requirements    
3. **IMMEDIATELY read `SETUP_STEPS.md`** – Contains:
	- Complete step-by-step instructions  
	- How to create/update config files  
	- How to set up Checkstyle, Spotless, Snyk, Git hooks  
	- Validation steps  

**File Locations**: All files live in the same directory as this file.

**To find the files**:

1. Determine the directory of this file (for example `java-setup/JAVA_ECOSYSTEM.md`)
2. Read the following files from that directory:
   - `CONFIGURATION.md`
   - `AI_INSTRUCTIONS.md`
   - `SETUP_STEPS.md`

**Execution Order (for AI)**:

1. **Read `CONFIGURATION.md`**  
   - Determine project_type  
   - Determine Java version rules  
   - Determine enabled tools (active tools only)  
   - Determine setup_mode and automation flags  

2. **Read `AI_INSTRUCTIONS.md`**  
   - Learn mandatory AI behavior  
   - Learn what not to modify  
   - Learn merging vs overwriting rules  
   - Learn decision trees  

3. **Read `SETUP_STEPS.md`**  
   - Execute steps EXACTLY as defined  
   - Adapt based on `project_type`  

> ❌ Do **NOT** run any setup commands or create files before reading all three files above.

---

## 📖 About This Guide

This setup is designed for:

- **New Java services** → default to **Java 21** (language level + compiler)  
- **Existing Java services** → **respect existing Java version** and configuration, only add missing pieces  
- Both **Maven** and **Gradle (Groovy or Kotlin DSL)** builds  

The guide is split into:

- **`CONFIGURATION.md`** – Central configuration options and flags  
- **`AI_INSTRUCTIONS.md`** – How AI must behave while using these instructions  
- **`SETUP_STEPS.md`** – Detailed, step‑by‑step setup process

---

## 🚀 Quick Start

### Option 1 (Recommended): Reference Main Entry File Only
For AI assistants:

```text
@JAVA_ECOSYSTEM.md
```

AI will automatically detect and load:

- `CONFIGURATION.md`  
- `AI_INSTRUCTIONS.md`  
- `SETUP_STEPS.md`  

### Option 2: Add All Files to Context
1. **Read `CONFIGURATION.md`** first to understand and customize your setup options
2. **Add all files to your chat context** when working with AI assistants (Copilot/Cursor)
3. **Set `project_type` in CONFIGURATION.md** to "new", "existing", or "auto-detect"
4. **Follow the steps** in `SETUP_STEPS.md` (AI will guide you through them) 

---

## 📁 File Structure

A typical layout when you copy this into a repo:

```text
docs/
├── JAVA_ECOSYSTEM.md          # This file – overview & navigation
├── CONFIGURATION.md      # Project type, Java version, tools, VS Code extensions
├── AI_INSTRUCTIONS.md    # AI behavior rules & merging strategy
└── SETUP_STEPS.md        # Step‑by‑step setup instructions
```

---

## 🎯 Goals

This Java ecosystem setup aims to:

- Provide a **standardized Java project structure**
- Maintain consistent formatting and style using **Spotless + Checkstyle**
- Detect vulnerabilities using **Snyk**
- Enforce quality through **native Git pre-commit hooks**
- Auto-generate context files for AI to improve code generation
- Allow reproducible environments with optional devcontainers
- Provide automation via safe, predictable AI behavior

---

## 📋 How to Use – For Developers

1. **Copy the folder** `docs/` into your repository
2. Open **`CONFIGURATION.md`** and:
   - Set `project_type` → `"new"`, `"existing"`, or `"auto-detect"`
   - Confirm Java 21 for new projects (`java_version_default: "21"`)
   - Enable or disable tools (Checkstyle, Spotless, PMD, JaCoCo, JUnit, etc.)
   - Set `ide_active` → `"vscode"` or `"intellij"`
3. Add **`JAVA_ECOSYSTEM.md`** to AI chat context when asking Copilot / Cursor to:
   - Bootstrap a new Java service
   - Standardize an existing Java repo
   - Add quality gates / coverage / static analysis
4. Follow the instructions in **`SETUP_STEPS.md`** yourself  
   or ask AI to execute them step‑by‑step (using terminal + file editing).
5. Add files to chat context when using Copilot or Cursor

---

## 📋 How to Use – For AI Assistants

When the user includes `JAVA_ECOSYSTEM.md` in context or uses a path like:

```text
@file docs/JAVA_ECOSYSTEM.md
```

you MUST:

1. **Locate and read**:

   - `CONFIGURATION.md`  
   - `AI_INSTRUCTIONS.md`  
   - `SETUP_STEPS.md`

2. Detect **project type** from `CONFIGURATION.md`:
   - `"new"` → create a **new** Maven/Gradle project targeting **Java 21**
   - `"existing"` → enhance an existing repo (no destructive changes)
   - `"auto-detect"` → inspect repository (pom.xml / build.gradle / src) to decide
   
3. Detect **ide_active** from `CONFIGURATION.md`:
   - `vscode` OR `intellij`

3. Respect the rules defined in **`AI_INSTRUCTIONS.md`**:
   - No hallucinated paths or plugins
   - No overwriting configs without reading and merging
   - Stop on errors and report them
   - Never downgrade Java version for existing project

4. Follow **`SETUP_STEPS.md`** in order, adapting:
   - Choose Maven or Gradle depending on existing files
   - Apply tools only if `enable_*` flags are `true`
   - For existing projects, merge configs instead of replacing
   
If AI cannot access the files →  
**AI MUST notify the user that additional files must be provided.**

---

## 🧩 Typical Java Stack Covered

This ecosystem is focused on establishing a clean, maintainable, AI-assisted Java backend setup.

- **Language / Runtime**
	- **Java 21** for new projects (default unless overridden)
	- **Respect existing Java versions** for legacy projects (8, 11, 17)

- **Frameworks & Libraries (Optional, Only If Project Uses Them)**
	- Spring Boot (REST, WebFlux, Data JPA, Security, Actuator)
	- JPA / Hibernate
	- Kafka Client (if required by project)
	- Lombok, MapStruct (optional and team-approval based)

> **Note:** These are NOT installed by default. AI enables them only when the project already contains Spring/Kafka or user explicitly requests it.

- **Build Tools**
	- **Maven** (`pom.xml`)
	- **Gradle** (`build.gradle` / `build.gradle.kts`)

- **Quality & Code Style (Active Tools)**
	- **Checkstyle** (Java code style rules)
	- **Spotless** (Formatting enforcement)
	- **Snyk** (Vulnerability scanning)
	- **Native Git Hooks** (pre-commit checks for Checkstyle, Spotless, Snyk)

- **For VS Code (When `ide_active: vscode`)**
	- Red Hat Java (`redhat.java`)
	- Java Extension Pack (`vscjava.vscode-java-pack`)
	- Maven / Gradle extensions
	- Spring Boot tools (**only if Spring present**)
	- Java Test Runner (`vscjava.vscode-java-test`)
	- GitLens
	- SonarLint
	- GitHub Copilot
	- Snyk extension (optional; disabled by default)

- **For IntelliJ (When `ide_active: intellij`)**
	- ✔ Uses built-in Java, Maven, and Gradle support  
	- ✔ Allows plugins (Checkstyle, Lombok, Copilot, Spotless plugin)  
	- ❌ **Does NOT create or modify**:  
	  - `.idea/**`  
	  - `*.iml`  
	  - `.iws`, `.ipr`  
	- ✔ Never generates IntelliJ project files  
	- ✔ All enforcement is through CLI + Git hooks, not IDE configs 

---

## 🎓 Onboarding Checklist (for New Team Members)

1. [ ] Install **JDK (21 recommended)** and ensure `JAVA_HOME` is configured  
2. [ ] Install **Maven** and/or **Gradle** (matching project build tool)  
3. [ ] Install your team’s **active IDE** (based on `ide_active` in CONFIGURATION.md):
   - If `ide_active: vscode` → Install **VS Code** and recommended extensions  
   - If `ide_active: intellij` → Install **IntelliJ IDEA** (Community or Ultimate)
4. [ ] Clone the repository and open it in the correct IDE  
   - VS Code → `.vscode/settings.json` will configure Java tools  
   - IntelliJ → IDE will auto-detect Maven/Gradle (no generated `.idea` files)
5. [ ] Run initial build:
   - Maven: `mvn clean verify`
   - Gradle: `./gradlew clean check`
6. [ ] Verify tests pass and code compiles  
7. [ ] Read `CONTEXT_GUIDE.md` and `AI_USAGE_POLICY.md` (created by setup)  
8. [ ] Try a small change + commit, verify any Git hooks and CI jobs run successfully

---

## 🔄 Project Type Handling

### `project_type: "new"`
- Fresh Java 21 project  
- Create all configs from scratch  
- Apply complete setup flow  

### `project_type: "existing"`
- Detect Java version from existing build files or project structure(Example: pom.xml <maven.compiler.release>, maven-compiler-plugin config,
   Gradle toolchain block, or source/target compatibility)
- Detect existing configs  
- READ then MERGE only  
- Never overwrite user-defined rules  

### `project_type: "auto-detect"`
AI scans repo to determine:

- If Java build files exist → treat as `"existing"`  
- If not → treat as `"new"`  

See `CONFIGURATION.md` for all options.

---

## 📚 Additional Resources

### Java & Backend Development Tools
- **Checkstyle Documentation** – For Java style rules and configuration
- **Spotless Plugin Docs** – For automated formatting (Maven/Gradle)
- **Snyk** – For vulnerability scanning
- **Maven/Gradle Docs** – Build tool references
- **IntelliJ Documentation** – IDE shortcuts, inspections & setup
- **VS Code Java Docs** – Extensions, debugging, build support

---

## 🎓 Onboarding Checklist (Java Developer)

New developers joining the project should complete the following:

1. [ ] Clone the repository
2. [ ] Install **Java (JDK)** — recommended Java 21 (or project-required version)
3. [ ] Install **Maven** or **Gradle** (depending on project)
4. [ ] Install an IDE:
   - VS Code (with Java extensions)
   - OR IntelliJ IDEA
   - OR Cursor IDE
5. [ ] Install recommended IDE plugins (Checkstyle, GitLens, Copilot, SonarLint)
6. [ ] Build project:
   - Maven: `mvn clean install`
   - Gradle: `./gradlew build`
7. [ ] Verify local Git hooks by making a test commit
8. [ ] Run Snyk (if enabled):
   - `tools/snyk/snyk test`
9. [ ] Read:
   - `CONTEXT_GUIDE.md`
   - `AI_USAGE_POLICY.md`
10. [ ] Run tests (if the project uses them):
    - Maven: `mvn test`
    - Gradle: `./gradlew test`

---

## 🔄 Maintenance

### Monthly Tasks
- Review and update **Maven/Gradle dependencies**
- Run **Snyk** and verify no new CVEs
- Update Checkstyle/Spotless rules if needed
- Review CI pipeline health (if enabled)
- Clean up outdated JARs, plugins, or unused configs

### When Adding New Features
- Update architectural notes in `CONTEXT_GUIDE.md`
- Add relevant “prompt recipes” to `prompts.md`
- Ensure new code follows:
  - Checkstyle rules  
  - Spotless formatting  
  - Error-handling guidelines  
  - Logging conventions  
  - Package structure rules  

---

## 🆘 Troubleshooting – Common Issues

Common issues developers face when using the Java Developer Ecosystem Setup.

---

## ❗ 1. Pre-commit hook not running
**Cause:** Hook is not executable or missing.  
**Fix:**  
```bash
chmod +x .git/hooks/pre-commit
```
If the file does not exist, re-run the hook setup step or copy the sample hook script again.

---

## ❗ 2. Checkstyle failing on commit
**Cause:** Code formatting/style violations.  
**Fix:**  
- To see violations:
  ```bash
  mvn checkstyle:check
  ```
- To fix common formatting issues with Spotless:
  ```bash
  mvn spotless:apply
  ```
- In Gradle:
  ```bash
  ./gradlew checkstyleMain
  ./gradlew spotlessApply
  ```

---

## ❗ 3. Spotless formatting not applied
**Cause:** Spotless plugin not installed or skipped in IDE.  
**Fix Options:**  
- Run formatter manually:
  ```bash
  mvn spotless:apply
  ```
- OR enable IDE’s auto-format on save  
- Ensure `spotless` block exists in Maven/Gradle config

---

## ❗ 4. Snyk Scan Errors
**Cause:** Missing authentication, outdated Snyk CLI cache, or incorrect project type (Maven/Gradle).  
**Fix:**  
1. Authenticate Snyk
```bash
tools/snyk/snyk auth
```
2. Clean Snyk cache (optional)
```bash
rm -rf ~/.config/snyk
```
3. Re-run Snyk scan
```bash
tools/snyk/snyk test
```

---

## ❗ 5. Build fails with Java version mismatch
**Cause:** Using a different JDK version than project expects.  
**Fix:**  
- Check required version in:
  - `pom.xml` → `maven-compiler-plugin`  
  - `build.gradle` → `sourceCompatibility`  
- Update `JAVA_HOME`, for example:
  ```bash
  export JAVA_HOME=/usr/lib/jvm/jdk-21
  ```

---

## ❗ 6. IntelliJ showing red imports or build errors
**Fix Options:**  
- **Reload Maven/Gradle project**  
- File → Invalidate Caches → Restart  
- Ensure correct JDK is selected (Project SDK + Module SDK)  
- Enable annotation processors for Lombok if applicable

---

## ❗ 7. VS Code Java language server errors
**Fix:**  
Open Command Palette:  
```
Java: Clean Java Language Server Workspace
```
Then restart VS Code.

---

## ❗ 8. Maven build fails due to missing dependencies
**Cause:** Local repo corruption.  
**Fix:**  
Clear local Maven cache:
```bash
rm -rf ~/.m2/repository
```
Rebuild:
```bash
mvn clean install
```

---

## ❗ 9. Gradle build extremely slow after setup
**Fix Options:**  
- Enable Gradle daemon  
- Enable build caching  
- Use:
  ```bash
  ./gradlew --stop
  ./gradlew clean build --refresh-dependencies
  ```

---

## ❗ 10. Git commit succeeds even if tools fail
**Cause:** Pre-commit hook not configured to exit non-zero.  
**Fix:**  
Ensure your `.git/hooks/pre-commit` ends with:
```bash
exit 1
```
for failed checks and  
```bash
exit 0
```
for success.

---

## ❗ 11. IDE does not format code according to Spotless
**Cause:** IDE formatter != Spotless rules.  
**Fix:**  
- Format code with command line first  
- Sync IDE settings with Spotless rules  
- Avoid mixing IDE auto-format + Spotless mismatched configs

---

## ❗ 12. Project using Lombok shows "Cannot find symbol"
**Fix:**  
- IntelliJ: enable **Annotation Processing**  
- VS Code: ensure Lombok plugin installed (if applicable)

---

## ❗ 13. CI pipeline failing after ecosystem setup
**Cause:** Missing plugins or incorrect Java version.  
**Fix:**  
- Check CI logs for failing tool  
- Verify JDK version set in GitHub Actions/CI YAML  
- Ensure Maven/Gradle plugins match your local environment

---

## 📝 File References

- **[CONFIGURATION.md](./CONFIGURATION.md)** - All configuration options and customization
- **[AI_INSTRUCTIONS.md](./AI_INSTRUCTIONS.md)** - AI behavior rules and guidelines
- **[SETUP_STEPS.md](./SETUP_STEPS.md)** - Detailed step-by-step setup instructions

---

**Last Updated**: [AI: Add current date]  
**Maintained By**: Backend / Platform Team
