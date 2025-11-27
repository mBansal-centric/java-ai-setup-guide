# Java Ecosystem – Setup Steps

> **Purpose**: This file contains the detailed **step-by-step instructions** for setting up a **Java development ecosystem** using Maven/Gradle, active quality tools, VS Code settings or IntelliJ, and Git hooks.  
> AI assistants must follow these steps in sequence and adapt behavior based on `project_type` in `CONFIGURATION.md`.

---

> ⚠️ **AI FILE BEHAVIOR OVERRIDE (DO NOT CREATE DUPLICATE FILES)**  
> - This file (`SETUP_STEPS.md`) is the **primary, runtime guide**, not a template.  
> - AI assistants **MUST NOT** create new variants like `SETUP_STEPS_java.md`, `SETUP_STEPS_generated.md`, or similar.  
> - AI MUST use and update the following existing files **as the single source of truth** (never create renamed copies of them):  
>   - `JAVA_ECOSYSTEM.md`  
>   - `CONFIGURATION.md`  
>   - `AI_INSTRUCTIONS.md`  
>   - `SETUP_STEPS.md`  
> - If AI is about to create any new `*_java.md` or `*_generated.md` files, it MUST **stop** and ask the user for confirmation instead.

---

# 🧩 Pre‑Setup Instructions for AI

Before executing these steps, AI assistants must:

1. Confirm that `CONFIGURATION.md` has been read.  
2. Confirm project type from `project_type`: `"new"`, `"existing"`, or `"auto-detect"`.  
3. Determine build tool based ONLY on:
	- If project_type = "new":
		  → Use build_tool_default from CONFIGURATION.md (no user confirmation)
	- If project_type = "existing":
		  → Detect based on existing files (pom.xml / build.gradle)
	- If project_type = "auto-detect":
		  → If build files exist, use them
		  → If NOT, use build_tool_default 
4. Respect:
   - `setup_mode`
   - Tool flags (`enable_checkstyle`, `enable_spotbugs`, etc.)
   - Java version strategy.

If any of the above is unclear → **ask the user first**.

---

# ✅ Prerequisites Check

**AI Instructions**: Verify these tools are installed. If not, inform the user which ones are missing.

- [ ] **JDK** (Java 21 for new projects; existing project’s version for legacy repos)
  - Check: `java -version`
- [ ] **Maven** (if used)
  - Check: `mvn -v`
- [ ] **Gradle** (if used)
  - Check: `gradle -v` or presence of `gradlew`
- [ ] **Git**
  - Check: `git --version`
- **If `ide_active: vscode`**  
	- [ ] **VS Code**
	- [ ] **VS Code Java extensions** (auto‑handled via `.vscode/extensions.json`)
- **If `ide_active: intellij`**  
	- [ ] **IntelliJ IDEA installed**
	- [ ] AI MUST NOT generate or modify `.idea/**` or `*.iml`
	- [ ] AI MUST provide IntelliJ setup **instructions only**

---

# 🛠️ Step 1 — Initialize Project Structure
This step determines how the setup proceeds for both new and existing Java projects.
AI MUST follow this order strictly, without skipping or assuming anything.

## 1.0 Configuration Loading (AI MUST follow this order)

1. Read `project_type`, `ide_active` from **CONFIGURATION.md**  
2. Check if `.java-ecosystem.config.yaml` exists  
   - If yes → treat YAML as the highest‑priority config  
3. If project is `"existing"` or `"auto-detect"`, detect existing files:  
   - `pom.xml` or `build.gradle`  
   - `src/main/java` and `src/test/java`  
   - `.editorconfig`  
   - `.vscode/settings.json` (if ide_active: vscode)
   - `.git/hooks/pre-commit`  
   - `.github/workflows/*.yml`  
   - `config/checkstyle/checkstyle.xml`
4. For each file:
   - If exists → READ → MERGE → DO NOT OVERWRITE  
   - If missing → create recommended version  
5. Respect config flags:
   - enable_checkstyle  
   - enable_spotless  
   - auto_setup_hooks  
   - auto_setup_ci  
   - create_context_files  
6. After creating/editing each file → validate  
7. On ANY error → STOP and report  
8. Proceed to Step 2 only after all validations succeed  

**Merging Strategy for Existing Configs**
When `project_type` is `"existing"` or `"auto-detect"` finds existing configs, AI must **read → compare → merge**, never overwrite.

**Checkstyle**
	- If `checkstyle.xml` exists:
	  - Read and preserve all existing rules.
	  - Add only **missing recommended rules**.
	  - Do NOT override user-defined severities.
	  - Do NOT remove suppressions or custom modules.
	  - **AI must also ensure the file includes all required rule categories:**
		- Naming conventions  
		- Import rules  
		- Javadoc rules  
		- Whitespace & formatting  
		- Blocks & braces  
		- Code structure rules  
		- Complexity checks  
		- Best-practice coding rules  

**Spotless**
	- If Spotless config exists (in `pom.xml` or `build.gradle`):
	  - Preserve existing formatting rules.
	  - Add missing Google/Palantir/Custom format rules only.
	  - Do NOT override user-defined target patterns.
	  - Do NOT remove custom import order rules.

**Snyk Security Scan**
	- If Snyk CLI is already configured:
		- Do NOT overwrite the existing .snyk policy file.
		- Preserve all user-defined ignore rules and security suppressions.
		- Add only missing recommended options (severity threshold, test/monitor flags).
		- Do NOT replace project-level overrides or organization settings.
		- Ensure the CLI path (tools/snyk/snyk) is preserved.

**Git Hooks (.git/hooks)**
	- If `pre-commit` exists:
	  - Append missing commands (Spotless, Checkstyle, Snyk).
	  - Do NOT delete existing commands.
	  - Preserve developer-added logic.
	  - Ensure correct execution order without overriding script.

**VS Code Settings (if ide_active: vscode)**
	- If `.vscode/settings.json` exists:
	  - Deep merge settings.
	  - User-defined settings always override defaults.
	  - Add only missing Java-related recommendations.

**VS Code Extensions (if ide_active: vscode)**
	- If `.vscode/extensions.json` exists:
	  - Merge arrays, avoid duplicates.
	  - NEVER remove or disable user-selected extensions.
	  - Add only missing recommended Java extensions.

**IntelliJ Configuration (if ide_active: intellij)**
AI MUST:
	- NEVER modify `.idea/**` or `*.iml`  
	- NEVER auto-generate IntelliJ project files  
	- Provide **instructions only** for IntelliJ  
	- Respect existing workspace-level preferences

**Build Files (Maven/Gradle)**
	- Never overwrite `pom.xml` or `build.gradle`.
	- Insert missing plugin declarations at correct locations.
	- Insert missing dependencies ONLY if not already present.
	- Preserve existing indentation, comments, and formatting.

---

## 🏗️ Step 1.1 — Create/Update Base Configuration Files

AI MUST create or merge the following base configuration files (depending on project_type):

- `.editorconfig`
- `config/checkstyle/` folder (if missing)
- `config/checkstyle/checkstyle.xml` (if enabled)
- Spotless config (separate config file, NOT plugin)
- If `ide_active: vscode`
	- `.vscode/settings.json`
	- `.vscode/extensions.json`
	- ⚠️ **AI MUST NOT** create VS Code configuration if `ide_active: intellij`.
- If `ide_active: intellij`
	- ❌ DO NOT create or modify:
	  - `.idea/**`
	  - `*.iml`
	- ✔ Provide IntelliJ setup instructions only  
	- ✔ Rely on Git hooks + CLI tools for quality checks 
- `java-context/CONTEXT_GUIDE.md` (if enabled)
- `java-context/AI_USAGE_POLICY.md` (if enabled)
- `java-context/prompts.md` (optional)
- `.github/workflows/ci.yml` (if auto_setup_ci: true)
- `.git/hooks/pre-commit` (if auto_setup_hooks: true)
- `pom.xml` or `build.gradle` (depending on build_tool_default)
- `scripts/spotless-format.sh` AND `scripts/spotless-format.bat`
- `scripts/spotless-check.sh` AND `scripts/spotless-check.bat`
- `scripts/checkstyle.sh` AND `scripts/checkstyle.bat`
- `scripts/snyk-scan.sh` AND `scripts/snyk-scan.bat`

### `.editorconfig` (Recommended)

```
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
indent_style = space
indent_size = 4

[*.md]
trim_trailing_whitespace = false
```

### `config/checkstyle/checkstyle.xml`

AI must generate full default Google-style or custom rules.

---

# 🧼 Step 2 — Configure Checkstyle, Spotless & Snyk (Non‑plugin approach)

**IMPORTANT:**  
We are NOT using Maven/Gradle plugins for enforcement.  
We will use command‑line execution + `.git/hooks`. This keeps the setup consistent across IntelliJ, VS Code, and Cursor.

## 2.1 Download or Initialize Tools

### Prepare Tool Folder Structure
```
tools/
  checkstyle/
    checkstyle.jar
  spotless/
    google-java-format.jar
  snyk/
    snyk.exe
scripts/
  checklist.sh
  spotless-format.sh
  spotless-check.sh
  snyk-scan.sh
  checklist.bat
  spotless-format.bat
  spotless-check.bat
  snyk-scan.bat
```

**AI Note**
AI assistants cannot download external JAR files.
If any required file is missing, STOP and instruct the user to download it manually.

## 2.2 AI MUST create the following script files with FULL content
AI MUST generate the files EXACTLY as written below 
❌ AI MUST NOT rename files and jar name

### ✔ scripts/spotless-format.sh
AI MUST generate spotless-format.sh script only when `"enable_spotless: true"`

```
#!/bin/sh
FILES=$(git ls-files "*.java")
[ -z "$FILES" ] && echo "No Java files found. Skipping Spotless Format..." && exit 0

java -jar tools/spotless/google-java-format.jar --replace $FILES
```

### ✔ scripts/spotless-format.bat
AI MUST generate spotless-format.bat script only when `"enable_spotless: true"`

```
@echo off
for /f "tokens=*" %%f in ('git ls-files "*.java"') do (
    set FILES=%%f !FILES!
)

if "%FILES%"=="" (
    echo No Java files found. Skipping Spotless Format...
    exit /b 0
)

java -jar tools/spotless/google-java-format.jar --replace %FILES%
```

### ✔ scripts/spotless-check.sh
AI MUST generate spotless-check.sh script only when `"enable_spotless: true"`

```
#!/bin/sh
FILES=$(git ls-files "*.java")
[ -z "$FILES" ] && echo "No Java files found. Skipping Spotless Check..." && exit 0

java -jar tools/spotless/google-java-format.jar --set-exit-if-changed $FILES
```

### ✔ scripts/spotless-check.bat
AI MUST generate spotless-check.bat script only when `"enable_spotless: true"`

```
@echo off
for /f "tokens=*" %%f in ('git ls-files "*.java"') do (
    set FILES=%%f !FILES!
)

if "%FILES%"=="" (
    echo No Java files found. Skipping Spotless Check...
    exit /b 0
)

java -jar tools/spotless/google-java-format.jar --set-exit-if-changed %FILES%
```

### ✔ scripts/checkstyle.sh

AI MUST generate checkstyle.sh script only when `"enable_checkstyle: true"`

```
#!/bin/sh
FILES=$(git ls-files "*.java")
[ -z "$FILES" ] && echo "No Java files found. Skipping Checkstyle..." && exit 0

java -jar tools/checkstyle/checkstyle.jar \
  -c config/checkstyle/checkstyle.xml \
  $FILES
```

### ✔ scripts/checkstyle.bat

AI MUST generate checkstyle.bat script only when `"enable_checkstyle: true"`

```
@echo off
for /f "tokens=*" %%f in ('git ls-files "*.java"') do (
    set FILES=%%f !FILES!
)

if "%FILES%"=="" (
    echo No Java files found. Skipping Checkstyle...
    exit /b 0
)

java -jar tools/checkstyle/checkstyle.jar -c config/checkstyle/checkstyle.xml %FILES%
```

### ✔ scripts/snyk-scan.sh
AI MUST generate snyk-scan.sh script only when `"enable_snyk: true"`

```
#!/bin/sh

# Ensure Snyk binary exists
if [ ! -f "tools/snyk/snyk" ] && [ ! -f "tools/snyk/snyk.exe" ]; then
  echo "Snyk CLI not found in tools/snyk/. Skipping Snyk Scan..."
  exit 0
fi

# Run Snyk test
tools/snyk/snyk test
```

### ✔ scripts/snyk-scan.bat
AI MUST generate snyk-scan.bat script only when `"enable_snyk: true"`

```
@echo off

IF NOT EXIST "tools\snyk\snyk.exe" (
    echo Snyk CLI not found in tools/snyk/. Skipping Snyk Scan...
    exit /b 0
)

tools\snyk\snyk.exe test
```

## 2.3 AI Verification Checklist

AI must ensure:

- All script files exist
- They contain the exact content above
- Scripts are made executable
- Required JAR files exist (if not → stop and notify user)
- .sh files are made executable on Unix/macOS
- .bat files are made executable on Windows

---

# 🪝 Step 3 — Pre‑Commit Hook Setup
AI MUST create the Git hook file with full content, unless it already exists.
AI logic:
	- If `enable_checkstyle` == false → DO NOT add checkstyle line
	- If `enable_spotless` == false → DO NOT ADD spotless line
	- If `enable_snyk` == false → DO NOT ADD snyk line
If the script file already exists:
	- AI MUST append missing lines (based on enabled tools)
	- AI MUST NOT remove or modify existing developer code
	
## OS Detection (Windows vs Linux/macOS)

The hook MUST:
- Run .sh scripts on Linux/macOS
- Run .bat scripts on Windows

AI MUST follow this exact logic:
```
If OS == Windows → run .bat script
Else (Linux/Mac) → run .sh script
```

### 🌍 Cross-Platform Compatibility Rules (MANDATORY)

1. The hook must NOT rely on `chmod` on Windows.  
2. The hook must run correctly under:
   - Git Bash  
   - PowerShell  
   - CMD  
   - IntelliJ’s built-in terminal  
3. **Windows does NOT require executable bits** — Git runs the hook file automatically.  
4. On Linux/macOS → AI MUST still set the executable bit using `chmod +x`.

### ✔ Updated `.git/hooks/pre-commit` (Cross-platform Safe)	

`.git/hooks/pre-commit`:

```sh
#!/bin/sh

# Detect OS
IS_WINDOWS=false
case "$(uname -s)" in
  *CYGWIN*|*MINGW*|*MSYS*)
    IS_WINDOWS=true
    ;;
esac

# --- Spotless (if enabled) ---
if [ -f "scripts/spotless-check.sh" ] || [ -f "scripts/spotless-check.bat" ]; then
  echo "Running Spotless..."
  if [ "$IS_WINDOWS" = true ] && [ -f "scripts/spotless-check.bat" ]; then
    scripts/spotless-check.bat || exit 1
  else
    scripts/spotless-check.sh || exit 1
  fi
fi

# --- Checkstyle (if enabled) ---
if [ -f "scripts/checkstyle.sh" ] || [ -f "scripts/checkstyle.bat" ]; then
  echo "Running Checkstyle..."
  if [ "$IS_WINDOWS" = true ] && [ -f "scripts/checkstyle.bat" ]; then
    scripts/checkstyle.bat || exit 1
  else
    scripts/checkstyle.sh || exit 1
  fi
fi

# --- Snyk Security Scan (if enabled) ---
if [ -f "scripts/snyk-scan.sh" ] || [ -f "scripts/snyk-scan.bat" ]; then
  echo "Running Snyk Security Scan..."
  if [ "$IS_WINDOWS" = true ] && [ -f "scripts/snyk-scan.bat" ]; then
    scripts/snyk-scan.bat || exit 1
  else
    scripts/snyk-scan.sh || exit 1
  fi
fi

exit 0
```

### Make Hook Executable (Cross-Platform Rules)

**Linux / macOS**
```sh
chmod +x .git/hooks/pre-commit
```

**Windows**
- ❗ Do NOT run chmod in PowerShell — it will fail (chmod is not recognized).
- Windows does not require executable permissions for Git hooks.
- As long as .git/hooks/pre-commit exists, Git for Windows will run it automatically
(via Git Bash / MSYS / MINGW under the hood).

---

# 🖥️ Step 4 — IDE Configuration (VS Code / IntelliJ)
AI MUST configure the IDE based strictly on the **`ide_active`** value from `CONFIGURATION.md`.

## 💻 VS Code Configuration (Only if ide_active = vscode)

### `.vscode/settings.json`
**AI Instructions**
- If .vscode/settings.json does not exist → create it with full content below
- If it exists:
	- READ the file first
	- Deep merge settings
	- User settings override defaults
	- Add only missing recommended Java settings

```json
{
  "editor.formatOnSave": true,
  "editor.tabSize": 4,
  "files.trimTrailingWhitespace": true,
  "java.configuration.updateBuildConfiguration": "automatic",
  "files.exclude": {
    "**/.git": true,
    "**/.DS_Store": true,
    "**/target": true,
    "**/build": true
  }
}
```

### `.vscode/extensions.json`
**AI Instructions**
- If .vscode/extensions.json does not exist → create it
- If it exists:
	- Read existing file
	- Merge recommendations array
	- NEVER remove user-added extensions

```json
{
  "recommendations": [
    "redhat.java",
    "vscjava.vscode-java-pack",
    "github.copilot",
    "eamodio.gitlens",
    "sonarsource.sonarlint-vscode"
  ]
}
```

## IntelliJ Configuration (Only if ide_active = intellij)
**AI Instructions**
- ❌ DO NOT create, modify, or delete:
	- .idea/**
	- *.iml
	- .ipr, .iws
- ❌ DO NOT update workspace-level settings
- ✔ Provide instructions only, never generate IntelliJ project files
- ✔ Rely on CLI-based enforcement (Git hooks, Checkstyle, Spotless, Snyk)
- ✔ If user explicitly requests shared project-level code style:
	- Only modify files outside .idea/**
	- NEVER generate .idea/codeStyles or similar workspace files

---

# ⚙️ Step 5 — CI Setup (Optional)
**AI Instructions**
- If .github/workflows/ci.yml exists →
	- Read it
	- Add missing jobs (Spotless, Checkstyle, Snyk)
	- Do NOT remove existing jobs
	- Merge safely
- If the file does NOT exist →
	- Create the full file below

`.github/workflows/ci.yml`:

```yaml
name: Java CI

on: [push, pull_request]

jobs:
  run-checks:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Set up JDK
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: "21"

      - name: Spotless Check
        run: scripts/spotless-check.sh

      - name: Checkstyle
        run: java -jar tools/checkstyle/checkstyle.jar -c config/checkstyle/checkstyle.xml $(git ls-files "*.java")

      - name: Snyk Security Scan
        run: |
          if [ -f tools/snyk/snyk ]; then
            tools/snyk/snyk test
          else
            echo "Snyk CLI not found. Skipping Snyk scan..."
          fi		
```

---

# 📘 Step 6 — Context Files
These files help developers AND AI understand the project architecture, coding standards, and safe AI usage rules.
AI must follow these rules when generating or updating context files:
- Create missing files **inside the `java-context/` folder only**
- When `project_type = existing`, **merge** with existing content instead of replacing it
- **NEVER overwrite custom sections**
- **Add** missing recommended sections if they are absent
- **Validate content** after creation or merge
- **STOP immediately** if ambiguity or merge conflict is detected


## `CONTEXT_GUIDE.md`

⚠️ IMPORTANT:
The **Project Overview** and **Tech Stack Summary** below are FIXED. AI must use them EXACTLY as written, without rewording, shortening, or modifying.

### ☕ Java Version Resolution Rules
AI MUST replace {{java_version}} based on the following rules:
1. If `project_type = "new"` → Use **Java 21**
2. If `project_type = "existing"` → Detect Java version from:
   - `pom.xml` (`<maven.compiler.release>` or compiler plugin config)
   - Gradle toolchain block
   - Source/target compatibility settings
3. If `project_type = "auto-detect"`:
   - Use detected version if available
   - If none detected, default to **Java 21**
4. AI MUST NOT upgrade or change Java versions for existing projects
5. When generating **CONTEXT_GUIDE.md**, the Java version MUST always match this resolved value

### 📌 Sections to Be Generated
AI must expand the full guideline document using best practices for {{java_version}} and **Spring Boot**, including detailed multi-paragraph professional content.

### 🔒 Locked Sections (AI Must Not Modify)
- **Project Overview**: 
	A Hotel Management System designed to streamline room booking, guest management, billing, and staff operations across multiple hotel branches. The system provides centralized control for reservations, check-in/check-out, payments, and housekeeping workflows. The application is built as a set of microservices to ensure scalability, modularity, and easy maintenance.
- **Tech Stack Summary**: 
	- Java Version: {{java_version}}
	- Build Tool: Maven
	- Frameworks: Spring Boot, Spring Web (REST), Spring Data JPA (optional), Spring Cloud (optional for microservices)
	- Architecture: Microservices-based distributed architecture
	- Communication: REST APIs
### Required sections (excluding locked sections above):
- **Folder Structure**
- **Layered Architecture**
- **Naming Conventions**
- **Logging Rules**
- **Exception Handling Rules**
- **API / REST Guidelines**
- **Database / JPA Patterns**
- **Security Guidelines**
- **Testing Rules**
- **Performance Rules**
- **What to Avoid**

AI MUST elaborate each section (excluding **Project Overview** and **Tech Stack Summary**) using best practices for {{java_version}} + Spring Boot frameworks.
All sections(excluding **Project Overview** and **Tech Stack Summary**) must be detailed, multi-paragraph, and production-grade.

## `AI_USAGE_POLICY.md`
AI MUST generate a detailed, structured, enterprise-grade policy document.
AI must explain and structure the following main points:

- **Safe AI Usage Rules**
- **Secrets & Security Handling**
- **Allowed Prompts**
- **Disallowed Prompts**
- **Testing Requirements for AI-Generated Code**
- **Pull Request Guidelines**

## `prompts.md`
AI MUST generate real, practical, expert-level prompts.
Each category MUST contain 5 to 12 fully written prompt examples.

Expand the following categories:

- CRUD prompts  
- Refactoring prompts  
- Testing prompts  
- API prompts  
- Validation prompts  
- Migration / modernization prompts  

---

# 🧪 Step 7 — Validation

## 🔍 Run All Checks

Execute the following commands to verify that formatting, style, and security checks are all working correctly:
⚠️ Important:
If on Windows, run the .bat versions (if they exist).
If on Linux/macOS, run the .sh versions.

✔ Linux / macOS
```
scripts/spotless-check.sh
java -jar tools/checkstyle/checkstyle.jar -c config/checkstyle/checkstyle.xml $(git ls-files "*.java")
scripts/snyk-scan.sh
```

✔ Windows (PowerShell or CMD)
```
scripts\spotless-check.bat
java -jar tools\checkstyle\checkstyle.jar -c config\checkstyle\checkstyle.xml (git ls-files "*.java")
scripts\snyk-scan.bat
```

(If .bat scripts do not exist, run .sh files using Git Bash.)

## 🪝 Test Git Hook

Run a commit to ensure that your **pre-commit hook** executes all tools automatically:

```
git add .
git commit --dry-run -m "Test hook"
```

If the hook works correctly, the commit will only proceed if:
- Spotless passes  
- Checkstyle passes    
- Snyk scan passes (no high-severity vulnerabilities)

If any fail, the commit should be blocked — confirming the ecosystem is working.

---

## 🔗 Related Files

- **[CONFIGURATION.md](./CONFIGURATION.md)** - Configuration options (read first!)
- **[AI_INSTRUCTIONS.md](./AI_INSTRUCTIONS.md)** - AI behavior rules and guidelines
- **[JAVA_ECOSYSTEM.md](./JAVA_ECOSYSTEM.md)** - Overview and navigation

---

**Last Updated**: [AI: Add current date]  
**Maintained By**: Development Team