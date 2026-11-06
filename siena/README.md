# Siena plugin for Claude

Connects Claude to your [Siena](https://siena.cx) account and teaches it how Siena works, so you can ask questions about your customer conversations and your Siena setup in plain language.

## What it includes

**The Siena connector** (`https://mcp.siena.cx/`). You sign in with your Siena account; Claude then sees only the organization and data your Siena role allows.

**Skills:**

| Skill | Use it for |
|---|---|
| `data-analytics` | Voice-of-customer, topic, sentiment and volume questions over your helpdesk conversations, including Social Agent data |
| `evaluations-and-topics-explorer` | Reproducing the numbers on the Evaluations tabs and Topics Explorer |
| `siena-features` | What Siena's Support Agent, Shopping Agent, Social Agent and Live Chat do, and the words the dashboard uses |
| `modules-and-integrations` | Which apps and channels Siena supports, what actions each can run, and where each is configured |
| `configuring-the-shopping-agent` | Changing Shopping Agent channels, guidance, product visibility and recommendation priority |
| `journey-builder` | Building or improving journeys and Siena Policies from SOPs, existing journeys, QA feedback or macros |

## Install

In Claude Code:

```
/plugin install siena --marketplace siena-ai/claude-plugins
```

## Requirements

- A Siena account. Some tools depend on your role and on features enabled for your organization.
- Claude (web, desktop, Cowork) or Claude Code.

## Data

The skills are instructions only and send nothing anywhere. All data access goes through the Siena connector, under your Siena sign-in. See Siena's [privacy policy](https://siena.cx/privacy-policy).

## Support

Contact Siena support from your dashboard at https://app.siena.cx.

## License

Proprietary. Copyright (c) 2026 Siena Labs, Inc. All rights reserved. Use is permitted only through Claude with the Siena service; see [LICENSE](./LICENSE).
