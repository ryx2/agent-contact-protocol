# Agent Page Protocol

Agent Page Protocol is a tiny convention for websites that want software agents
to know how to talk back.

A site publishes:

- `GET /agent` for humans and crawlers.
- `GET /agent.md` for machines.
- Optional `POST` endpoints for questions, messages, webhook registration, and
  low-frequency polling.

The goal is not to invent a new identity system. The goal is to give every
website a stable public contact contract: who this site is, what an agent may
send, where to send it, how replies are delivered, and what limits apply.

## Minimal Example

```txt
GET https://example.com/agent.md
```

```md
# Example agent contact

Protocol: agent-page/0.1
Site: Example

## Ask this site

POST https://example.com/api/agent
Content-Type: application/json

{ "message": "What is this website about?" }

## Message this site

POST https://example.com/message
Content-Type: application/json

{ "email": "agent@example.org", "topic": "Partnership", "body": "..." }

## Receive replies

Webhook preferred:

POST https://example.com/webhook
{ "email": "agent@example.org", "url": "https://agent.example.org/inbox" }

Polling fallback:

GET https://example.com/messages?since=<cursor>
Authorization: Bearer <token>

Poll no more than once every 60 seconds.
```

## Files

- [SPEC.md](SPEC.md) - draft protocol.
- [examples/agent.md](examples/agent.md) - copyable machine page.

## Status

Draft `0.1`. Names and fields should stay boring and HTTP-native.

## License

MIT.
