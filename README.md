# Flowmind skill and Claude Code plugin

[Flowmind](https://www.flowmind.pro/) is a work log for solo builders who ship with AI coding agents. This repository holds the Flowmind agent skill.

With the skill, your coding agent can set up Flowmind, link your repositories to Flowmind projects, and stage each finished piece of work with a one-line outcome and the link that proves it. Nothing enters your record until you approve it in Flowmind.

## Install

### Claude Code plugin

```sh
claude plugin marketplace add afgonullu/flowmind-skill
claude plugin install flowmind@flowmind
```

In a Claude Code session, `/plugin marketplace add afgonullu/flowmind-skill` does the same.

### Any agent that reads skills

```sh
npx skills add afgonullu/flowmind-skill
```

### Record agent time in Codex and Claude Code

The skill works with the Flowmind CLI. The CLI also installs session hooks that record how long each agent turn runs:

```sh
npm install -g @flowmind/cli
flowmind auth login
flowmind hooks install
```

`flowmind hooks install` copies the same skill to Codex (`~/.agents/skills`). In Claude Code, from `@flowmind/cli` 0.3.2, it detects this plugin when it is installed and enabled, and leaves the skill to it, so the skill loads once. It still adds the hooks. Codex runs new hooks only after you trust them once in `/hooks`.

### OpenClaw

Import the `plugins/flowmind/skills/flowmind` folder into your OpenClaw skills. OpenClaw has no hooks, so your agent stages each session with the start time and minutes it observed.

## Start

Ask your agent: "Set up Flowmind." The skill has your agent find your repositories, propose a list of Flowmind projects, and create and link them only after you approve the list.

## Links

- [How Flowmind works with Codex and Claude Code](https://www.flowmind.pro/agents)
- [Frequently asked questions](https://www.flowmind.pro/faq)
- [@flowmind/cli on npm](https://www.npmjs.com/package/@flowmind/cli)
- [Privacy policy](https://www.flowmind.pro/privacy)

## Versions

The Flowmind product repository is the source of this skill. It is published here with each `@flowmind/cli` release, and the plugin version matches the CLI version.

## License

MIT. See [LICENSE](LICENSE).
