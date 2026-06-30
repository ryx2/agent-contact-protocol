# Agent Contact Protocol 0.1

## Purpose

Agent Contact Protocol defines a public, low-friction contact endpoint for
websites. It tells agents how to ask questions, send messages, receive replies,
and respect limits without scraping hidden UI or guessing endpoints.

## Required URLs

### `GET /agent`

The public contact contract. It MAY return HTML by default and SHOULD return
Markdown when the caller sends `Accept: text/markdown`.

```html
<link rel="alternate" type="text/markdown" href="/agent.md">
```

### `POST /agent`

The default machine contact action. The site decides whether this means "ask the
site AI", "send an inbound message", "open a channel", or "return a more specific
endpoint to use next."

The request body SHOULD be JSON. Minimal payload:

```json
{ "message": "What is this website about?" }
```

### `GET /agent.md`

Optional machine-readable Markdown mirror. It SHOULD be short, stable, and
explicit.

Recommended headers:

```txt
Content-Type: text/markdown; charset=utf-8
X-Robots-Tag: noindex
```

## Required Markdown Sections

### Identity

Name the site, canonical URL, and protocol version.

```md
Protocol: agent-contact/0.1
Site: Example
Canonical: https://example.com/agent
Machine-readable: https://example.com/agent.md
```

### Capabilities

List every public machine contact path. Common capabilities:

- `contact` - the default `POST /agent` action.
- `ask` - an AI or help action for questions about the site.
- `message` - an inbound message action.
- `reply` - how to continue an existing thread or channel.
- `webhook_register` - where an agent registers a push URL.
- `poll` - durable fallback for agents that cannot receive webhooks.

### Limits

State rate limits, polling intervals, auth requirements, moderation policy, and
what happens on rejection.

## Recommended JSON Hints

Responses from `POST /agent` and message/control endpoints SHOULD include
machine-actionable hints:

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
Agent-Contact-Signature: sha256=<hex>
Agent-Contact-Delivery-Id: <id>
Agent-Contact-Event: message
```

## Polling

Polling is allowed for agents without public inbound HTTP, but it must be slow.
The `/agent` contract SHOULD state the minimum interval.

Recommended default:

```txt
poll_after_seconds: 60
```

Servers SHOULD return `429 Retry-After` when callers poll too quickly.

## Non-Goals

Agent Contact Protocol does not define:

- Global identity.
- Trust or reputation.
- Payment.
- A universal moderation policy.
- A replacement for email, ActivityPub, MCP, A2A, or HTTP signatures.

It is only the contact endpoint.
