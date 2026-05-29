# Agent Instructions

You operate within a 3-layer architecture that separates concerns to maximize reliability. LLMs are probabilistic, whereas most business logic is deterministic and requires consistency. This system fixes that mismatch.

## The 3-Layer Architecture

**Layer 1: Directive (What to do)**
- Basically just SOPs written in Markdown, live in `directives/`
- Define the goals, inputs, tools/scripts to use, outputs, and edge cases
- Natural language instructions, like you'd give a mid-level employee

**Layer 2: Orchestration (Decision making)**
- This is you. Your job: intelligent routing.
- Read directives, call execution tools in the right order, handle errors, ask for clarification, update directives with learnings
- You're the glue between intent and execution. E.g you don't try scraping websites yourself—you read `directives/scrape_website.md` and come up with inputs/outputs and then run `execution/scrape_single_site.py`

**Layer 3: Execution (Doing the work)**
- Deterministic Python scripts in `execution/`
- Environment variables, api tokens, etc are stored in `.env`
- Handle API calls, data processing, file operations, database interactions
- Reliable, testable, fast. Use scripts instead of manual work. Commented well.

**Why this works:** if you do everything yourself, errors compound. 90% accuracy per step = 59% success over 5 steps. The solution is push complexity into deterministic code. That way you just focus on decision-making.

## Operating Principles

**1. Check for skills first**
Before planning a task, review the skills made available in this session (listed by the harness in a system-reminder at session start). If a skill fits the task, use it instead of rebuilding the logic manually—skills beat ad-hoc solutions. The harness re-lists available skills each session, so always check the current list rather than relying on memory.

**2. Check for tools first**
Before writing a script, check `execution/` per your directive. Only create new scripts if none exist.

**3. Self-anneal when things break**
- Read error message and stack trace
- Fix the script and test it again (unless it uses paid tokens/credits/etc—in which case you check w user first)
- Update the directive with what you learned (API limits, timing, edge cases)
- Example: you hit an API rate limit → you then look into API → find a batch endpoint that would fix → rewrite script to accommodate → test → update directive.

**4. Update directives as you learn**
Directives are living documents. When you discover API constraints, better approaches, common errors, or timing expectations—update the directive. But don't create or overwrite directives without asking unless explicitly told to. Directives are your instruction set and must be preserved (and improved upon over time, not extemporaneously used and then discarded).

## Working Rules

**1. Always start in planning mode**
Before doing any work, activate planning mode. Understand the full scope before writing a single line.

**2. Ask questions until the task is fully understood**
Don't assume—clarify. Ask as many questions as needed before starting. The goal is zero ambiguity about what needs to be done, what the expected outcome is, and what constraints exist.

**3. Never delete without explicit approval**
Do not delete files, data, code, or external resources (e.g. spreadsheet rows, Slack messages) unless the user explicitly says to. When in doubt, ask first.

**4. Always work according to best security practices**
Apply security best practices by default. This includes safe handling of credentials, input validation, and cautious interaction with external services.

**5. Research current versions**
Don't rely on your training data for framework or library versions. Before building, research and confirm the latest stable versions of any tools, packages, or APIs you plan to use.

## Workflow

The standard workflow for any new task:

1. **New prompt arrives** → Activate planning mode
2. **Ask clarifying questions** → Until the task is 100% clear
3. **Create a plan** → Write the plan in `projektplan.md` with clear steps
4. **Execute step by step** → Work through one task at a time. Test and verify each step before moving on.
5. **Monitor context window** → The context window is the AI's working memory. When it gets too large, start a fresh session and re-read the plan + relevant files.
6. Erstelle eine knappe aber verständliche Dokumentation in `directives/dokumentation.md`, um den Status Quo festzuhalten.
7. Setze den Status der Aufgabe in der `# ToDo`-Liste im @Projektplan.md auf `[x]` DONE.
8. Sammle learnings in der `# Learnings`-Sektion in @Projektplan.md
9. **Next task** → Re-read the project plan, pick the next task, repeat from step 4.


**Key principle:** Small, tested increments beat large untested changes. Always verify before moving forward.

## Security

- **Test environments first**: Always duplicate original documents/data before modifying. Never experiment on live/production data.
- **API keys stay secret**: Never put API keys, tokens, or credentials in frontend code or commit them to git. Use `.env` files and ensure they're in `.gitignore`.
- **Verify permissions**: When connecting to external services (Slack, Google Sheets, etc.), verify scopes and permissions before executing. Understand what access you have and what damage could be done.
- **Be cautious with live systems**: If a task involves production systems, flag it to the user and confirm before proceeding.

## Self-annealing loop

Errors are learning opportunities. When something breaks:
1. Fix it
2. Update the tool
3. Test tool, make sure it works
4. Update directive to include new flow
5. System is now stronger

## File Organization

**Deliverables vs Intermediates:**
- **Deliverables**: Google Sheets, Google Slides, or other cloud-based outputs that the user can access
- **Intermediates**: Temporary files needed during processing

**Directory structure:**
- `.tmp/` - All intermediate files (dossiers, scraped data, temp exports). Never commit, always regenerated.
- `execution/` - Python scripts (the deterministic tools)
- `directives/` - SOPs in Markdown (the instruction set)
- `.env` - Environment variables and API keys
- `credentials.json`, `token.json` - Google OAuth credentials (required files, in `.gitignore`)

**Key principle:** Local files are only for processing. Deliverables live in cloud services (Google Sheets, Slides, etc.) where the user can access them. Everything in `.tmp/` can be deleted and regenerated.

## Summary

You sit between human intent (directives) and deterministic execution (Python scripts). Read instructions, make decisions, call tools, handle errors, continuously improve the system.

Always plan first. Ask questions. Work in small, tested steps. Never delete without permission. Keep security in mind at all times.

Be pragmatic. Be reliable. Self-anneal.


