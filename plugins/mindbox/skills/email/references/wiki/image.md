---
description: "An image in an email: a source from a file or a parameter, click, size, alignment, socials lines, an image as the email background, what the preview shows"
---

# Image — `<Image>`

A standalone element of a column or a line of `<Socials>`. No content, always a single tag; the source is set by
the mandatory `image` attribute.

## Source

`image` takes two modes.

**A file** — `mode: "static"`: the address and the file name.

```jsx
<Image image={{ mode: "static", static: { url: "https://cdn.example.com/banner.png", fileName: "banner.png" } }} />
```

`url` is the address the image is loaded from in the email. `fileName` is a service name. For an image from the
gallery it is the `fileName` of the row Ops returned; an image stored in the gallery as WebP is served at the
address of its PNG copy.

**From a parameter** — `mode: "dynamic"`: the address is assembled as a list of segments with a chip, like any
personalized value. In the editor this is the "Personalised picture for the customer" radio button.

```jsx
<Image image={{ mode: "dynamic", dynamic: [<Var param="RecipientCustomFieldString" customFieldType={{ "systemName": "<CUSTOM_FIELD_SYSTEM_NAME_FROM_LOOKUP>" }} />] }} />
```

The parameter's value is the whole address; text before the chip (`dynamic: ["https://cdn.shop.example/",
<Var … />]`) becomes its prefix, and like any literal address in an email it is `https://`. The modes are mutually exclusive: write only the fields of the chosen one — `static`
for a file, `dynamic` for a parameter. The field's value is not checked at conversion: a non-URL value leaves the recipient with an image that
fails to load. An empty value is handled by `emptyVariableBehavior` — in the editor this is "Display
conditions" → "If the variables in the element are empty".

An image with no source — `<Image />` — converts, but in place of the address the platform substitutes a
built-in `data:` placeholder.

## Attributes

| attribute | what it does | default |
|---|---|---|
| `image` | the source, see above | mandatory |
| `url` | the click address — an HTTPS string or a list of segments with a chip: `url={["https://shop.example/points/", <Var … />]}` or a single `<Var>` as a whole; the converter does not validate the click address | no link |
| `size` | the size of a standalone image: `{ type: "fixed", width: N, mobile: { type: "fixed", width: N } }` in pixels, or `{ type: "manual", width: N }` as a percentage of the column | `{ type: "inherit" }`: the full width of the column, and a narrower file is scaled up and blurred — a logo or an icon gets an explicit `size` |
| `align` | `{ align: "left" \| "center" \| "right", mobile: { align: "…" } }` | — |
| `alt` | the caption in place of an image that failed to load: a string or a list of segments with a chip | empty |
| `emptyVariableBehavior` | what happens to the image in the sent email when a variable in its `image`, `url` or `alt` is empty: `"hide"` — the element drops out of the email entirely, `"show_height_only"` — it stays invisible but keeps its place, `"show"` — it renders as is. A variable with a fallback does not count as empty. Applies at send time; in the preview the image is always visible | `"hide"` |
| `innerSpacing` | as for `Block` | 0 on every side |
| `border`, `borderRadius`, `visibilityOnDevices` | as for `Block` | no border, no rounding, `"all"` |

In emails built in the editor `size` also carries `equalizedImageMaxWidth`: a string with the width of the
container, which the editor measures itself once the image has loaded on the canvas. It is not written for a
new image and is carried over as is when editing.

Inside a product row card an image also has `height` — [product-rows.md](../product-rows.md).

## `<Socials>` lines

Socials lines are the same `<Image image={{…}} />`; their size and alignment are set by the group:
`Socials.imageSize`, of the same shape as `size`, and `Socials.align`.

## An image as the email background

`Template.background` accepts an image; the address is the same kind of source as for `<Image>`:

```jsx
<Template background={{
  type: "image",
  url: "https://cdn.example.com/background.jpeg",
  fileName: "background.jpeg",
  fallbackColor: "#ffffff",
  mode: "cover"
}}>
```

| `mode` | what it does |
|---|---|
| `cover` | fills the background area, cropping the excess along one side |
| `contain` | fits the image whole, leaving empty space along the other |
| `stretch` | stretches it over the whole area, distorting the proportions |
| `repeat` | tiles it with copies |

`fallbackColor` is the colour the recipient sees instead of the image: some email clients do not show a
background image at all, so the text on top must stay readable on the fallback colour, and an image that
carries meaning goes into `<Image>`, not into the background.

## In the preview

The preview renders the editor canvas: an image with no source is drawn as a placeholder, and a `url` with a
chip is rendered as `href="#"` — the address is substituted at send time.
