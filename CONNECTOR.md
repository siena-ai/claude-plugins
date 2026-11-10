# Siena connector for Claude

The Siena connector links Claude to your [Siena](https://siena.cx) account through Siena's MCP server at `https://mcp.siena.cx`. Once connected, you can ask Claude about your customer conversations and work with your Siena setup in plain language: Journeys, policies, personas, your knowledge base, the Shopping Agent, inbox views and rules, and more.

## Requirements

- A Siena account with the Intelligence product.
- Claude on any plan (web, desktop or Claude Code).

## Add the connector

### Claude on the web or desktop

**Free, Pro, Max or Ultra plan**

1. Go to **Customize > Connectors**.
2. Click **Add custom connector**.
3. Enter `https://mcp.siena.cx` as the server URL.
4. Click **Add**.

**Team or Enterprise plan**

An Owner adds the connector for the organization:

1. Go to **Organization settings > Connectors**.
2. Select **Add**, then **Custom**. If asked for the connector type, choose **Web**.
3. Enter `https://mcp.siena.cx` as the server URL and click **Add**.

Each member then goes to **Customize > Connectors**, finds Siena (labelled **Custom**) and clicks **Connect**.

### Claude Code

Install the Siena plugin, which adds the connector together with Siena skills:

```
/plugin marketplace add siena-ai/claude-plugins
/plugin install siena@siena
```

Or add only the connector:

```
claude mcp add --transport http siena https://mcp.siena.cx
```

## Sign in

The first time Claude uses a Siena tool, your browser opens Siena's sign-in page:

1. Sign in with your Siena account.
2. If you belong to several organizations, choose the one to connect.
3. You return to Claude, connected to that organization.

The connection covers one organization at a time. To switch, disconnect the connector and connect again.

## Use it in a chat

In a conversation, click **+**, select **Connectors** and turn Siena on. Then ask, for example:

- "What were the top reasons customers contacted us last week?"
- "Show me the Journeys that handle order cancellations and summarize their steps."
- "Which policies mention returns, and when were they last updated?"
- "Find conversations tagged as damaged item in the last 30 days and group them by product."
- "How do I set up Siena Live Chat?"

Tools that only read run without asking. Tools that change something in Siena ask for your approval each time.

## Permissions

The connector acts as you, with your Siena role. A tool is hidden when your role can't view that area, and a change is refused with a message naming the missing permission when your role can't make it. Which tools appear also depends on the features enabled for your organization. To change your role, ask an Administrator or Owner in your Siena organization.

## Disconnect

Go to **Customize > Connectors** (Team and Enterprise Owners: **Organization settings > Connectors**) and remove Siena. In Claude Code, run `claude mcp remove siena`. To revoke a session on Siena's side, contact Siena support.

## Privacy and support

- Privacy policy: https://siena.cx/privacy-policy
- Support: contact Siena support from your dashboard at https://app.siena.cx
