# plan-ticket

A reusable AI coding skill that reads Jira tickets and generates structured implementation plans. Auto-detects the project stack and loads stack-specific exploration rules.

## Supported Stacks

| Stack     | Detection                                            |
| --------- | ---------------------------------------------------- |
| Rails     | `Gemfile` contains `rails`                           |
| Next.js   | `package.json` contains `next`                       |
| PHP Yii2  | `composer.json` contains `yiisoft/yii2`              |
| WordPress | `style.css` with `Theme Name:` or WP `functions.php`|
| Shopify   | `config/settings_schema.json` or Liquid templates    |

## Setup

### 1. Copy or symlink into your project

The setup depends on which AI tool you use:

**Claude Code:**
```bash
mkdir -p /path/to/your-project/.claude/skills
ln -s /path/to/plan-ticket /path/to/your-project/.claude/skills/plan-ticket
```

**Cursor:**
```bash
# Copy SKILL.md content into your .cursorrules or use as a prompt file
cp -r /path/to/plan-ticket /path/to/your-project/.cursor/skills/plan-ticket
```

**Other AI tools:**
```bash
# Copy into your project and reference SKILL.md as a system prompt or custom instruction
cp -r /path/to/plan-ticket /path/to/your-project/.ai/skills/plan-ticket
```

### 2. Ensure Jira CLI is configured

The skill uses the [jira-cli](https://github.com/ankitpokhrel/jira-cli) tool. Make sure it's installed and configured:

```bash
# Config should exist at:
~/.config/.jira/.config.yml

# And the API token env var should be set:
export JIRA_API_TOKEN="your-token"
```

## Usage

**Claude Code** (as a slash command):
```
/plan-ticket MPP-650
```

**Other AI tools** (as a prompt):
```
Follow the instructions in SKILL.md to plan ticket MPP-650
```

The ticket ID must include the project prefix (e.g., `MPP-650`, `KMB-123`).

## What It Does

1. **Reads the Jira ticket** — fetches description, comments, acceptance criteria, and attachments
2. **Detects the stack** — checks project files to determine which stack rules to load
3. **Explores the codebase** — launches parallel agents to investigate relevant code based on the ticket
4. **Generates a plan** — writes a structured plan to `.docs/plans/<ticket-id>/plan.md`
5. **Asks clarifying questions** — flags ambiguities before implementation begins

## What It Does NOT Do

- Does not implement any code
- Does not create git branches
- Does not modify source files

## Structure

```
plan-ticket/
├── SKILL.md          # Main skill definition
├── stacks/
│   ├── rails.md      # Rails exploration + plan rules
│   ├── nextjs.md     # Next.js exploration + plan rules
│   ├── php-yii2.md   # PHP Yii2 exploration + plan rules
│   ├── wordpress.md  # WordPress exploration + plan rules
│   └── shopify.md    # Shopify Liquid exploration + plan rules
```

## Adding a New Stack

1. Create a new file in `stacks/` (e.g., `stacks/laravel.md`)
2. Define three sections: **Explore Agents**, **Stack-Specific Plan Sections**, and **Verification Commands**
3. Add the detection rule to the table in `SKILL.md` (Step 3)

## Optional: Browser Testing

If your project has the `agent-browser` skill installed, the generated plan will include a browser testing section with scenarios that can be executed after implementation.

## Compatibility

### Operating Systems

| OS      | Support | Notes                                                        |
| ------- | ------- | ------------------------------------------------------------ |
| macOS   | Full    | All commands work natively                                   |
| Linux   | Full    | All commands work natively                                   |
| Windows | Partial | Requires Git Bash, WSL, or similar. PowerShell alternatives are noted in SKILL.md where applicable |

### AI Tools

The skill is written as structured markdown instructions, so it can be used with any AI coding tool that supports custom prompts or instructions:

| Tool       | How to use                                                      |
| ---------- | --------------------------------------------------------------- |
| Claude Code| Native skill support — symlink into `.claude/skills/`           |
| Cursor     | Use as a prompt file or include in `.cursorrules`               |
| Windsurf   | Include in `.windsurfrules` or reference as context             |
| Copilot    | Reference as a custom instruction or paste into chat            |
| Other      | Feed `SKILL.md` + the relevant `stacks/*.md` as system context  |

The codebase exploration step uses parallel agents in Claude Code. Other tools should perform the same searches sequentially using their available search capabilities.
