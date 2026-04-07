# plan-ticket

A reusable Claude Code skill that reads Jira tickets and generates structured implementation plans. Auto-detects the project stack and loads stack-specific exploration rules.

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

```bash
# Option A: symlink (recommended — stays in sync)
mkdir -p /path/to/your-project/.claude/skills
ln -s /path/to/plan-ticket /path/to/your-project/.claude/skills/plan-ticket

# Option B: copy
cp -r /path/to/plan-ticket /path/to/your-project/.claude/skills/plan-ticket
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

In Claude Code, inside your project directory:

```
/plan-ticket MPP-650
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
