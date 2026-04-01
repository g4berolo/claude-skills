# Claude Skills

A collection of agent skills for [Claude Code](https://claude.ai/code) and other skills-compatible agents.

Install with:

```bash
npx skills add g4berolo/claude-skills
```

## Skills

### buddy-reset

Reset your Claude Code coding buddy/companion so you can hatch a new one.

**What it does:**

- Reads the current season seed (`fk_`) from `cli.js`
- Lets you choose a new seed (random or custom)
- Replaces the seed and removes companion data from `~/.claude.json`
- After restarting Claude Code, run `/buddy` to hatch a new companion

**Triggers:** `buddy reset`, `reroll buddy`, `new buddy`, `change buddy`, `reset companion`

## License

MIT
