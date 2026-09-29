# guided-review

A [Claude Code](https://claude.com/claude-code) skill that walks you through a code change like a guided tour: short summary first, then one small snippet at a time, explained in plain words. After each stop you answer with OK, ask for more detail, or request a change - Claude applies it, re-checks it and only moves on when you approve.

At the end it proposes a commit message. It never commits on its own.

## Installation

### Option 1: As a plugin

Inside Claude Code:

```
/plugin marketplace add niels-numbers/guided-review
/plugin install guided-review@guided-review
```

Invoke with `/guided-review:guided-review`.

### Option 2: Copy the skill manually

```bash
mkdir -p ~/.claude/skills/guided-review
curl -o ~/.claude/skills/guided-review/SKILL.md \
  https://raw.githubusercontent.com/niels-numbers/guided-review/main/skills/guided-review/SKILL.md
```

For a single project only, use `.claude/skills/guided-review/` in the project instead.

Invoke with `/guided-review`.

## Usage

```
/guided-review            # uncommitted changes (staged + unstaged)
/guided-review HEAD       # a specific commit
/guided-review main..HEAD # a range
```

The skill has `disable-model-invocation: true`, so it only runs when you call it explicitly.

## License

MIT
