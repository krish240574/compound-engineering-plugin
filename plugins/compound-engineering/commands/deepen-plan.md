---
name: deepen-plan
description: Enhance a plan with parallel research agents for each section to add depth, best practices, and implementation details
argument-hint: "[path to plan file]"
---

# Deepen Plan - Power Enhancement Mode

## Introduction

**Note: The current year is 2025.** Use this when searching for recent documentation and best practices.

This command takes an existing plan (from `/workflows:plan`) and enhances each section with parallel research agents. Each major element gets its own dedicated research sub-agent to find:
- Best practices and industry patterns
- Performance optimizations
- UI/UX improvements (if applicable)
- Quality enhancements and edge cases
- Real-world implementation examples

The result is a deeply grounded, production-ready plan with concrete implementation details.

## Plan File

<plan_path> #$ARGUMENTS </plan_path>

**If the plan path above is empty:**
1. Check for recent plans: `ls -la plans/`
2. Ask the user: "Which plan would you like to deepen? Please provide the path (e.g., `plans/my-feature.md`)."

Do not proceed until you have a valid plan file path.

## Main Tasks

### 1. Parse and Analyze Plan Structure

<thinking>
First, read and parse the plan to identify each major section that can be enhanced with research.
</thinking>

**Read the plan file and extract:**
- [ ] Overview/Problem Statement
- [ ] Proposed Solution sections
- [ ] Technical Approach/Architecture
- [ ] Implementation phases/steps
- [ ] Code examples and file references
- [ ] Acceptance criteria
- [ ] Any UI/UX components mentioned
- [ ] Technologies/frameworks mentioned (Rails, React, Python, TypeScript, etc.)
- [ ] Domain areas (data models, APIs, UI, security, performance, etc.)

**Create a section manifest:**
```
Section 1: [Title] - [Brief description of what to research]
Section 2: [Title] - [Brief description of what to research]
...
```

### 2. Discover and Apply Available Skills

<thinking>
Dynamically discover all available skills and match them to plan sections. Don't assume what skills exist - discover them at runtime.
</thinking>

**Step 1: Discover ALL available skills from ALL sources**

```bash
# 1. Project-local skills (highest priority - project-specific)
ls .claude/skills/

# 2. User's global skills (~/.claude/)
ls ~/.claude/skills/

# 3. compound-engineering plugin skills
ls ~/.claude/plugins/cache/*/compound-engineering/*/skills/

# 4. ALL other installed plugins - check every plugin for skills
find ~/.claude/plugins/cache -type d -name "skills" 2>/dev/null

# 5. Also check installed_plugins.json for all plugin locations
cat ~/.claude/plugins/installed_plugins.json
```

**Important:** Check EVERY source. Don't assume compound-engineering is the only plugin. Use skills from ANY installed plugin that's relevant.

**Step 2: For each discovered skill, read its SKILL.md to understand what it does**

```bash
# For each skill directory found, read its documentation
cat [skill-path]/SKILL.md
```

**Step 3: Match skills to plan content**

For each skill discovered:
- Read its SKILL.md description
- Check if any plan sections match the skill's domain
- If there's a match, spawn a sub-agent to apply that skill's knowledge

**Step 4: Launch sub-agents for matched skills**

For each matched skill, launch a parallel Task:
```
Task general-purpose: "You have access to the [skill-name] skill. Apply its patterns and best practices to analyze [plan section]. Provide:
- Skill-specific recommendations
- Patterns from the skill's reference documentation
- Anti-patterns the skill warns against
- Concrete code examples following the skill's conventions"
```

**Run as many skill-based sub-agents as you find matches for. No limit.**

### 3. Launch Per-Section Research Agents

<thinking>
For each major section in the plan, spawn dedicated sub-agents to research improvements. Use the Explore agent type for open-ended research.
</thinking>

**For each identified section, launch parallel research:**

```
Task Explore: "Research best practices, patterns, and real-world examples for: [section topic].
Find:
- Industry standards and conventions
- Performance considerations
- Common pitfalls and how to avoid them
- Documentation and tutorials
Return concrete, actionable recommendations."
```

**Also use Context7 MCP for framework documentation:**

For any technologies/frameworks mentioned in the plan, query Context7:
```
mcp__plugin_compound-engineering_context7__resolve-library-id: Find library ID for [framework]
mcp__plugin_compound-engineering_context7__query-docs: Query documentation for specific patterns
```

**Use WebSearch for current best practices:**

Search for recent (2024-2025) articles, blog posts, and documentation on topics in the plan.

### 4. Discover and Run ALL Review Agents

<thinking>
Dynamically discover every available agent and run them ALL against the plan. Don't filter, don't skip, don't assume relevance. 40+ parallel agents is fine. Use everything available.
</thinking>

**Step 1: Discover ALL available agents from ALL sources**

```bash
# 1. Project-local agents (highest priority - project-specific)
find .claude/agents -name "*.md" 2>/dev/null

# 2. User's global agents (~/.claude/)
find ~/.claude/agents -name "*.md" 2>/dev/null

# 3. compound-engineering plugin agents (all subdirectories)
find ~/.claude/plugins/cache/*/compound-engineering/*/agents -name "*.md" 2>/dev/null

# 4. ALL other installed plugins - check every plugin for agents
find ~/.claude/plugins/cache -path "*/agents/*.md" 2>/dev/null

# 5. Check installed_plugins.json to find all plugin locations
cat ~/.claude/plugins/installed_plugins.json

# 6. For local plugins (isLocal: true), check their source directories
# Parse installed_plugins.json and find local plugin paths
```

**Important:** Check EVERY source. Include agents from:
- Project `.claude/agents/`
- User's `~/.claude/agents/`
- compound-engineering plugin
- ALL other installed plugins (agent-sdk-dev, frontend-design, etc.)
- Any local plugins

**Step 2: For each discovered agent, read its description**

Read the first few lines of each agent file to understand what it reviews/analyzes.

**Step 3: Launch ALL agents in parallel**

For EVERY agent discovered, launch a Task in parallel:

```
Task [agent-name]: "Review this plan using your expertise. Apply all your checks and patterns. Plan content: [full plan content]"
```

**CRITICAL RULES:**
- Do NOT filter agents by "relevance" - run them ALL
- Do NOT skip agents because they "might not apply" - let them decide
- Launch ALL agents in a SINGLE message with multiple Task tool calls
- 20, 30, 40 parallel agents is fine - use everything
- Each agent may catch something others miss
- The goal is MAXIMUM coverage, not efficiency

**Step 4: Also discover and run research agents**

Research agents (like `best-practices-researcher`, `framework-docs-researcher`, `git-history-analyzer`, `repo-research-analyst`) should also be run for relevant plan sections.

### 5. Collect and Synthesize Research

<thinking>
Wait for all parallel agents to complete, then synthesize their findings into actionable enhancements for each section.
</thinking>

**For each agent's findings:**
- [ ] Extract concrete recommendations
- [ ] Note specific code patterns or examples
- [ ] Identify performance metrics or benchmarks
- [ ] List relevant documentation links
- [ ] Capture edge cases discovered

### 4. Enhance Plan Sections

<thinking>
Merge research findings back into the plan, adding depth without changing the original structure.
</thinking>

**Enhancement format for each section:**

```markdown
## [Original Section Title]

[Original content preserved]

### Research Insights

**Best Practices:**
- [Concrete recommendation 1]
- [Concrete recommendation 2]

**Performance Considerations:**
- [Optimization opportunity]
- [Benchmark or metric to target]

**Implementation Details:**
```[language]
// Concrete code example from research
```

**Edge Cases:**
- [Edge case 1 and how to handle]
- [Edge case 2 and how to handle]

**References:**
- [Documentation URL 1]
- [Documentation URL 2]
```

### 5. Add Enhancement Summary

At the top of the plan, add a summary section:

```markdown
## Enhancement Summary

**Deepened on:** [Date]
**Sections enhanced:** [Count]
**Research agents used:** [List]

### Key Improvements
1. [Major improvement 1]
2. [Major improvement 2]
3. [Major improvement 3]

### New Considerations Discovered
- [Important finding 1]
- [Important finding 2]
```

### 6. Update Plan File

**Write the enhanced plan:**
- Preserve original filename
- Add `-deepened` suffix if user prefers a new file
- Update any timestamps or metadata

## Output Format

Update the plan file in place (or create `plans/<original-name>-deepened.md` if requested).

## Quality Checks

Before finalizing:
- [ ] All original content preserved
- [ ] Research insights clearly marked and attributed
- [ ] Code examples are syntactically correct
- [ ] Links are valid and relevant
- [ ] No contradictions between sections
- [ ] Enhancement summary accurately reflects changes

## Post-Enhancement Options

After writing the enhanced plan, use the **AskUserQuestion tool** to present these options:

**Question:** "Plan deepened at `[plan_path]`. What would you like to do next?"

**Options:**
1. **View diff** - Show what was added/changed
2. **Run `/plan_review`** - Get feedback from reviewers on enhanced plan
3. **Start `/workflows:work`** - Begin implementing this enhanced plan
4. **Deepen further** - Run another round of research on specific sections
5. **Revert** - Restore original plan (if backup exists)

Based on selection:
- **View diff** → Run `git diff [plan_path]` or show before/after
- **`/plan_review`** → Call the /plan_review command with the plan file path
- **`/workflows:work`** → Call the /workflows:work command with the plan file path
- **Deepen further** → Ask which sections need more research, then re-run those agents
- **Revert** → Restore from git or backup

## Example Enhancement

**Before (from /workflows:plan):**
```markdown
## Technical Approach

Use React Query for data fetching with optimistic updates.
```

**After (from /workflows:deepen-plan):**
```markdown
## Technical Approach

Use React Query for data fetching with optimistic updates.

### Research Insights

**Best Practices:**
- Configure `staleTime` and `cacheTime` based on data freshness requirements
- Use `queryKey` factories for consistent cache invalidation
- Implement error boundaries around query-dependent components

**Performance Considerations:**
- Enable `refetchOnWindowFocus: false` for stable data to reduce unnecessary requests
- Use `select` option to transform and memoize data at query level
- Consider `placeholderData` for instant perceived loading

**Implementation Details:**
```typescript
// Recommended query configuration
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 5 * 60 * 1000, // 5 minutes
      retry: 2,
      refetchOnWindowFocus: false,
    },
  },
});
```

**Edge Cases:**
- Handle race conditions with `cancelQueries` on component unmount
- Implement retry logic for transient network failures
- Consider offline support with `persistQueryClient`

**References:**
- https://tanstack.com/query/latest/docs/react/guides/optimistic-updates
- https://tkdodo.eu/blog/practical-react-query
```

NEVER CODE! Just research and enhance the plan.
