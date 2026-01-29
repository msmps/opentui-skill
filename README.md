# OpenTUI Skill for OpenCode

OpenTUI platform reference docs. Covers the Core imperative API, React reconciler, Solid reconciler, layout system, components, keyboard handling, animations, and testing.

## Install

### AI Coding Assistants

Add the skill to your AI coding assistant for richer context:

```bash
npx skills add msmps/opentui-skill
```

This works with Claude Code, Codex, Cursor, Gemini CLI, GitHub Copilot, Goose, OpenCode, and Windsurf.

### OpenCode (inc /command)

Local installation (current project only):

```bash
curl -fsSL https://raw.githubusercontent.com/msmps/opentui-skill/main/install.sh | bash
```

Global installation (available in all projects):

```bash
curl -fsSL https://raw.githubusercontent.com/msmps/opentui-skill/main/install.sh | bash -s -- --global
```

## Usage

Once installed, the skill appears in OpenCode's `<available_skills>` list. The agent loads it automatically when working on OpenTUI tasks.

Use the `/opentui` command to load the skill and get contextual guidance:

```
/opentui create a React TUI with keyboard navigation
```

### Updating

To update to the latest version:

```
/opentui --update-skill
```

## Structure

The installer adds both a skill and a command:

```
# Skill (reference docs)
skill/opentui/
├── SKILL.md              # Main manifest + decision trees
└── references/           # Framework and concept subdirectories
    ├── core/             # Imperative API (5-file pattern)
    ├── react/            # React reconciler (5-file pattern)
    ├── solid/            # Solid reconciler (5-file pattern)
    ├── components/       # Component categories
    ├── layout/           # Yoga/Flexbox layout
    ├── keyboard/         # Input handling
    ├── animation/        # Timeline animations
    └── testing/          # Test renderer + snapshots

# Command (slash command)
command/opentui.md        # /opentui entrypoint
```

### Decision Trees

The main `SKILL.md` contains decision trees for:
- Choosing a framework (Core, React, Solid)
- Displaying content (text, containers, code, diffs)
- Handling user input (inputs, keyboard, focus)
- Layout and positioning (flexbox, absolute, responsive)
- Animations (timeline, easing, transitions)
- Testing (snapshots, interaction, debugging)
- Troubleshooting (gotchas, common issues)

## Topics Covered

**Frameworks**: Core (imperative), React (declarative), Solid (fine-grained reactivity)

**Components**: text, box, scrollbox, input, textarea, select, tab-select, ascii-font, code, line-number, diff

**Cross-cutting**: Layout (Yoga/Flexbox), Keyboard handling, Animations, Testing

## Credits & Inspiration

This skill's structure and patterns are heavily inspired by:

- **[cloudflare-skill](https://github.com/dmmulroy/cloudflare-skill)** by [Dillon Mulroy](https://github.com/dmmulroy) — an excellent example of a platform skill, demonstrating decision trees, progressive disclosure, and the multi-file reference pattern.

- **[Anatomy of an Excellent OpenCode Skill](https://jpcaparas.medium.com/anatomy-of-an-excellent-opencode-skill-lessons-from-cloudflare-skill-0f853babc471)** by [JP Caparas](https://medium.com/@jpcaparas) — an insightful breakdown of what makes cloudflare-skill effective, covering context management, troubleshooting indexes, and intent-based routing.

## License

MIT - see [LICENSE](LICENSE)
