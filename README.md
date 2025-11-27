# Java Developer Ecosystem Setup (VS Code + AI)

> **AI‑assisted setup guide** for configuring Java backend projects (new Java 21 services or existing Java apps) with VS Code, testing, static analysis, CI, and AI context files.

---

## 🟢 Active Tools (Enabled in This Project)

This Java ecosystem uses **only the following tools**:

- **Checkstyle** – Code quality & style enforcement  
- **Spotless** – Automatic formatting  
- **Snyk** – Vulnerability scanning  
- **Native Git Hooks (.git/hooks)** – Pre-commit checks  

### ⚠️ Manual Download Required for Tool JARs

Some tools in this Java ecosystem require external JAR files or CLI archives.

**AI assistants (Copilot/Cursor) cannot download these files automatically.**  
Developers must manually download them and place them in the correct directories.

#### Required Downloads

1. **Checkstyle JAR**  
   Download: https://github.com/checkstyle/checkstyle/releases/  
   Save as:  
	   ```
	   tools/checkstyle/checkstyle.jar
	   ```
2. **google-java-format JAR** (used by Spotless CLI)  
	Download google-java-format.jar: https://github.com/google/google-java-format/releases  
	Save as:  
	   ```
	   tools/spotless/google-java-format.jar
	   ```
3. **SNYK JAR** (used by Spotless CLI)  
	Download Snyk Exe: https://github.com/snyk/snyk/releases  
	Save as:  
	   ```
	   tools/snyk/snyk.exe   (Windows)
	   ```	  
	After download, authenticate Snyk once:
		```
		tools/snyk/snyk.exe auth
		```
	This opens a browser login or requests a token.

---

## 🚀 Quick Start

### 1. Copy Java Setup Folder

Copy the `java-setup/` and `tools/` folder into the root of your Java repository:

```text
your-repo/
  java-setup/
    JAVA_ECOSYSTEM.md
    CONFIGURATION.md
    AI_INSTRUCTIONS.md
    SETUP_STEPS.md
  tools/
	checkstyle/
	dependency-check/
	snyk/
	spotless
```

### 2. Configure Project Type & Tools

Open `java-setup/CONFIGURATION.md` — this is the **single source of truth** for how the Java ecosystem behaves.  
All project-type, tooling, and automation flags are defined in one consolidated block.

- In the **Project Type Configuration**:
	- Set `project_type`:
		- `"new"` → new Java 21 project
		- `"existing"` → existing project, keep current Java version
		- `"auto-detect"` → let AI detect based on files
- In the **Java Version & Build Tool Configuration**:
	- Set `java_version_default` and `build_tool_default`
- In the **Tool & Quality Configuration (Inline Flags)**:
	- Set `enable_checkstyle`
	- Set `enable_spotless`
	- Set `enable_snyk`
		
### 3. Update Core Project Details

Open **`java-setup/SETUP_STEPS.md`** and navigate to **Step 6 (CONTEXT_GUIDE.md)**.
- **Project Overview**  
  Brief description of business context, purpose, domain, and overall architecture intention.

- **Tech Stack Summary**  
  Java version, frameworks, libraries, build tool, database, messaging systems, cloud platform, and any major architectural patterns.

### 4. Use in Copilot / Cursor Chat

In Copilot Chat or Cursor, reference the main file:

```text
@file java-setup/JAVA_ECOSYSTEM.md
@file java-setup/CONFIGURATION.md
@file java-setup/AI_INSTRUCTIONS.md
@file java-setup/SETUP_STEPS.md

Read the complete Java setup ecosystem:
- JAVA_ECOSYSTEM.md
- CONFIGURATION.md
- AI_INSTRUCTIONS.md
- SETUP_STEPS.md

Follow ALL rules, constraints, merging strategies, and active tool settings defined in these files.

Respect `project_type` from CONFIGURATION.md:
- "new" → create all files from scratch
- "existing" → read → compare → merge (never overwrite)
- "auto-detect" → detect project state, then follow the correct path

Do NOT add anything outside the ecosystem rules.
Do NOT overwrite any existing files; always read → compare → merge.
Do NOT install or enable tools other than the active tools defined in configuration.
Use only the active tools (Checkstyle, Spotless CLI, Snyk, Git Hooks).

If `create_context_files: true`, generate:
- CONTEXT_GUIDE.md
- AI_USAGE_POLICY.md
- prompts.md

Before applying any changes:
1. Summarize exactly what you plan to do.
2. Wait for my confirmation.
3. Apply changes step-by-step using SETUP_STEPS.md.
```

You can add extra requirements like:

- “Use Maven and keep existing Java version.”
- “Enable Checkstyle and Spotless only.”
- “Generate Git hooks but do not apply automatically.”

---
## 🔍 How to Check and Modify Configuration

### Check Current Configuration
Open `CONFIGURATION.md` and verify:

- project_type (new / existing / auto-detect)
- Java version settings
- Maven/Gradle detection
- Enabled tools:
  - enable_checkstyle
  - enable_spotless
  - enable_snyk
- Git hook automation:
  - auto_setup_hooks

If `.java-ecosystem.config.yaml` exists → it overrides inline settings.

### How to Modify Configuration

1. **Project type & automation** → `CONFIGURATION.md`
2. **AI behavior rules** → `AI_INSTRUCTIONS.md`
3. **Detailed steps & snippets** → `SETUP_STEPS.md`

If you need more control, create `.java-ecosystem.config.yaml` in the repository root and follow the examples in `CONFIGURATION.md`.

### Advanced Configuration
Create `.java-ecosystem.config.yaml` for:

- custom Checkstyle rules
- Spotless formatting rules
- Custom snyk config  
- CI overrides  
- Merge behavior 

If present:
- AI reads YAML **first**
- CONFIGURATION.md becomes secondary
- AI must not overwrite configs without merging  

---

## 💬 Example AI Prompts

### Basic Ecosystem Setup

```text
@java-setup/JAVA_ECOSYSTEM.md

Set up a standard Java backend ecosystem for this project using the configuration in CONFIGURATION.md.
Respect the current Java version and build tool.
Summarize the changes first, then apply them step by step.
```

### New Java 21 Project with Maven

```text
@java-setup/JAVA_ECOSYSTEM.md

Create a new Java 21 Maven service in this repository using the Java setup guide.

Requirements:
- Enable Checkstyle, Spotless, and Snyk
- Generate CONTEXT_GUIDE.md and prompts.md
```

---

## ✅ Verify Setup

After setup completes, run:

For **Maven**:

```bash
mvn clean verify
```

For **Gradle**:

```bash
./gradlew clean check
```

Then, open the project in VS Code and ensure:

- VS Code Java extension works  
- Checkstyle errors appear  
- Spotless formatting works  
- Snyk scan runs successfully  
- Git hooks prevent invalid commits 

---

## 🆘 Quick Troubleshooting

| Issue | Fix |
|------|------|
| AI ignored config | Check `project_type` |
| Wrong Java version | Set `respect_existing_java_version: true` |
| Git hooks failing | Run `chmod +x .git/hooks/pre-commit` |
| Checkstyle skipped | Ensure plugin exists in pom/gradle |
| Spotless not formatting | Run `mvn spotless:apply` |

---

## 📝 Git Hooks for Java

Java uses **native Git hooks** located in `.git/hooks`.

### Pre-commit example:
```sh
#!/bin/sh
mvn spotless:check || exit 1
mvn checkstyle:check || exit 1
tools/snyk/snyk test || { echo '❌ Snyk vulnerabilities found'; exit 1; }
mvn test -q || exit 1
```

Make executable:
```
chmod +x .git/hooks/pre-commit
```

---

## 📚 Files in `java-setup/`

- **`README.md`** – This quick overview and how‑to.
- **`JAVA_ECOSYSTEM.md`** – Entry point for AI and human readers.
- **`CONFIGURATION.md`** – Configuration and tool selection.
- **`AI_INSTRUCTIONS.md`** – Rules that AI must follow.
- **`SETUP_STEPS.md`** – The full step‑by‑step setup guide.

---

**Last Updated**: [Add current date]  
**Maintained By**: Backend / Platform Team
