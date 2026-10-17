---
name: flowmind
description: "Set up Flowmind for a builder's coding agents and log agent work to it with the flowmind CLI. Use when the user asks to set up, connect, or onboard Flowmind; to create or link Flowmind projects; to log or stage work in Flowmind; to check Flowmind progress; when you finish material work in a repository that has a .flowmind.json file; when you start or scaffold a new project or repository; or when you work in a repository that Flowmind reports as not linked. Skip generic time tracking that does not involve Flowmind."
homepage: https://www.flowmind.pro/
metadata: { "openclaw": { "emoji": "⏱️" } }
---

# Flowmind

Flowmind records what a builder ships, including work their agents do. Agents use the `flowmind` CLI. It is also installed as `fm`, but on macOS `fm` can be Apple's Foundation Models tool, so always call `flowmind`.

## Set up Flowmind

Use this when the user says "set up Flowmind", "connect my projects to Flowmind", or similar.

1. Check the CLI with `flowmind --version`. If it is missing or older than 0.3.0, run `npm install -g @flowmind/cli`.
2. Check sign-in with `flowmind auth status --json`. If `authenticated` is false, run `flowmind auth login --no-browser`, give the user the printed URL, and keep the command running while it waits (up to 5 minutes). The user approves in the browser.
3. Install the skill and session hooks with `flowmind hooks install`. Tell the user that Codex runs new hooks only after they trust them once in `/hooks`. Claude Code runs them at its next session.
4. Inventory the user's local work:
   - Find git repositories and their link status with `flowmind link --scan <dir...> --json` over the folders the user works in (for example `~/projects`, `~/code`, `~/src`, and the workspaces your host lists). Read each README or package manifest only far enough to name and describe the project.
   - Read existing Flowmind projects with `flowmind projects --json`.
   - A `remote-linked` repository needs only `git pull`.
5. Propose a table: repository path, Flowmind project name, one-line description, and action (create, link to an existing project, or ignore). Put repositories of one product under one project. Ask the user to approve or edit the list. Create nothing before they approve.
6. After approval, for each row:
   - Create: `flowmind projects add "<name>" --description "<one line>" --json`. The new id is `project.id`.
   - Link: `flowmind link <project-id> --dir <repo> --json`. This writes `.flowmind.json` at the repository root.
   - Ignore: `flowmind link --ignore --dir <repo>`.
   - Ask whether to commit `.flowmind.json`. A committed link reaches the user's other machines and agents.
7. Report what you created and linked, and explain how agent work gets logged from now on.

## New projects and unlinked repositories

Use this when you start or scaffold a new project or repository, or when a session starts in a repository that Flowmind reports as not linked.

One product is one Flowmind project on every machine. The same repository on another machine already shares its link through the committed `.flowmind.json`. Never create a duplicate project.

1. Run `git pull` first. Another machine may have linked the repository already. If Flowmind says the remote links it, the pull is all you need.
2. Look for the existing project with `flowmind projects --json`. Match by product, repository, and name.
3. Create a project with `flowmind projects add "<name>" --description "<one line>" --json` only for a new product. If it refuses because the name exists, link to that project instead.
4. Run `flowmind link <project-id>` and commit `.flowmind.json`.
5. For a third-party clone or a scratch folder, run `flowmind link --ignore` instead, and commit the marker.

Ask the user once when you are unsure which project a repository belongs to, or whether it counts at all. Do this before you finish the task.

To find gaps across the user's folders, run `flowmind link --scan <dir...>`. It lists each repository as `linked`, `ignored`, `remote-linked`, or `unlinked`.

## Log agent work

In a linked repository, the session hooks record how long each agent turn runs. When you finish a material unit of work, such as a feature, fix, investigation, or document, stage it. Do not stage every turn.

```bash
flowmind agent stage --intention "<what you set out to do>" --outcome "<what changed or shipped>" --artifact-url <commit, PR, or document URL> --json
```

- The CLI reads the project from `.flowmind.json` and the time from the session record. A second stage in the same session covers only the time after the first.
- After 20 minutes of unstaged agent time, each prompt in a linked repository carries a reminder with the unstaged minutes. Stage when the current unit of work is complete; a stage with explicit times also resets the count.
- Add `--dry-run` to preview the project and minutes without staging.
- Pass `--project`, `--minutes`, and `--started-at` only when no session record exists. Use observed times only. Never estimate time from tokens or tool calls.
- Write the intention and the outcome as one short sentence each. Never paste transcripts, terminal output, commit messages, issue titles, emails, or secrets.
- Share `proposal.webReviewUrl` with the user. The user approves staged work in Flowmind Activity; **Approve all with links** approves every item with an artifact link at once. Approved agent time counts on the project, not as the user's personal effort or XP.
- If staging fails, report the error. Never fall back to `flowmind log`, and never approve your own proposals.

## Log the user's own work

Use `flowmind log` only when the user asks you to log work that they did themselves:

```bash
flowmind log -p "<project>" -h <hours> -i "<intention>" -o "<outcome>" [--started-at <ISO date-time>] [-a <artifact URL>]
```

Ask only for missing fields. A natural-language entry (`flowmind log "2h on the API"`) needs the user at the terminal to review it.

## Read progress

- `flowmind status` and `flowmind stats`: hours, streak, and level.
- `flowmind momentum --json`: eight weeks of outcomes, personal and agent hours, and project trends.
- `flowmind ship-log <project> --json`: a project's public ship log.
- `flowmind agent proposals --status proposed --json`: staged work that waits for approval.

## Troubleshooting

- `flowmind auth status` shows the API URL. A normal install uses `https://api.flowmind.pro`.
- `flowmind hooks status` shows whether the skill and hooks are installed for Codex and Claude Code.
- `outdated` hooks: run `flowmind hooks install` again (for example after a Node upgrade) and trust the changed hooks.
- "No recorded agent time": the hooks are missing or not trusted, or the session started before installation. Stage with observed `--minutes` and `--started-at`, or stage in the next session.
- "Several agent sessions have a turn in progress": pass `--session-id`.
- "No project given": run `flowmind link <project>` in the repository, or pass `--project`.
- "This repository is ignored for Flowmind": the repository has an ignore marker. Pass `--project` only if the user wants this work staged.
- "A project named ... already exists": link to that project with `flowmind link <id>`. Use `--allow-duplicate` only when the user asks for a second project with the same name.
