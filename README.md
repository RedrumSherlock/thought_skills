# thought_skills

Agent skills for structured thinking. Each folder is one skill with its own `SKILL.md`.

## Skills

| Skill | What it does | How to run |
|-------|--------------|------------|
| [first-principles](first-principles/SKILL.md) | Analyzes a statement, question, or design from first principles: defines the real goal, keeps only proven facts, reasons or builds from those facts, then has an independent sub-agent judge the work. | `/first-principles <statement>` |
| [ml-scientist](ml-scientist/SKILL.md) | Runs ML and deep-learning work as research. It observes the data, reasons, states a hypothesis, tests it with training and validation, explains the result, and repeats. When progress stops, it searches for the best published methods for the same problem. A simple model is only a baseline. An independent judge checks the work. | Loads on its own during ML work, or `/ml-scientist <task>` |
| [tldr](tldr/SKILL.md) | Rewrites the previous output, or the given text, as a short plain-English summary: a tl;dr first, then bulleted sections of one-to-two-line points, no made-up terms. Important process details move to an appendix. Use when the last summary is too long. | `/tldr [text]` |

## Install

### Option 1: skills CLI (Claude Code, Cursor, Codex, and other agents)

```bash
# Pick skills and agents interactively
npx skills add RedrumSherlock/thought_skills

# Install one skill for all your projects
npx skills add RedrumSherlock/thought_skills --skill first-principles -g

# Install every skill in this repo for all your projects
npx skills add RedrumSherlock/thought_skills --skill '*' -g
```

Update later with `npx skills update`.

### Option 2: clone and link (Claude Code)

```bash
git clone https://github.com/RedrumSherlock/thought_skills.git ~/thought_skills
mkdir -p ~/.claude/skills
for d in ~/thought_skills/*/; do
  [ -f "$d/SKILL.md" ] && ln -sfn "${d%/}" ~/.claude/skills/"$(basename "$d")"
done
```

The links point at the clone, so `git -C ~/thought_skills pull` updates every skill. Run the loop again after pulling to link any new skills.

To install for one project only, link into `<project>/.claude/skills/` instead of `~/.claude/skills/`.

### Auto-loading ml-scientist

`ml-scientist` loads on its own when a task trains or improves an ML model. Claude decides from the skill's `description` and `when_to_use` fields. To make it load every time, add this line to `~/.claude/CLAUDE.md` or to a project's `CLAUDE.md`:

```
When a task trains, tunes, or evaluates a machine-learning or deep-learning model, use the ml-scientist skill.
```

## Adding a skill

1. Create a folder named after the skill, with a `SKILL.md` that has `name` and `description` in its frontmatter.
2. Add a row to the table above.

## License

[MIT](LICENSE)
