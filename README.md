# AI Skills

Reusable AI assistant skills for working with specific tools, APIs, and workflows.

## Skills

- `breeze-project-management` - Breeze project management API workflows for projects, cards/tasks, lists, swimlanes, todos, comments, users, notifications, time entries, and reports.
- `minimal-coherent-change` - Implement fixes and features with the smallest coherent change, reusing existing flows and preserving system semantics.
- `sentry-issue-to-pr` - Investigate a Sentry issue, implement an evidence-backed fix, validate it, and open a GitHub pull request.

## Structure

Each skill lives in its own directory under `skills/` and includes a `SKILL.md` file with metadata and operating instructions.

```text
skills/
  breeze-project-management/
    SKILL.md
  minimal-coherent-change/
    SKILL.md
  sentry-issue-to-pr/
    SKILL.md
```

## Usage

Install with the Skills CLI:

```bash
npx skills add khatriafaz/ai-skills
```

Install a specific skill:

```bash
npx skills add khatriafaz/ai-skills --skill breeze-project-management
npx skills add khatriafaz/ai-skills --skill minimal-coherent-change
npx skills add khatriafaz/ai-skills --skill sentry-issue-to-pr
```

You can also copy or reference the relevant skill directory in an AI agent environment that supports custom skills.

### Use minimal-coherent-change for every implementation

The skill description instructs agents to load it before every implementation-planning or code-changing task, including small edits and new projects, alongside other applicable skills.

Skill descriptions guide discovery; they cannot guarantee loading across all agents. For an explicit requirement, add this portable rule to your agent's persistent instructions (for example, `AGENTS.md`, `CLAUDE.md`, or its equivalent global rules):

```text
Before planning an implementation or changing code, load and follow the
minimal-coherent-change skill's SKILL.md from the available skills.
Apply it alongside any other relevant skills, including for small edits
and new projects. If it is already loaded in the current context, use it
without rereading. If unavailable, say so and follow the smallest coherent
change principle without claiming to have loaded the skill.
```

Configure the instruction location and skill discovery according to your agent; neither requires Pi.
