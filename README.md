# skills

A shared collection of reusable Claude Code skills, packaged as a Claude Code plugin.

## Loading the plugin

Add this plugin to your Claude Code configuration:

```json
{
  "plugins": [
    { "type": "local", "path": "/path/to/this/repo" }
  ]
}
```

Or point Claude Code at the repo root directly.

## Adding a new skill

1. Copy `skills/example-skill/` to `skills/your-skill-name/`
2. Edit `SKILL.md` — update the frontmatter (`name`, `description`, `version`) and replace the instructions with your own
3. Add any supporting files your skill needs in the same directory

## Structure

```
skills/
├── .claude-plugin/
│   └── plugin.json       # Plugin manifest
├── skills/
│   └── example-skill/
│       └── SKILL.md      # Example skill template
└── README.md
```
