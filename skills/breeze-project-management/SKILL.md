---
name: breeze-project-management
description: Breeze project management API workflows. Use this skill whenever the user asks to inspect, create, update, move, report on, or automate Breeze projects, cards/tasks, lists/stages, swimlanes, todos, comments, users, notifications, time entries, or reports through the Breeze REST API, even if they say "Breeze task", "Breeze card", "project board", "time tracking", or "team activity" instead of naming the API.
---

# Breeze Project Management

Use this skill to work safely and efficiently with the Breeze REST API.

Primary docs: `https://www.breeze.pm/api`

## Intent

Breeze exposes project-management data through predictable JSON endpoints under `https://api.breeze.pm`. The agent's job is to translate user intent into the smallest safe API interaction, avoid leaking credentials, and elevate sensitive operations before execution.

## Authentication And Team Context

Prefer API-token auth over username/password Basic auth.

Use these environment variables unless the user gives different names:

- `BREEZE_API_TOKEN`
- `BREEZE_TEAM_ID`

Do not ask the user to paste tokens, usernames, passwords, or team IDs into chat. Ask them to set environment variables instead.

Authentication examples:

```bash
curl -u "$BREEZE_API_TOKEN:" "https://api.breeze.pm/projects.json"
curl "https://api.breeze.pm/projects.json?api_token=$BREEZE_API_TOKEN"
```

If the account belongs to multiple teams, include team context on every request:

```bash
curl -u "$BREEZE_API_TOKEN:" -H "HTTP_X_TEAM_ID: $BREEZE_TEAM_ID" "https://api.breeze.pm/projects.json"
```

Use `GET /me.json` to discover the current user and available teams when needed.

## Operating Workflow

1. Identify the resource, operation, target IDs, and team context.
2. For reads, call the narrowest GET endpoint that answers the question.
3. For creates/updates, build a minimal JSON payload from explicit user intent.
4. Before sensitive actions, pause and show the endpoint, target IDs, payload summary, and expected impact. Execute only after explicit confirmation.
5. After execution, summarize IDs, names, changed fields, and any next-step IDs the user will need.

## Sensitive Actions

Elevate sensitive actions to the user before execution. Confirmation must identify the exact action, endpoint, target resource, and relevant IDs.

Destructive or hard-to-reverse actions:

- Delete projects, workspaces, cards/tasks, lists/stages, swimlanes, comments, todo lists, todos, or time entries.
- Archive projects or cards/tasks.
- Remove users from projects or cards/tasks.

Access, assignment, and notification actions:

- Add people to projects or cards/tasks, because this can grant access or notify users.
- Change card/task assignees through `invitees` or people endpoints.
- Mark notifications read or unread, because this changes user workflow state.

Visibility and business-impacting changes:

- Move a card/task to a different project with `new_project_id`.
- Change a card/task's `done`, `hidden`, `archived`, or `deleted_at` state.
- Reactivate an archived project.
- Change project workspace, budget, hourly rate, or currency fields.
- Change due dates, start dates, planned time, tags, or custom fields on existing cards/tasks.

Time and reporting actions:

- Update or delete time entries.
- Generate broad reports that may expose user activity, billing, or workload data.

Bulk or ambiguous mutations:

- Any request affecting multiple users, cards/tasks, projects, or time entries.
- Any mutation where the target name cannot be verified before execution.
- Any operation using an ID without enough context to confirm the target.

Actions safe without extra confirmation when explicitly requested and unambiguous:

- Read-only list/get requests.
- Create projects, workspaces, cards/tasks, lists/stages, swimlanes, comments, todo lists, or todos.
- Create time entries when the user supplied the target card/task and tracked time.
- Move cards/tasks between lists/stages or swimlanes.
- Move lists/stages.
- Fix typo-level text fields such as names, descriptions, comments, todo names, or todo-list names.

## Endpoint Map

Projects:

- `GET /projects.json`
- `GET /projects/:project_id.json`
- `POST /projects.json`
- `PUT /projects/:project_id.json`
- `DELETE /projects/:project_id.json`
- `GET /projects/:project_id/archive.json`
- `GET /projects/:project_id/reactivate.json`
- `GET /projects/:project_id/people.json`
- `POST /projects/:project_id/people.json`
- `DELETE /projects/:project_id/people/:user_id.json`

Workspaces:

- `GET /workspaces`
- `GET /workspaces/:workspace_id.json`
- `POST /workspaces.json`
- `PUT /workspaces/:workspace_id.json`
- `DELETE /workspaces/:workspace_id.json`

Cards/tasks:

- `GET /projects/:project_id/cards.json` returns project cards grouped by stage.
- `GET /V2/projects/:project_id/cards.json` returns a paginated array, 100 per page.
- `GET /v2/cards.json` supports cross-project card filters such as `creator_id` and `assigned_id`.
- `GET /projects/:project_id/stages/:stage_id/cards.json`
- `GET /projects/:project_id/swimlanes/:swimlane_id/cards.json`
- `GET /projects/:project_id/cards/:card_id.json`
- `POST /projects/:project_id/cards.json`
- `PUT /projects/:project_id/cards/:card_id.json`
- `DELETE /projects/:project_id/cards/:card_id.json`
- `POST /projects/:project_id/cards/:card_id/move.json`
- `POST /projects/:project_id/cards/:card_id/people.json`
- `DELETE /projects/:project_id/cards/:card_id/people/:user_id.json`

Lists/stages:

- `GET /projects/:project_id/stages.json`
- `POST /projects/:project_id/stages.json`
- `PUT /projects/:project_id/stages/:stage_id.json`
- `DELETE /projects/:project_id/stages/:stage_id.json`
- `PUT /projects/:project_id/stages/:stage_id/move.json`

Swimlanes:

- `GET /projects/:project_id/swimlanes.json`
- `POST /projects/:project_id/swimlanes.json`
- `PUT /projects/:project_id/swimlanes/:swimlane_id.json`
- `DELETE /projects/:project_id/swimlanes/:swimlane_id.json`

Comments:

- `GET /projects/:project_id/cards/:card_id/comments.json`
- `POST /projects/:project_id/cards/:card_id/comments.json`
- `PUT /projects/:project_id/cards/:card_id/comments/:comment_id.json`
- `DELETE /projects/:project_id/cards/:card_id/comments/:comment_id.json`

Todo lists and todos:

- `GET /projects/:project_id/cards/:card_id/todo_lists.json`
- `POST /projects/:project_id/cards/:card_id/todo_lists.json`
- `PUT /projects/:project_id/cards/:card_id/todo_lists/:todo_list_id.json`
- `DELETE /projects/:project_id/cards/:card_id/todo_lists/:todo_list_id.json`
- `GET /projects/:project_id/cards/:card_id/todo_lists/:todo_list_id/todos.json`
- `POST /projects/:project_id/cards/:card_id/todo_lists/:todo_list_id/todos.json`
- `PUT /projects/:project_id/cards/:card_id/todo_lists/:todo_list_id/todos/:todo_id.json`
- `DELETE /projects/:project_id/cards/:card_id/todo_lists/:todo_list_id/todos/:todo_id.json`

Time tracking:

- `GET /projects/:project_id/cards/:card_id/time_entry.json`
- `POST /projects/:project_id/cards/:card_id/time_entry.json`
- `PUT /projects/:project_id/cards/:card_id/time_entry/:timeentry_id.json`
- `DELETE /projects/:project_id/cards/:card_id/time_entry/:timeentry_id.json`
- `GET /running_timers.json`

Activity, notifications, reports, and users:

- `GET /activities.json`
- `GET /activities/:user_id.json`
- `GET /projects/:project_id/activities.json`
- `GET /projects/:project_id/activities/:user_id.json`
- `GET /notifications.json`
- `PUT /notifications/:id.json`
- `POST /reports.json`
- `GET /me.json`
- `GET /users.json`

## Common Payloads

Create a card/task:

```json
{
  "name": "New task",
  "description": "Task description",
  "stage_id": 9,
  "swimlane_id": 1,
  "duedate": "2025-02-22T16:00:00Z",
  "startdate": "2025-02-20T16:00:00Z",
  "planned_time": 120,
  "invitees": ["john@example.com"],
  "tags": ["tag1", "tag2"],
  "custom_fields": [{"name": "Rating", "value": "***"}]
}
```

Move a card/task within a board:

```json
{
  "stage_id": 9,
  "prev_id": 123
}
```

Create a time entry:

```json
{
  "tracked": 120,
  "logged_date": "2025-01-05",
  "desc": "Description here"
}
```

Generate a time-tracking report:

```json
{
  "report_type": "timetracking",
  "start_date": "this_month",
  "end_date": "2025-07-01",
  "projects": [1, 2, 3],
  "users": [9, 8, 7],
  "stages": ["todo", "done"],
  "tags": ["bugs", "features"]
}
```

## Pagination And Filters

- `GET /V2/projects/:project_id/cards.json` returns 100 cards per page. Use `page=2`, `page=3`, and so on.
- Activity and notification endpoints return 50 items per page.
- Card filters include `archived`, `only_archived`, `status`, `hidden`, `done`, `creator_id`, and `assigned_id`, depending on the endpoint.
- Report `start_date` can be `last_month`, `last_week`, `yesterday`, `today`, `this_week`, or `this_month`.

## Output Style

For read results, summarize only the fields that help the user act next:

- Project: `id`, `name`, `workspace_id`, `total_tracked`, users.
- Card/task: `id`, `name`, `stage_id`, `swimlane_id`, dates, `planned_time`, `total_tracked`, users, tags.
- Time entry: `id`, `card_id`, `tracked`, `logged_date`, `user_id`, `desc`.
- User: `id`, `name`, `email` when present.

For mutations, report the endpoint used, IDs affected, fields changed, and whether Breeze returned a useful response body.

## Examples

User: "Move card 123 to Doing in project 9."

Behavior: This is safe without extra confirmation if project 9, card 123, and the destination stage are unambiguous. Use `POST /projects/9/cards/123/move.json` with the destination `stage_id`.

User: "Archive project 9."

Behavior: Elevate before execution because archiving hides active work. Ask for confirmation naming `GET /projects/9/archive.json` and the project name if known.

User: "Log 2 hours on card 123 for today."

Behavior: Creating a time entry is safe without extra confirmation if the card is unambiguous. Use `tracked: 120` and today's `logged_date`.

User: "Change yesterday's time entry from 2h to 3h."

Behavior: Elevate before execution because updating time tracking changes historical work/billing data.
