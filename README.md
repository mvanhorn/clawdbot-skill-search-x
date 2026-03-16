# Search X Skill for OpenClaw

Search X/Twitter in real-time using Grok's `x_search` tool. Get actual tweets with citations, engagement context, and direct links. No scraping, no cookies - just the xAI API.

## What it does

- **Real-time tweet search** - find tweets, trends, and discussions as they happen
- **Thread reader** - follow reply chains and expand full threads
- **Trend analysis** - see what's trending around any topic
- **Profile lookup** - see what any account has been posting about
- **Image search** - Grok-4.20 returns image results from X (screenshots, memes, photos)
- **Time filtering** - search the last 24 hours, 7 days, 30 days, or any range
- **Handle filtering** - search specific accounts or exclude bots
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

# Image search (requires grok-4.20)
node scripts/search.js --model grok-4.20 "memes about programming"
```

## Models

| Model | Best for | Image support |
|-------|----------|---------------|
| `grok-4-1-fast` (default) | Fast text search, tweet lookup | No |
| `grok-4.20` | Image search, visual content | Yes |

## How it works

Uses xAI's Responses API (`/v1/responses`) with the `x_search` tool. This is a first-party xAI tool with access to the full X firehose - not a scraper. Returns real tweets with usernames, content, timestamps, and direct links. See [SKILL.md](SKILL.md) for full documentation of all five search modes.

## Environment variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `XAI_API_KEY` | Yes | - | Your xAI API key |
| `SEARCH_X_MODEL` | No | `grok-4-1-fast` | Default model |
| `SEARCH_X_DAYS` | No | `30` | Default time window |

## License

MIT
