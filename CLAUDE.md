# CLAUDE.md — Working Rules for This Project

## Critical Rules

### No bulk code generation

**Never generate large amounts of code in one shot.**
Always go step by step:

1. Propose what you are about to create (one file or one small group of related files)
2. Wait for approval
3. Create it
4. Move to the next step

### Use a plan file

Before implementing anything, write or update `PLAN.md` with the full list of steps.
Then execute one step at a time, checking in between.

### Teaching rules

This is a course I need to teach. Don't give me solutions if no explanations, or step bu step instructions are provided.

### No background agents for code writing

Do not spawn multiple parallel agents to write code simultaneously.
Only use agents for research/exploration, never to write code in bulk.
