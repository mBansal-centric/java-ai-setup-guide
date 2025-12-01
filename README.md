# Java Developer Ecosystem Setup (VS Code/IntelliJ + AI)

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
	   <!-- Use the version that support your Java version:
               - Java 17 → google-java-format 1.17.0
               - Java 21+ → latest release (2.x or newer)
          -->
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

## 🤖 Recommended AI Models for This Ecosystem

This Java ecosystem is **tested, and verified** with the following AI models:

### ⭐ Best Overall — **Claude Sonnet 4.5 (Highly Recommended)**
- Exceptional adherence to rules, configs, and multi-file merges  
- Safest behavior for editing existing code  
- Strongest at respecting `CONFIGURATION.md` and `SETUP_STEPS.md`  
- Best performance with long context and tool instructions  

### ✔ Fully Compatible
- GitHub Copilot Chat (VS Code / IntelliJ)

### ⚠ Not Recommended for Setup Steps
- Models with limited context  
- Lightweight LLMs / mobile LLMs  
- Any model that cannot load multiple md files

> **Tip:** For the most accurate and predictable setup behavior, always run the ecosystem with **Claude Sonnet 4.5** when available.

---

## 🚀 Quick Start

### 1. Copy Java Setup Folder

Copy the `java-setup/` and `tools/` folder into the root of your Java repository:

```text
your-repo/
  docs/
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

Open `docs/CONFIGURATION.md` — this is the **single source of truth** for how the Java ecosystem behaves.  
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

Open **`docs/SETUP_STEPS.md`** and navigate to **Step 6 (CONTEXT_GUIDE.md)**.
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

### How to Modify Configuration

1. **Project type & automation** → `CONFIGURATION.md`
2. **AI behavior rules** → `AI_INSTRUCTIONS.md`
3. **Detailed steps & snippets** → `SETUP_STEPS.md`

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

## 📚 Files in `docs/`

- **`README.md`** – This quick overview and how‑to.
- **`JAVA_ECOSYSTEM.md`** – Entry point for AI and human readers.
- **`CONFIGURATION.md`** – Configuration and tool selection.
- **`AI_INSTRUCTIONS.md`** – Rules that AI must follow.
- **`SETUP_STEPS.md`** – The full step‑by‑step setup guide.

---

**Last Updated**: [Add current date]  
**Maintained By**: Backend / Platform Team
