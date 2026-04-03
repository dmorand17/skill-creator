# Skill Creator for Claude Code

> **Usage:** Reference this file with `@skills/skill-creator.md` when creating or updating a skill that extends Claude Code with specialized knowledge, workflows, or tool integrations.

This skill provides guidance for creating effective skills for Claude Code.

## About Skills

Skills are modular, self-contained markdown files that extend Claude Code's capabilities by providing specialized knowledge, workflows, and domain expertise. They transform a general-purpose session into a specialized one when `@`-referenced by the user.

### What Skills Provide

1. Specialized workflows - Multi-step procedures for specific domains
2. Tool integrations - Instructions for working with specific file formats or APIs
3. Domain expertise - Company-specific knowledge, schemas, business logic
4. Bundled resources - Reference documents loaded on demand

### How Skills Work in Claude Code

Unlike Kiro's auto-triggering system, Claude Code skills are explicitly invoked:

- **Always-loaded**: Add skill content to `CLAUDE.md` (impacts all conversations in that project)
- **On-demand**: Store as a separate file and `@`-reference it: `@skills/my-skill.md`
- **References**: Supporting docs can be `@`-referenced individually: `@skills/my-skill/references/details.md`

## Core Principles

### Concise is Key

The context window is shared with system prompts, conversation history, and user requests.

**Default assumption: Claude is already capable.** Only add context Claude doesn't already have. Challenge each piece of information: "Does Claude really need this?" and "Does this justify its token cost?"

Prefer concise examples over verbose explanations.

### Set Appropriate Degrees of Freedom

Match specificity to the task's fragility and variability:

**High freedom (text-based instructions)**: Multiple approaches are valid, decisions depend on context, or heuristics guide the approach.

**Medium freedom (pseudocode or scripts with parameters)**: A preferred pattern exists, some variation is acceptable, or configuration affects behavior.

**Low freedom (specific scripts, few parameters)**: Operations are fragile and error-prone, consistency is critical, or a specific sequence must be followed.

### Anatomy of a Skill

Every skill consists of a main markdown file and optional bundled resources:

```
skill-name/
├── skill-name.md (required)         # Main skill file
└── references/ (optional)
    ├── topic-a.md                   # Loaded on demand with @
    ├── topic-b.md
    └── scripts/                     # Executable code
```

#### Main Skill File (required)

- **Usage note at top**: Explain how to reference the skill and its supporting files
- **Body**: Instructions and guidance

#### Bundled Resources (optional)

##### References (`references/`)
Documentation and reference material loaded on demand with `@`.

- **When to include**: For documentation Claude should reference while working
- **Examples**: Database schemas, API docs, domain knowledge, company policies
- **Benefits**: Keeps main skill file lean; users load only what's needed
- **Best practice**: If files are large (>10k words), include search patterns in the main file
- **Avoid duplication**: Information should live in either the main file or references, not both

##### Scripts
Executable code for tasks requiring deterministic reliability or that are repeatedly rewritten.

- **When to include**: Same code is rewritten repeatedly or deterministic reliability is needed
- **Where to put**: `references/scripts/` or inline in the skill file

#### What to Not Include

Do NOT create extraneous documentation:
- INSTALLATION_GUIDE.md
- QUICK_REFERENCE.md (unless it's the actual skill content)
- CHANGELOG.md

The skill should only contain information needed to do the job.

### Progressive Disclosure Design

Skills use a two-level approach in Claude Code:

1. **Main skill file** - When `@`-referenced by user (~5k words max)
2. **Reference files** - Loaded on demand with additional `@` references (unlimited)

Keep the main skill file under 500 lines. Split content into separate reference files when approaching this limit.

**Pattern 1: High-level guide with references**
```markdown
# PDF Processing

## Quick start
Extract text with pdfplumber: [code example]

## Advanced features
- **Form filling**: See @skills/pdf/references/forms.md
- **API reference**: See @skills/pdf/references/reference.md
```

**Pattern 2: Domain-specific organization**
```
bigquery-skill/
├── bigquery.md (overview and navigation)
└── references/
    ├── finance.md
    ├── sales.md
    └── product.md
```

**Guidelines:**
- Avoid deeply nested references - keep references one level deep from the main skill file
- For files longer than 100 lines, include a table of contents

## Skill Creation Process

1. Understand the skill with concrete examples
2. Plan reusable skill contents (scripts, references)
3. Initialize the skill directory structure
4. Write the skill (implement resources and main file)
5. Iterate based on real usage

### Step 1: Understanding the Skill

Clearly understand concrete examples of how the skill will be used.

Example questions:
- "What functionality should this skill support?"
- "Can you give examples of how this skill would be used?"
- "What would a user say that should trigger this skill?"

### Step 2: Planning Reusable Contents

Analyze each example by:
1. Considering how to execute the example from scratch
2. Identifying what scripts and references would be helpful

Example: For a `pdf-editor` skill handling "Help me rotate this PDF":
1. Rotating a PDF requires re-writing the same code each time
2. A `references/rotate_pdf.py` script would be helpful

Example: For a `big-query` skill handling "How many users logged in today?":
1. Querying BigQuery requires re-discovering table schemas each time
2. A `references/schema.md` file documenting schemas would be helpful

### Step 3: Initialize the Skill

Create the skill directory structure:

```
skill-name/
├── skill-name.md
└── references/
```

### Step 4: Write the Skill

#### Start with Reference Contents

Implement the `references/` files identified in planning.

Test any scripts by running them to ensure they work correctly.

#### Write the Main Skill File

**Writing Guidelines:** Always use imperative/infinitive form.

##### Usage Note (top of file)

Begin every skill file with a usage note:

```markdown
> **Usage:** Reference this file with `@skills/skill-name/skill-name.md` when [trigger condition]. Load reference files on demand with `@skills/skill-name/references/<filename>.md`.
```

##### Body

Write instructions for using the skill and its bundled resources.

- Include all "when to use" information at the top
- Use progressive disclosure: overview first, details in references
- Link to reference files with `@skills/skill-name/references/filename.md` syntax

### Step 5: Iterate

After testing the skill:
1. Use the skill on real tasks
2. Notice struggles or inefficiencies
3. Identify how the main file or references should be updated
4. Implement changes and test again
