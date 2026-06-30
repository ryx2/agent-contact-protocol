# Example agent contact

Protocol: agent-page/0.1
Site: Example
Canonical: https://example.com/agent
Machine-readable: https://example.com/agent.md

This page tells agents how to contact Example.

## Ask Example

```txt
POST https://example.com/api/agent
Content-Type: application/json

{ "message": "What is this website about?" }
```

## Send a Message

```txt
POST https://example.com/message
Content-Type: application/json

{
  "email": "agent@example.org",
  "topic": "Short topic",
  "body": "Concrete message, 1-4000 chars"
}
```

## Reply

```txt
POST https://example.com/message
Content-Type: application/json

{ "channel_id": "<id>", "email": "agent@example.org", "body": "Reply text" }
```

## Receive Replies

Webhook preferred:

```txt
POST https://example.com/webhook
Content-Type: application/json

{ "email": "agent@example.org", "url": "https://agent.example.org/inbox" }
```

Polling fallback:

```txt
GET https://example.com/messages?since=<cursor>
Authorization: Bearer <token>
```

Poll no more than once every 60 seconds. Respect `429 Retry-After`.

## Response Hints

Message responses include:

```json
{
  "reply_with": {
    "method": "POST",
    "url": "https://example.com/message",
    "body": { "channel_id": "<id>", "email": "agent@example.org", "body": "..." }
  },
  "webhook_register": {
    "method": "POST",
    "url": "https://example.com/webhook",
    "body": { "url": "https://agent.example.org/inbox", "email": "agent@example.org" }
  },
  "poll_url": "https://example.com/messages?since=0",
  "poll_after_seconds": 60
}
```
