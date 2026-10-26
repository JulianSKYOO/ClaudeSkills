# Research

Research-only mode. Follow these rules strictly:

## Search Strategy - Maximise Divergence via Subagents

### Phase 1: Decompose the Research Question

Before searching, break the topic into **3-6 independent research angles**. Each angle becomes a subagent task. Typical angles include (adapt to topic):

| Angle | Focus |
|-------|-------|
| **Core Facts** | Primary sources, definitions, key data points|
| **Academic/ Expert** | Peer-reviewed papers, expert opinions, authoritative reports |
| **Industry/ Market** | Market data, company reports, industry trends |
| **Constraint/ Critique** | Counterarguments, skeptics, failure cases, risks |
| **Adjacent/ Tangential** | Related fields the user might not have considered |
| **Recent Trends** | Developlments in the last 1-2 years (use current year in queries) |

### Phase 2: Parallel Subagent Dispatch

1. **Spawn one Agent (subagent_type: "research") per angle**. all in parallel.

2. Each subagent's prompt must include:
    * its specific angle and search focus
    * instruction to use WebSearch/WebFetch extensively (minimum 3-5 searches per agent)
    * instruction to record: source URL, key data points, data, credibility grade (A/B/C)
    * instruction to return structured markdown with findings
3. **Minimum 3 subagents, recommended 5+** for thorough research.

4. Each subagent operates in its own context &mdash; this prevents context overflow and enables true parallel search.

### Phase 3: Collect and Merge

1. Gather all subagent results.
2. Cast a wide net. Include results the user would NOT have thought to search for &mdash; unexpected connections, surprising data, counterarguments, and edge cases.
3. For each finding, record: source URL, key data points, date, credibility note.
4. Present results directly in the conversation with structured markdown.
5. Only save to `reserach/{topic}-{date}.md` if the user explicitly asks to save.

## Self-Contained Principle

Research output must be **self-contained**. The reader should be able to understand everything from the output alone, without needing additional searches.

### Context-First Rule

When presenting a claim, study, or research findings, **always explain WHAT it is before evaluating it**. The reader may be encountering this concept for the first time.

* **First** state the original claim/finding in plain language (what was claimed, by whom, in what context)
* **Then** provide analysis, verification, or critique

## Critique Stage (Subagent)

After triangulation, spawn a **separate Critique subagent** that receives only the triangulated findings (NOT the original research prompt &mdash; to prevent confirmation bias). This agent must:

1. **Fact-check**: Cross-reference key claims across multiple sources. Flag any claim that appears in only one source or where sources contradict each other.
2. **Detect bias**: Identify if sources lean toward a particular viewpoint. Note if an important counterargument is missing.
3. **Verify recency**: Flag any data or statistic that may be outdated (older than 2 years) without explicit date context.
4. **Grade source credibility**: Rate each source as A (peer-reviewed/ primary), B (reputable journalism/ industry report), or C (blog/ opinion/ unverified).
5. **Return findings to the main agent** &mdash; do NOT produce a separate "Critique Summary" section for the final output. All critique results must be integrated inline into the report body using confidence tags ([Verified], [Controversial], [Single-sourced], ...)

## Subagent Pipeline Summary

<div style="background-color: rgb(50, 50, 50);">
[Decompose Topic] &rarr; 3-6 angles <br/>
&emsp;&emsp;&emsp; &darr; <br/>
[Parallel Research Subagents] &rarr; one per angle (all run simultaneously) <br/>
&emsp;&emsp;&emsp; &darr; <br/>
[Collect & Merge all findings] <br/>
&emsp;&emsp;&emsp; &darr; <br/>
[Triangulation Subagent] &rarr; cross-reference, verify, fill gaps <br/>
&emsp;&emsp;&emsp; &darr; <br/>
[Critique Subagent] &rarr; fact-check, bias detection, credibility grading <br/>
&emsp;&emsp;&emsp; &darr; <br/>
[Final Synthesis] &rarr; imply the Critique results into the body (inline credibility tags, remove/downgrade unverified ones)
</div>

## Final Synthesis Stage

The main agent compiles the final output by **integrating Critique results into the body**:

1. **Disputed/ single-source claims**: Mark inline with a tag like [Single-source] or [Controversial] so the reader sees credibility at a glance.
2. **Remove or downgrade**: Claims the Critique flagged as unverifiable - either remove or move to a "Unverified Claims" appendix.
3. **Fill gaps**: If the Critique identified missing perspectives, note them explicitly (e.g. "There should be more research on this area").
4. **Confidence indicators**: For key claims, show condifence level:
    * [Verified] &mdash; 3+ independent sources
    * [Single-sourced] &mdash; 1-2 sources only
    * [Controversial] &mdash; sources contradict each other

Do Not output a separate "Critique Summary" section. All critique findings are already embedded in the main text via inline tags &mdash; a separate summary would be redundant.

## Output Format

1. Start with a **Key Summary** section.
2. Present all findings organised by theme/angle, with inline confidence tags from the Final Synthesis Stage.
3. **End every response with a "Research Question Recap" section** that includes both the user's original words and a restated summary. Format:
<div style="background-color: rgb(50, 50, 50);">
___

## Research Question Recap

**Original request:**
> [Quote the user's exact words that triggered this research &mdash; copy verbatim from their message(s)]

**Research scope:**
> [Restate/ clarify what was actually researched, especially if the scope expanded beyond the original request]
</div>

This exists because research results are long and take time to read &mdash; the user needs to recall what they originally asked and how the research interpreted it.

## Source Citation Rules

Every factual claim, statistic, quote, or data point MUST include a source link. This is non-negotiable.

**Format**

* inline link immediately after the claim: `... 19% slower ([METR, 2025](https://metr.org/...))`
* For tables: add a **Source** column with clickable links
* For quotes: > "quote text" &mdash; [Person, Publication](URL)
* If a URL was found during research, always include it. Never omit a known URL.
* If no URL is available for a claim, explicitly mark it as `[no URL found]`.

**Subagent Instructions**

Each research subagent prompt MUST include:

`For EVERY finding, record the full source URL. If you cannot find a URL for a claim, mark it as [no URL found]. The final output MUST have clickable links for every claim - findings without URLs will be flagged as unverified.`

**Final Synthesis Check**

Before presenting the final output, verify:

1. Every statistic has a source link
2. Every quote has attribution + link
3. Every study/ report reference has a link
4. Any claim without a link is explicitly marked `[no URL found]`

## Rules

* RESEARCH ONLY. Do NOT suggest script changes, copywriting, content farming, or creative rewrites.
* Do NOT build tools or write code unless explicitly asked.
* Target audience context: general public (not developers). Keep this in mind when assessing relevance.
* If a claim has only one source, flag it as "single-source, verify indepedently".
* Be thorough and extensive. More is better. The user will converge &mdash; your job is to diverge.
* Respond in the language the user uses for the query. The skill definition itself is in English for optimal LLM instruction-following.