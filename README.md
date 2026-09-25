# my-skills

[![skills.sh](https://skills.sh/b/sundaram2021/my-skills)](https://skills.sh/sundaram2021/my-skills)

Agent skills for bug fixing and plain-English technical explanations.

## Install

```bash
npx skills add sundaram2021/my-skills
```

Install a single skill:

```bash
npx skills add sundaram2021/my-skills --skill fix-the-issue
npx skills add sundaram2021/my-skills --skill tech-explainer
```

Works with Claude Code, Cursor, Codex, Copilot, Windsurf, Gemini, OpenCode, and other agents supported by the [skills CLI](https://skills.sh/docs/cli).

## Skills

| Skill | Description |
| --- | --- |
| [`fix-the-issue`](./fix-the-issue/SKILL.md) | Fix bugs from existing GitHub issues or urgent hotfixes, verify changes, and ensure all tests and builds pass. |
| [`tech-explainer`](./tech-explainer/SKILL.md) | Explain complex technical concepts, codebases, bugs, and architectures in simple, beginner-friendly language, tracing the relevant source files. |

## Usage

Once installed, invoke the skill by name in your agent, or generate a one-off prompt without installing:

```bash
npx skills use sundaram2021/my-skills@tech-explainer
```

## License

MIT
