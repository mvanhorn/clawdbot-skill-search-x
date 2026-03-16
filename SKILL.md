---
name: search-x
version: "3.0.0"
description: "Search X/Twitter in real-time with Grok-4 and Grok-4.20 - Responses API with x_search tool orchestration, multi-agent research, keyword/semantic/user/thread search modes, image search, prompt caching, and batch API. Full citations with engagement stats."
author: mvanhorn
license: MIT
repository: https://github.com/mvanhorn/clawdbot-skill-search-x
homepage: https://docs.x.ai
triggers:
  - search x
  - search twitter
  - find tweets
  - what's on x about
  - x search
  - twitter search
  - what's trending on x
  - read thread
  - who is on x
  - x profile
  - search tweets
  - trending on twitter
  - show me tweets
  - x images
  - research on x
  - batch search x
metadata:
  openclaw:
    emoji: "🔍"
    requires:
      env:
        - XAI_API_KEY
    primaryEnv: XAI_API_KEY
    tags:
      - x
      - twitter
      - search
      - grok
      - real-time
      - threads
      - trends
      - profiles
      - images
      - citations
      - social-media
      - tweets
      - engagement
      - responses-api
      - multi-agent
      - batch
      - prompt-caching
---

# Search X

Real-time X/Twitter search powered by xAI's Responses API and the `x_search` tool. Returns actual tweets with citations, engagement context, images, and direct links. No scraping, no cookies, no browser automation - just the xAI API.

## Setup

Set your xAI API key:

```bash
openclaw config set skills.entries.search-x.apiKey "xai-YOUR-KEY"
```

Or use environment variable:
```bash
export XAI_API_KEY="xai-YOUR-KEY"
```

Get your API key at: https://console.x.ai

---

## Responses API with x_search (RECOMMENDED)

The primary way this skill searches X is through the Responses API (`POST /v1/responses`) with the `x_search` tool. This is the recommended approach because xAI handles all search orchestration server-side.

### How it works

```
POST https://api.x.ai/v1/responses
Authorization: Bearer $XAI_API_KEY
Content-Type: application/json

{
  "model": "grok-4-1-fast-non-reasoning",
  "tools": [{"type": "x_search"}],
  "input": "Find recent tweets about Claude Code tips"
}
```

Key advantages over manual chat/completions + search:
- **Server-side orchestration** - Grok decides when and how to search X, no client-side tool loop needed
- **Automatic citations** - every result includes source URLs pointing to actual tweets
- **Combined tools** - you can pass `x_search`, `web_search`, and `code_interpreter` in the same request and Grok will use whichever tools are appropriate
- **Structured results** - citations come back as structured objects with URLs, not inline text

### Combining tools in one request

```json
{
  "model": "grok-4-1-fast-non-reasoning",
  "tools": [
    {"type": "x_search"},
    {"type": "web_search"}
  ],
  "input": "What are people saying about the new React compiler on X, and what does the official documentation say?"
}
```

Grok will search X for social discussion and the web for official docs, then synthesize both into a single response with citations from each source.

---

## Models

| Model | Context | Price (in/out per 1M tokens) | Best For |
|-------|---------|------------------------------|----------|
| grok-4.20-multi-agent-beta-0309 | 2M | $2 / $6 | Complex multi-step research |
| grok-4.20-beta-0309-reasoning | 2M | $2 / $6 | Deep analysis with reasoning |
| grok-4-1-fast-reasoning | 2M | $0.20 / $0.50 | Fast search with reasoning |
| grok-4-1-fast-non-reasoning | 2M | $0.20 / $0.50 | Quick searches (cheapest) |

### Grok 4.20 Multi-Agent Beta

The `grok-4.20-multi-agent-beta-0309` model uses multi-agent collaboration for complex research queries. Multiple Grok agents work together to break down the query, search from different angles, and synthesize findings. Use this when:

- The query requires cross-referencing multiple topics or accounts
- You need comprehensive research rather than a quick lookup
- The user asks for analysis, comparison, or trend synthesis across many data points
- Time and cost are less important than thoroughness

```bash
node {baseDir}/scripts/search.js --model grok-4.20-multi-agent-beta-0309 "comprehensive analysis of AI safety discourse evolution"
```

### Model selection guidance

- **Default (quick searches):** `grok-4-1-fast-non-reasoning` - cheapest, fastest, good for simple tweet lookups
- **Reasoning needed:** `grok-4-1-fast-reasoning` - same price, adds chain-of-thought for better analysis
- **Image search:** `grok-4.20-beta-0309-reasoning` - returns image results alongside tweets
- **Deep research:** `grok-4.20-multi-agent-beta-0309` - multi-agent for complex queries

---

## Search Modes via x_search

The `x_search` tool supports four distinct search modes. Grok selects the appropriate mode automatically based on the query, but you can guide it with your prompt.

### Keyword Search

Exact term matching. Best for finding tweets with specific phrases, hashtags, or terms.

```bash
node {baseDir}/scripts/search.js "Claude Code monorepo tips"
```

Grok searches for tweets containing the exact terms. Use quotes in the query for exact phrase matching.

### Semantic Search

Meaning-based search. Best for finding tweets about a concept even when they don't use the exact words.

```bash
node {baseDir}/scripts/search.js "people frustrated with slow CI/CD pipelines"
```

Grok understands the intent and finds relevant tweets even if they use different terminology (e.g., "build times are killing me", "GitHub Actions taking forever").

### User Search

Find accounts and their activity. Best for profile lookups and account discovery.

```bash
node {baseDir}/scripts/search.js --handles karpathy --days 30 "posts from @karpathy about transformers"
```

Returns account information, recent posts, and engagement patterns. Combine with topic filters to see what someone says about a specific subject.

### Thread Fetch

Complete conversation threads. Best for reading full reply chains and discussions.

```bash
node {baseDir}/scripts/search.js --handles sama --days 7 "thread about AI regulation"
```

Grok reconstructs the full thread including replies, quote tweets, and the complete conversation context.

---

## Commands

### Basic Search

```bash
node {baseDir}/scripts/search.js "AI video editing"
```

Searches X for the query and returns tweets with usernames, content, dates, and direct links.

### Filter by Time

```bash
node {baseDir}/scripts/search.js --days 7 "breaking news"
node {baseDir}/scripts/search.js --days 1 "trending today"
node {baseDir}/scripts/search.js --days 90 "product launch retrospective"
```

The `--days` flag sets how far back to search. Default is 30 days. Use `--days 1` for "what happened today" queries, `--days 7` for weekly roundups, and `--days 90` for longer research.

### Filter by Handles

```bash
node {baseDir}/scripts/search.js --handles @elonmusk,@OpenAI "AI announcements"
node {baseDir}/scripts/search.js --exclude @bots "real discussions"
```

Use `--handles` to restrict results to specific accounts. Use `--exclude` to filter out noise. Both accept comma-separated lists with or without the @ prefix.

### Output Options

```bash
node {baseDir}/scripts/search.js --json "topic"        # Full JSON response with all metadata
node {baseDir}/scripts/search.js --compact "topic"     # Just tweets, no commentary or citations list
node {baseDir}/scripts/search.js --links-only "topic"  # Just X links, one per line
```

### Model Selection

```bash
node {baseDir}/scripts/search.js --model grok-4.20-multi-agent-beta-0309 "complex research query"
node {baseDir}/scripts/search.js --model grok-4-1-fast-non-reasoning "quick text search"
node {baseDir}/scripts/search.js --model grok-4.20-beta-0309-reasoning "topic with images"
```

---

## Modes

This skill supports six distinct modes based on what the user is asking for. The agent should pick the right mode based on the query.

### Mode 1: Basic Tweet Search

**When to use:** User asks to "search X", "find tweets about", or "what are people saying about".

**How to run:**
```bash
node {baseDir}/scripts/search.js "query here"
```

**Example queries:**
- "Search X for Claude Code tips"
- "Find tweets about the new iPhone"
- "What are people saying about Rust on X?"

### Mode 2: Thread Reader

**When to use:** User asks to "read this thread", "expand this thread", or provides an X/Twitter URL and wants the full conversation.

**How to run:**
```bash
node {baseDir}/scripts/search.js "thread by @username about [topic] site:x.com"
```

For thread reading, construct a query that includes the author's handle and the thread topic. Grok's x_search will find the thread and return the full reply chain. If the user provides a direct tweet URL, extract the username and topic from the URL and search for it.

**Example queries:**
- "Read this thread: https://x.com/karpathy/status/1234567890"
  - Extract: search for "thread by @karpathy" with --handles karpathy --days 7
- "Expand the thread from @levelsio about startups"
  - Search: --handles levelsio "startups thread"

**Tips for thread reading:**
- Use `--handles` to lock onto the thread author
- Use `--days 3` or `--days 7` to narrow the time window
- Ask Grok to "include all replies in the thread" in the query
- If a thread is very long, Grok may summarize - run again with --compact for denser output

### Mode 3: Trend Analysis

**When to use:** User asks "what's trending", "what's hot on X", or "what are the big conversations about [topic]".

**How to run:**
```bash
node {baseDir}/scripts/search.js --days 1 "trending [topic] most discussed"
```

For trend analysis, use a short time window (1-3 days) and include words like "trending", "viral", "most discussed", or "biggest conversations". Grok will identify high-engagement posts and common themes.

**Example queries:**
- "What's trending on X about AI today?"
  - Search: --days 1 "trending AI most discussed viral"
- "What are the big conversations on Twitter this week about tech layoffs?"
  - Search: --days 7 "tech layoffs biggest conversations most discussed"
- "What's going viral about the election?"
  - Search: --days 1 "election viral trending"

**Output guidance for trends:**
When presenting trend results, organize them by theme. Group related tweets together and note which topics have the most engagement. Example format:

```
## Trending: [Topic] (last 24 hours)

### Theme 1: [Description]
- @user1: "tweet content" (high engagement)
  https://x.com/user1/status/123
- @user2: "related tweet"
  https://x.com/user2/status/456

### Theme 2: [Description]
- @user3: "tweet content"
  https://x.com/user3/status/789
```

### Mode 4: Profile / Account Analysis

**When to use:** User asks "who is @handle on X", "what does @handle post about", or "show me recent posts from @handle".

**How to run:**
```bash
node {baseDir}/scripts/search.js --handles username --days 30 "posts from @username"
```

For profile analysis, use `--handles` to lock onto the account and search their recent activity. You can combine this with topic filters to see what someone has been saying about a specific subject.

**Example queries:**
- "What has @elonmusk been posting about lately?"
  - Search: --handles elonmusk --days 7 "recent posts from @elonmusk"
- "Show me @karpathy's recent AI takes"
  - Search: --handles karpathy --days 30 "AI machine learning deep learning"
- "Who is @levelsio on X?"
  - Search: --handles levelsio --days 30 "posts from @levelsio about"

**Output guidance for profiles:**
When presenting profile results, lead with a summary of what the person mainly posts about, then show recent highlights:

```
## @username - Profile Summary

**Main topics:** AI, startups, indie hacking
**Posting frequency:** ~5-10 tweets/day
**Notable recent posts:**

1. @username (Mar 14): "tweet content"
   https://x.com/username/status/123

2. @username (Mar 12): "tweet content"
   https://x.com/username/status/456
```

### Mode 5: Image Search

**When to use:** User asks for images, screenshots, photos, memes, or visual content from X. This is a key differentiator - Grok-4.20 can return image results.

**How to run:**
```bash
node {baseDir}/scripts/search.js --model grok-4.20-beta-0309-reasoning "images of [topic]"
```

**IMPORTANT:** You MUST use a grok-4.20 model for image searches. The grok-4-1-fast models do not return image results.

You can also combine Responses API + x_search with `grok-imagine-image` for image generation based on search context. For example, search for trending meme formats on X, then generate a new image in that style.

**Example queries:**
- "Show me images people are sharing about the Mars landing on X"
  - Search: --model grok-4.20-beta-0309-reasoning --days 3 "Mars landing images photos"
- "Find memes about programming on X"
  - Search: --model grok-4.20-beta-0309-reasoning --days 7 "programming memes funny"
- "What screenshots are people sharing about Claude Code?"
  - Search: --model grok-4.20-beta-0309-reasoning --days 7 "Claude Code screenshots"

### Mode 6: Multi-Agent Research

**When to use:** User asks for comprehensive analysis, cross-topic comparison, or deep research that requires multiple search angles.

**How to run:**
```bash
node {baseDir}/scripts/search.js --model grok-4.20-multi-agent-beta-0309 "comprehensive query"
```

The multi-agent model breaks complex queries into sub-tasks, searches from multiple angles, and synthesizes findings. Best for:
- "Compare what @OpenAI and @AnthropicAI communities are saying about model safety"
- "Give me a comprehensive view of the React vs Svelte debate this month"
- "Research the full timeline of the [event] controversy on X"

---

## Prompt Caching

xAI supports prompt caching with a 90% reduction in input token costs on cached tokens. This is useful when:

- Running repeated searches with similar system prompts or context
- Monitoring a topic over time with the same base query
- Building dashboards that poll X at regular intervals

Cached tokens are automatically detected by the API. Structure your requests so the system prompt and tool definitions stay consistent across calls - only the user query should change. The first request pays full price; subsequent requests with the same prefix get the 90% discount.

---

## Batch API

The xAI Batch API (GA since January 2026) supports bulk search processing. Use it for:

- **Monitoring workflows** - search 50 different topics/accounts in a single batch
- **Research pipelines** - run many queries overnight at reduced cost
- **Competitive analysis** - track multiple competitors' X activity in parallel

### How to use batch search

```
POST https://api.x.ai/v1/batch
Authorization: Bearer $XAI_API_KEY

{
  "requests": [
    {
      "custom_id": "search-1",
      "method": "POST",
      "url": "/v1/responses",
      "body": {
        "model": "grok-4-1-fast-non-reasoning",
        "tools": [{"type": "x_search"}],
        "input": "Query 1"
      }
    },
    {
      "custom_id": "search-2",
      "method": "POST",
      "url": "/v1/responses",
      "body": {
        "model": "grok-4-1-fast-non-reasoning",
        "tools": [{"type": "x_search"}],
        "input": "Query 2"
      }
    }
  ]
}
```

Batch requests are processed asynchronously. Poll the batch status endpoint to check completion. Results are returned in the same order as the requests.

---

## Output Formatting

When presenting search results to the user, format them as tweet cards with engagement context.

### Standard Tweet Card Format

```
@username (Display Name) - Mar 14, 2026
"The full tweet content goes here, preserving the original text
exactly as posted."

Link: https://x.com/username/status/1234567890123456789
```

### Compact Format (for large result sets)

```
- @username: "Tweet content truncated if needed..." (Mar 14)
  https://x.com/username/status/123
```

### Links-Only Format

When the user just wants URLs (e.g., to open in a browser or pass to another tool):

```
https://x.com/user1/status/123
https://x.com/user2/status/456
https://x.com/user3/status/789
```

### Key Formatting Rules

1. Always include the direct X link for every tweet
2. Preserve the original tweet text - do not paraphrase
3. Include the author's @handle on every tweet
4. When Grok returns engagement signals (likes, retweets, replies), include them
5. Group results by relevance, not chronologically, unless the user asks for a timeline
6. If no results are found, say so clearly and suggest alternate queries
7. Cap output at 10-15 tweets unless the user asks for more

---

## Error Recovery

### No API Key

If `XAI_API_KEY` is not set and no key is found in the config:

```
Error: No xAI API key found.
Set XAI_API_KEY in your environment or run:
  openclaw config set skills.entries.search-x.apiKey "xai-YOUR-KEY"
Get your key at: https://console.x.ai
```

### Rate Limits (HTTP 429)

The xAI API has rate limits. If you hit a 429:

1. Wait 10-15 seconds and retry once
2. If still rate-limited, tell the user: "xAI API rate limit hit. Try again in a minute."
3. Do NOT retry more than once - the user can re-run manually

### Authentication Failures (HTTP 401 / 403)

1. Tell the user their API key may be invalid or expired
2. Direct them to https://console.x.ai to check their key
3. Suggest: `export XAI_API_KEY="xai-NEW-KEY"`

### Empty Results

If Grok returns no tweets:

1. Try broadening the time window: `--days 90` instead of `--days 7`
2. Try removing handle filters
3. Try simpler query terms
4. Tell the user: "No tweets found for this query. Try broader search terms or a wider time range."

### Timeout / Network Errors

1. Grok searches can take 5-15 seconds for complex queries - this is normal
2. Multi-agent queries may take 15-30 seconds - this is expected
3. If the request times out, retry once with a simpler query
4. If network is down, tell the user clearly

---

## Environment Variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `XAI_API_KEY` | Yes | - | Your xAI API key from console.x.ai |
| `SEARCH_X_MODEL` | No | `grok-4-1-fast-non-reasoning` | Default model for searches |
| `SEARCH_X_DAYS` | No | `30` | Default time window in days |

---

## Example Usage in Chat

### Simple search

**User:** "Search X for what people are saying about Claude Code"
**Action:** Run `node {baseDir}/scripts/search.js "Claude Code"`
**Present:** Tweet cards with links

### Time-scoped search

**User:** "Find tweets from the last week about Rust vs Go"
**Action:** Run `node {baseDir}/scripts/search.js --days 7 "Rust vs Go"`

### Account-specific search

**User:** "What has @karpathy been posting about lately?"
**Action:** Run `node {baseDir}/scripts/search.js --handles karpathy --days 14 "posts from @karpathy"`

### Thread reading

**User:** "Read this thread: https://x.com/sama/status/1234567890"
**Action:** Extract handle (sama), run `node {baseDir}/scripts/search.js --handles sama --days 7 "thread"`

### Trend check

**User:** "What's trending about AI on X today?"
**Action:** Run `node {baseDir}/scripts/search.js --days 1 "AI trending most discussed viral"`

### Image search

**User:** "Show me images people are sharing about the new MacBook on X"
**Action:** Run `node {baseDir}/scripts/search.js --model grok-4.20-beta-0309-reasoning --days 7 "new MacBook images photos screenshots"`

### Multi-agent research

**User:** "Give me a comprehensive analysis of the AI safety debate on X this month"
**Action:** Run `node {baseDir}/scripts/search.js --model grok-4.20-multi-agent-beta-0309 --days 30 "AI safety debate comprehensive analysis all perspectives"`

### Multi-handle comparison

**User:** "What are @OpenAI and @AnthropicAI saying about AI safety?"
**Action:** Run `node {baseDir}/scripts/search.js --handles OpenAI,AnthropicAI --days 30 "AI safety"`

### Exclusion filter

**User:** "Search X for AI news but exclude bot accounts"
**Action:** Run `node {baseDir}/scripts/search.js --exclude bots,spam --days 7 "AI news"`

### Links for later

**User:** "Give me links to the best tweets about indie hacking this month"
**Action:** Run `node {baseDir}/scripts/search.js --links-only --days 30 "indie hacking best advice"`

### JSON for piping

**User:** "Get raw JSON of tweets about TypeScript"
**Action:** Run `node {baseDir}/scripts/search.js --json "TypeScript"`

### Batch monitoring

**User:** "Set up monitoring for 10 competitor accounts on X"
**Action:** Use the Batch API to submit 10 queries at once, one per competitor handle

---

## Tips for Best Results

1. **Be specific with queries.** "Claude Code tips for monorepos" beats "Claude Code" every time.
2. **Use handle filters for signal.** Following specific people cuts noise dramatically.
3. **Match time window to intent.** Breaking news = 1 day. Weekly roundup = 7 days. Research = 30-90 days.
4. **Use grok-4.20 models for visual queries.** They are the only models that return image results from X.
5. **Use multi-agent for deep research.** `grok-4.20-multi-agent-beta-0309` excels at multi-angle analysis.
6. **Combine modes.** Search for a topic, find an interesting thread, then read that thread in full.
7. **Use --compact for scanning.** When you need to review many tweets quickly, compact mode strips commentary.
8. **Use --links-only for workflows.** Pipe links to a browser opener, bookmark tool, or another skill.
9. **Leverage prompt caching.** Keep system prompts consistent across repeated searches to save 90% on input costs.
10. **Use Batch API for bulk work.** Monitoring, competitive analysis, and research pipelines benefit from batch processing.

---

## Related Skills

- **/xai** - Direct Grok chat without X search (general knowledge, coding, analysis)
- **/last30days** - Research what happened in the last 30 days across multiple sources (not just X)
- **/parallel** - Run multiple searches in parallel for faster research workflows
