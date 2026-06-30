# Agent Page Protocol 0.1

## Purpose

Agent Page Protocol defines a public, low-friction contact contract for websites.
It tells agents how to ask questions, send messages, receive replies, and respect
limits without scraping hidden UI or guessing endpoints.

## Required URLs

### `GET /agent`

Human-readable HTML page. It SHOULD link to `/agent.md`.

```html
<link rel="alternate" type="text/markdown" href="/agent.md">
```

### `GET /agent.md`

Machine-readable Markdown page. It SHOULD be short, stable, and explicit.

Recommended headers:

```txt
Content-Type: text/markdown; charset=utf-8
X-Robots-Tag: noindex
```

## Required Markdown Sections

### Identity

Name the site, canonical URL, and protocol version.

```md
Protocol: agent-page/0.1
Site: Example
Canonical: https://example.com/agent
Machine-readable: https://example.com/agent.md
```

### Capabilities

List every public machine contact path. Common capabilities:

- `ask` - an AI or help endpoint for questions about the site.
- `message` - an inbound message endpoint.
- `reply` - how to continue an existing thread or channel.
- `webhook_register` - where an agent registers a push URL.
- `poll` - durable fallback for agents that cannot receive webhooks.

### Limits

State rate limits, polling intervals, auth requirements, moderation policy, and
what happens on rejection.

## Recommended JSON Hints

Responses from message/control endpoints SHOULD include machine-actionable hints:

```json
{
  "reply_with": {
    "method": "POST",
    "url": "https://example.com/message",
    "body": { "channel_id": "<id>", "body": "..." }
  },
  "webhook_register": {
    "method": "POST",
    "url": "https://example.com/webhook",
    "body": { "url": "https://agent.example.org/inbox" }
  },
  "poll_url": "https://example.com/messages?since=0",
  "poll_after_seconds": 60
}
```

## Webhooks

If webhooks are supported, they SHOULD:

- Require HTTPS.
- Return a shared secret on registration.
- Sign delivered payloads with HMAC-SHA256 over the raw request body.
- Include a delivery id for idempotency.
- Treat polling as the source of truth.

Recommended headers:

```txt
Agent-Page-Signature: sha256=<hex>
Agent-Page-Delivery-Id: <id>
Agent-Page-Event: message
```

## Polling

Polling is allowed for agents without public inbound HTTP, but it must be slow.
The `/agent.md` page SHOULD state the minimum interval.

Recommended default:

```txt
poll_after_seconds: 60
```

Servers SHOULD return `429 Retry-After` when callers poll too quickly.

## Non-Goals

Agent Page Protocol does not define:

- Global identity.
- Trust or reputation.
- Payment.
- A universal moderation policy.
- A replacement for email, ActivityPub, MCP, A2A, or HTTP signatures.

It is only the contact card.
