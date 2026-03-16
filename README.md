# Search X Skill for OpenClaw

Search X/Twitter in real-time using xAI's Responses API with the `x_search` tool. Get actual tweets with citations, engagement context, and direct links. No scraping, no cookies - just the xAI API.

## What it does

- **Responses API with x_search** - server-side search orchestration with automatic citations
- **Four search modes** - keyword, semantic, user search, and thread fetch
- **Multi-agent research** - Grok 4.20 multi-agent model for complex cross-topic analysis
- **Real-time tweet search** - find tweets, trends, and discussions as they happen
- **Thread reader** - follow reply chains and expand full threads
- **Trend analysis** - see what's trending around any topic
- **Profile lookup** - see what any account has been posting about
- **Image search** - Grok-4.20 returns image results from X (screenshots, memes, photos)
- **Prompt caching** - 90% input cost reduction on repeated searches
- **Batch API** - bulk search processing for monitoring and research workflows
- **Time and handle filtering** - search any time range, specific accounts, or exclude noise
- **Multiple output formats** - full results, compact, links-only, or JSON

## Quick start

### Install the skill

```bash
git clone https://github.com/mvanhorn/clawdbot-skill-search-x.git ~/.openclaw/skills/search-x
```

### Set up your API key

Get a key from [console.x.ai](https://console.x.ai), then:

```bash
export XAI_API_KEY="xai-YOUR-KEY"
```

### Example chat usage

- "Search X for what people are saying about Claude Code"
- "Find tweets from @remotion_dev in the last week"
- "What's trending on Twitter about AI today?"
- "Read this thread from @karpathy about transformers"
- "Who is @levelsio on X? What do they post about?"
- "Show me images people are sharing about the new MacBook on X"
- "Give me a comprehensive analysis of the AI safety debate on X this month"

## CLI usage

```bash
# Basic search
node scripts/search.js "AI video editing"

# Time-scoped
node scripts/search.js --days 7 "breaking news"
node scripts/search.js --days 1 "trending today"

# Handle filters
node scripts/search.js --handles @elonmusk,@OpenAI "AI announcements"
node scripts/search.js --exclude @bots "real discussions"

# Output formats
node scripts/search.js --compact "topic"
node scripts/search.js --links-only "topic"
node scripts/search.js --json "topic"

# Image search (requires grok-4.20 model)
node scripts/search.js --model grok-4.20-beta-0309-reasoning "memes about programming"

# Multi-agent research
node scripts/search.js --model grok-4.20-multi-agent-beta-0309 "comprehensive AI safety analysis"
```

## Models

| Model | Context | Price (in/out) | Best For |
|-------|---------|----------------|----------|
| `grok-4.20-multi-agent-beta-0309` | 2M | $2 / $6 | Complex multi-step research |
| `grok-4.20-beta-0309-reasoning` | 2M | $2 / $6 | Deep analysis with reasoning, images |
| `grok-4-1-fast-reasoning` | 2M | $0.20 / $0.50 | Fast search with reasoning |
| `grok-4-1-fast-non-reasoning` (default) | 2M | $0.20 / $0.50 | Quick searches (cheapest) |

## How it works

Uses xAI's Responses API (`POST /v1/responses`) with the `x_search` tool. This is a first-party xAI tool with access to the full X firehose - not a scraper. The Responses API handles all search orchestration server-side: Grok decides when to search, executes the search, and returns structured results with URL citations. You can combine `x_search` with `web_search` and `code_interpreter` in the same request. See [SKILL.md](SKILL.md) for full documentation of all six search modes, prompt caching, and the Batch API.

## Environment variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `XAI_API_KEY` | Yes | - | Your xAI API key |
| `SEARCH_X_MODEL` | No | `grok-4-1-fast-non-reasoning` | Default model |
| `SEARCH_X_DAYS` | No | `30` | Default time window |

## License

MIT
