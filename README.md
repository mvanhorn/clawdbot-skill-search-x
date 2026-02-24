# 🔍 Search X Skill for OpenClaw

Search X/Twitter in real-time using Grok's `x_search` tool. Get actual tweets with citations.

## What it does

- **Real-time search** - find tweets, trends, and discussions as they happen
- **Time filtering** - search the last 24 hours, 7 days, 30 days, etc.
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
- "Search X for Remotion best practices, last 30 days"

## CLI usage

```bash
node scripts/search.js "AI video editing"
node scripts/search.js --days 7 "breaking news"
node scripts/search.js --handles @elonmusk,@OpenAI "AI announcements"
node scripts/search.js --compact "topic"
node scripts/search.js --links-only "topic"
```

## How it works

Uses xAI's Responses API (`/v1/responses`) with the `x_search` tool. Model: `grok-4-1-fast` (optimized for agentic search). Returns real tweets with usernames, content, timestamps, and direct links.

## License

MIT
