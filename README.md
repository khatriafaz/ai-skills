# AI Skills

Reusable AI assistant skills for working with specific tools, APIs, and workflows.

## Skills

- `breeze-project-management` - Breeze project management API workflows for projects, cards/tasks, lists, swimlanes, todos, comments, users, notifications, time entries, and reports.

## Structure

Each skill lives in its own directory under `skills/` and includes a `SKILL.md` file with metadata and operating instructions.

```text
skills/
  breeze-project-management/
    SKILL.md
```

## Usage

Install with the Skills CLI:

```bash
npx skills add khatriafaz/ai-skills
```

Install only the Breeze skill:

```bash
npx skills add khatriafaz/ai-skills --skill breeze-project-management
```

You can also copy or reference the relevant skill directory in an AI agent environment that supports custom skills.
