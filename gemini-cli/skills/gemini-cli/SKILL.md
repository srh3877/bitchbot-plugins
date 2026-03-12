---
name: gemini-cli
description: Use Google Gemini CLI to get a second opinion, review code, or cross-check work
when_to_use: When the user asks to "use gemini", "ask gemini", "review with gemini", "get gemini's take", or wants a second AI opinion on code, plans, or decisions
when_not_to_use: When the user wants to use Gemini API programmatically (use claude-api skill instead), or for tasks Claude can handle alone without a second opinion
---

# Gemini CLI Skill

Use the locally installed Gemini CLI (`/opt/homebrew/bin/gemini`) to get a second opinion from Google's Gemini model.

## Core Command Pattern

```bash
gemini -p "<prompt>" --yolo -o text
```

Flags:
- `-p "<prompt>"` -- non-interactive single prompt mode
- `--yolo` -- auto-approve all tool calls (no interactive confirmations)
- `-o text` -- plain text output (no markdown rendering artifacts)

## Variants

### Review a file
```bash
cat <file> | gemini -p "Review this code for <aspect>" --yolo -o text
```

### Review a diff
```bash
git diff | gemini -p "Review these changes for correctness and security" --yolo -o text
```

### Review a plan or document
```bash
cat <file> | gemini -p "Review this plan for <criteria>" --yolo -o text
```

### Use a specific model
```bash
gemini -p "<prompt>" --yolo -o text -m gemini-2.5-pro
```

### Sandbox mode (isolated execution)
```bash
gemini -p "<prompt>" --yolo -o text --sandbox
```

## Workflow

1. **Prepare context** -- gather the relevant code, plan, or content
2. **Pipe or inline** -- use stdin pipe for large content, `-p` for short prompts
3. **Run gemini** -- execute with `--yolo -o text`
4. **Relay results** -- present Gemini's feedback to the user, noting agreements and disagreements with your own analysis
5. **Synthesize** -- combine both perspectives into actionable recommendations

## Built-in Tools

Gemini CLI has its own tool access when running with `--yolo`:
- File read/write/edit
- Shell command execution
- Web search (Google Search grounding)

These activate automatically when relevant to the prompt.

## Error Handling

- If gemini is not found: inform user to install via `npm install -g @anthropic-ai/gemini-cli` or `brew install gemini`
- If auth fails: user needs to run `gemini` interactively once to authenticate
- Timeout: for long-running reviews, use Bash tool with extended timeout (up to 600000ms)

## Security Notes

- Never pipe secrets, API keys, or .env files to gemini
- Content sent to gemini goes to Google's API -- same privacy considerations as any external API call
- Use `--sandbox` flag when reviewing untrusted code that might execute
