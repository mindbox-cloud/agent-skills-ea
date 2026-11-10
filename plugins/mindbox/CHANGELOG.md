# Changelog

## 1.0.0

First Early Access release. The same five skills as the main marketplace, at their latest
state: changes reach this channel before they reach
[agent-skills](https://github.com/mindbox-cloud/agent-skills), and may still change before
they do.

- **`/mindbox:flow-create`** — designs a marketing scenario from a plain-language description,
  fills and wires its blocks, verifies each one by reading it back, and hands the flow over as
  a draft with a link and a checklist of what is left to a human.
- **`/mindbox:filter-build`** — builds an audience filter from a request, validates it on the
  platform and returns a link to the list when one is available; it also edits an existing
  filter.
- **`/mindbox:filter-explain`** — explains in plain words which audience a filter selects,
  where its limits are, and what could not be interpreted.
- **`/mindbox:email`** — lays out an email for the Mindbox visual editor: structure, text,
  images, buttons, styles, personalization and product rows, with an unsubscribe link. Newer
  here than in the main marketplace: editor blocks are edited through their own settings,
  images are always placed through the project gallery, and dividers follow the editor's real
  rules.
- **`/mindbox:email-ops`** — creates and edits campaigns, finds or uploads images in the
  project gallery, previews the email, saves it into the campaign and sends a test to the
  project's test recipients.

Nothing is launched or sent to customers, no campaign is activated or deleted, and no filter is
saved as a segment. Saving an email, editing a campaign and a test send each wait for your
explicit confirmation.

Install either this marketplace or the main one, not both: both ship a plugin named
`mindbox`, and their commands would collide.

```
/plugin marketplace add https://github.com/mindbox-cloud/agent-skills-ea
/plugin install mindbox@mindbox-cloud-plugins-ea
```

```
codex plugin marketplace add mindbox-cloud/agent-skills-ea
codex plugin add mindbox@mindbox-cloud-plugins-ea
```

## 1.1.0

- **`/mindbox:analytics`** — new: answers questions about a project's figures from its
  analytics data — a metric, why it changed, how a campaign or a flow performed, a summary
  of a period. Answers follow fixed rules, end with the matching report in the admin menu,
  and give no benchmark without a named source.

## 1.2.0

- **`/mindbox:whypromo`** — new: explains in plain language why a promotion did not apply to an
  order, gave no benefit or gave less than expected. Takes the explanation link from the admin panel
  or a promotion link with an order number, names one reason and who to go to, and gives the detailed
  version on request. Read-only; it never shows the customer's promo code.

## 1.2.1

- **`/mindbox:analytics`** — the report link is now built from the project's admin address.
- **`/mindbox:whypromo`** — works over the client connection: no project name in tool calls
  unless a tool asks for one.

## 1.3.0

Emails catch up with the main marketplace.

- **`/mindbox:email`**
  - Reads each element's attributes, defaults and allowed values from the editor's reference
    served by the `wiki` tool, instead of a copy bundled with the skill. When the reference
    cannot be read, it stops rather than guesses.
  - Builds an email from a saved template.
  - Takes a block from the editor's library and changes its settings; a block shared between
    emails is inserted as is, without editing its content.
  - Shows a block or a row only to a segment of customers.
  - Uses the project's custom fonts, keeps the segments and fonts of an email it edits, and
    changes shared styles field by field.
  - Lays out an email from a mockup by the role and position of each element, and asks which
    width to take when the mockup's differs from the email's.
  - Adds an unsubscribe link to every new email, except a transactional one or one whose
    wrapper or campaign settings carry the link.
- **`/mindbox:email-ops`**
  - Creates, edits and copies saved templates.
  - Shows a preview after every change, and before sending, a short list of what the campaign
    still needs.
  - Takes the parameter names for reading, previewing and saving an email from the tools
    themselves, so it keeps working as the project's MCP server updates its email tools.

Emails now also need the `wiki` tool of the project's MCP server.

Setup: the Mindbox MCP server has one address for everyone, https://mcp.mindbox.ru. Add it as a
custom connector and choose the project when you sign in — there is no separate server per
project anymore.
