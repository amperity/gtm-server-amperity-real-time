# Amperity Real Time Events Tag for GTM server containers

A Google Tag Manager **server-side** tag template that forwards events from your server container to
an [Amperity](https://amperity.com) real-time event stream. Amperity then uses those events for
identity resolution and real-time profiles.

The tag sends every event the container receives (for example, from the built-in GA4 client) to:

```
POST {apiBaseUrl}/prof/events/{streamId}
```

## What it sends

- **All event data, with no fixed field list.** The tag reads the full event with `getAllEventData()`.
- **Two renamed fields:** `client_id` becomes `visitor_id`, and `page_location` becomes `page_url`.
- **Metadata fields:** `amp_adapter` (`gtm-server-container`), `amp_container_version` and
  `amp_container_id`.
- **Flattened structure:** nested objects and arrays become dot-notation keys, for example
  `items.0.item_id`.
- **No client-side timestamp.** Amperity stamps `received_at` when the event arrives, and that is the
  canonical event time.

## Prerequisites

- A GTM **server** container with a tagging server.
- An Amperity tenant with a real-time **event stream**. Note its ID, which looks like `esm-...`.
- An Amperity API key (JWT) that is allowed to write to that stream.

## Setup

1. In your server container, go to **Templates → Tag Templates → Search Gallery** and add
   **Amperity Real Time Events Tag**.
2. Go to **Tags → New**, choose the template, and fill in the fields:

   | Field | Description |
   |---|---|
   | Amperity API Base URL | Your Amperity host, for example `https://app.amperity.com` |
   | Amperity Tenant ID | Your tenant identifier. Sent as the `X-Amperity-Tenant` header. |
   | Amperity Event Stream ID | The `esm-...` ID of the target event stream |
   | Amperity API Key (JWT) | Bearer token for the Streaming Ingest API |
   | Enable debug logging | Logs event names and response codes in Preview and debug mode only |

3. Add a trigger. For example, a **Custom** trigger that fires on all events from the GA4 client.
4. Test in **Preview**. The tag should report success, and the events should show up in your
   Amperity event stream.
5. Publish the container.

## Permissions

| Permission | Scope |
|---|---|
| Sends HTTP requests | `https://*.amperity.com/prof/events/*` only |
| Reads event data | All keys (the tag forwards the full event) |
| Reads container data | Container ID and version, which are added as metadata |
| Logs to console | Debug environment only |

## Security notes

- The API key is sent only from your server container and is never exposed to the browser. Anyone
  with edit access to the container can still read it in the tag settings, so use a key that is
  scoped to ingest and can be revoked.

## Support

To report bugs or request features, open an issue in this repository. For help with your Amperity
setup, contact your Amperity representative or see [docs.amperity.com](https://docs.amperity.com).

## License

Apache 2.0. See [LICENSE](LICENSE).
