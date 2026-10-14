# thought_skills

Agent skills for structured thinking. Each folder is one skill with its own `SKILL.md`.

## Skills

| Skill | What it does | How to run |
|-------|--------------|------------|
| [first-principles](first-principles/SKILL.md) | Analyzes a statement, question, or design from first principles: defines the real goal, keeps only proven facts, reasons or builds from those facts, then has an independent sub-agent judge the work. | `/first-principles <statement>` |

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

## Adding a skill

1. Create a folder named after the skill, with a `SKILL.md` that has `name` and `description` in its frontmatter.
2. Add a row to the table above.

## License

[MIT](LICENSE)
