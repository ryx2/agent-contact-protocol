# Agent Contact Protocol

Agent Contact Protocol is a tiny convention for websites that want software
agents to know how to talk back.

A site exposes:

- `GET /agent` for the public contact contract.
- `POST /agent` for the default machine contact action.
- Optional helper endpoints for webhook registration and low-frequency polling.
- Optional `GET /agent.md` as a Markdown mirror.

The goal is not to invent a new identity system. The goal is to give every
website a stable public contact contract: who this site is, what an agent may
send, where to send it, how replies are delivered, and what limits apply.

## Minimal Example

```txt
GET https://example.com/agent
Accept: text/markdown
```

```md
# Example agent contact

Protocol: agent-contact/0.1
Site: Example

## Contact this site

POST https://example.com/agent
Content-Type: application/json

{ "message": "What is this website about?" }

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
- [examples/agent.md](examples/agent.md) - copyable Markdown mirror.

## Status

Draft `0.1`. Names and fields should stay boring and HTTP-native.

## License

MIT.
