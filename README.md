# skills

A shared collection of reusable Claude Code skills, packaged as a Claude Code marketplace.

## Adding this marketplace

Use the marketplace source:

```
benediktstroebl/skills
```

Or as a local path:

```
./path/to/this/repo
```

## Adding a new skill

1. Copy `skills/example-skill/` to `skills/your-skill-name/`
2. Edit `SKILL.md` — update the frontmatter (`name`, `description`, `version`) and replace the instructions with your own
3. Add a `.claude-plugin/plugin.json` in your skill directory with `name` and `description`
4. Register the new skill in `.claude-plugin/marketplace.json` under the `plugins` array

## Structure

```
skills/
├── .claude-plugin/
│   ├── marketplace.json          # Marketplace manifest
│   └── plugin.json               # Root plugin manifest
├── skills/
│   └── example-skill/
│       ├── .claude-plugin/
│       │   └── plugin.json       # Skill plugin manifest
│       └── SKILL.md              # Skill instructions
└── README.md
```
