# Value formats (production contract)

**When to read:** before generating value shapes beyond the copy-safe forms of the
“Value rules (partial merge)” section in `SKILL.md` — this file has the exact forms that are not in the core.
**Return:** to Workflow / Self-check in `SKILL.md`.

Exact JSX attribute formats for the production converter. A violation means a validation error or silent data loss.

## 1. Strings

- Write them in quotes: `url="https://example.com"`, `visibilityOnDevices="mobile"`.
- A string with special characters (`"`, `&`, `<`, `>`, `{`, `}`, `\`, line break) goes in curly braces as a JSON string:
  ```jsx
  <Button url={"https://shop.example/?from=email&utm=sale"}>Shop</Button>
  ```
- In `<Text>`, a single quoted string protects against JSX parsing (`{`, `}`, `"`, line break), but its content is treated as **rich-HTML markup** rather than escaped: `{"<p>One&nbsp;Two<br>Next</p>"}` produces real `<p>`/`<br>`, not visible text with tags. So HTML special characters (`<`, `>`, `&`) inside it behave as HTML — literal text where the angle brackets themselves matter cannot be produced this way. Write ordinary markup with explicit JSX tags; move complex raw HTML (a comment, rare entities, a table) into `<Html>{"…"}</Html>`. A quoted string in `<Text>` is needed for strings with JSX-level special characters and for existing personalization. Do not mix bare text and a quoted string in one element — live rejects it (`<Text> may only contain text`).

## 2. Numbers

- Write them in curly braces: `size={12}`.
- Integers for `size`, `fontSize`, spacing, radii.

## 3. Objects (JSON structures)

- Write them in curly braces as JSON: `innerSpacing={{ top: 24 }}`, `style={{ fontSize: 24 }}`.
- **Partial merge**: write only the changed fields; the rest are filled in from the default prop value of the new node, not from the previously saved email.

### background

```jsx
background={{ type: "color", value: "#RRGGBB" }}
background={{ type: "transparent" }}
```

### border

```jsx
border={{ type: "solid" | "dashed" | "dotted", color: "#RRGGBB", size: { top: N, right: N, bottom: N, left: N } }}
border={{ type: "none" }}
```

### borderRadius

```jsx
borderRadius={{ topLeft: N, topRight: N, bottomLeft: N, bottomRight: N, mobile: { … } }}
```

### innerSpacing

```jsx
innerSpacing={{ top: N, bottom: N, left: N, right: N, mobile: { … } }}
```

### columnsGap

```jsx
columnsGap={{ size: N, mobile: { size: N } }}
```

### style (Text)

```jsx
style={{
  font: { family: "…" },
  fallbackFontFamily: "Tahoma",
  fontSize: N,
  lineHeight: "1.8",
  letterSpacing: N,
  color: "#RRGGBB",
  inscription: ["bold"|"italic"|"underlined"|"crossed"],
  link: { color: "…", inscription: […] },
  align: "left"|"center"|"right",
  mobile: { fontSize: N, align: "left"|"center"|"right" }
}}
```

Strikethrough is `crossed`. The converter silently accepts `strikethrough` and renders nothing:
there will be no error, and no strikethrough in the email.

These are exactly the nine fields that reading an email prints in full on a node outside the shared styles
(§8 `dsl-surface.md`). `lineHeight` is a string (`"1.0"`, `"1.8"`), `letterSpacing` is a number:
the mockup's letter spacing is reproduced, not declared impossible; live preview has been checked on both.
`fallbackFontFamily` follows the font rules below: a value only from the web-safe
ten, preserved on round-trip.

If `Text.style` sets `fontSize` or `align`, set `style.mobile` deliberately.
Without it the backend substitutes the mobile default `{ fontSize: 18, align: "left" }`,
and the mobile hierarchy may break.

`font.family` is a closed set of 19 names, the same for all projects: it is built into
the editor, not configured by the tenant. A name outside the lists will silently not apply, and the text
will go out in the default font.

**Web-safe (10)** — available on the recipient's side, nothing to load:
`Arial`, `Helvetica`, `Times New Roman`, `Verdana`, `Courier / Courier New`, `Tahoma`,
`Georgia`, `Palatino`, `Trebuchet MS`, `Geneva`.

**Not web-safe (9)** — loaded from Google Fonts:
`Roboto`, `Open Sans`, `Montserrat`, `Inter`, `Poppins`, `Bebas Neue`, `Overpass`,
`Nunito Sans`, `Fira Sans`.

For a font from the second list, set `fallbackFontFamily` to a value from the first: an email
client may not support the web font (Outlook, the Gmail app), and without a fallback the text
falls back to `sans-serif` instead of a replacement with a similar design.

```jsx
style={{ font: { family: "Montserrat" }, fallbackFontFamily: "Verdana" }}
```

The project's custom fonts — the ones the client uploaded themselves — are not in these lists and
cannot be chosen through JSX. A read email shows one as `font: { internalId: "…" }`, sometimes with a
`family` beside it: copy that `font` object unchanged and it is kept on save.

### simpleTextStyles (Button)

```jsx
simpleTextStyles={{
  font: { family: "…" },
  fontSize: N,
  color: "#RRGGBB",
  inscription: […],
  mobile: { fontSize: N }
}}
```

If you change a button's desktop `simpleTextStyles.fontSize`, set
`simpleTextStyles.mobile.fontSize`. Otherwise the mobile caption may revert to the
backend default.

`simpleTextStyles.font.family` and `simpleTextStyles.fallbackFontFamily` follow the same
lists and the same fallback rule as `Text.style` above.

### buttonSize

```jsx
buttonSize={{ height: N, widthType: "percent"|"pixels", width: N }}
buttonSize={{ height: N, heightMobile: N, widthType: "pixels", width: N, widthMobile: N }}
```

`widthType` is required next to `width`: without it the number is read as percent. Write `heightMobile` and
`widthMobile` only when the mobile value differs from the desktop one — without them the mobile
side is synchronized with the desktop side. On choosing between percent and pixels — `dsl-surface.md`, §6
“Button”.

### align

```jsx
align={{ align: "left"|"center"|"right", mobile: { align: "…" } }}
```

### gapAfterBlock

```jsx
gapAfterBlock={{ desktop: N, mobile: N }}
```

This is an external gap between blocks, not the standard way to do vertical spacing. For
ordinary spacing inside a section use `innerSpacing`, so that the section's background is preserved.
Set a new `gapAfterBlock` only on explicit request or when the mockup shows
a separate strip of the external background; preserve an existing value on an unrelated round-trip.

### verticalAlign

```jsx
verticalAlign={{ align: "top"|"middle"|"bottom", mobile: { align: "…" } }}
```

## 4. Lists

- **Lists are replaced, not merged**: `inscription: ["bold"]` replaces the whole list.
- Write the list in full: `["bold", "italic"]`.

## 5. Color (hex)

- Format: `#RRGGBB` (6 hex digits).
- Not validated automatically — check it yourself.
- Where it is used: inside `background.value`, `border.color`, `style.color`, `simpleTextStyles.color`.

## 6. URL

- Attribute: `Button.url` (not `href`). Optional: without it the button is saved with an empty link (`url: ""`) and is rendered without a link in the sent email — this is how a button is left when its address was asked for and the user does not know it. Do not write placeholder addresses instead.
- Allowed schemes: `https://`, `tel:`, `mailto:`.
- The backend validates the format and rejects survey links.
- A URL with `&`, `=`, `"` goes in quotes inside `{}`:
  ```jsx
  <Button url={"https://shop.example/?from=email&utm=sale"}>Shop</Button>
  ```

## 7. image (Image)

The shapes of `image`, `size`, `align`, `url` and `alt` of an image are in `wiki/image.md`.

## 8. Grid (Column.size)

- Integer 1..12, required.
- The sum within one FlexRow and `<Split>` **== 12** (strictly, not ≤).
- A `<Split>` column accepts only `size` and contains 0..1 elements.
- Error: `Column sizes in a <FlexRow> must sum to 12 (got N)` or `Column sizes in a <Split> must sum to 12 (got N)`.

## 9. Visibility (visibilityOnDevices)

- String: `"all"` (default), `"desktop"`, `"mobile"`.

## 10. Group attributes

Use these forms only on the group the attribute belongs to. `itemsGap` defaults are in `dsl-surface.md` §6, “Defaults that appear on their own”.

### itemsGap and iconTextGap

```jsx
itemsGap={{ size: N, mobile: { size: N } }}
iconTextGap={{ size: N, mobile: { size: N } }}
```

### iconTopPadding

```jsx
iconTopPadding={{ top: N, bottom: N, left: N, right: N, mobile: { top: N, bottom: N, left: N, right: N } }}
```

### imageSize

```jsx
imageSize={{ type: "fixed", width: N, mobile: { type: "fixed", width: N } }}
```

### bulletIcon

Small dot/circle (actual width 4px):

```jsx
bulletIcon={{ url: "https://cdn.example.com/dot.png", fileName: "dot.png" }}
```

Large editor-compatible marker/number badge:

```jsx
bulletIcon={{ type: "custom", url: "https://cdn.example.com/one.png", fileName: "one.png", size: N }}
```

`bulletIcon` is required by policy: the default `data:` SVG breaks the HTML attribute and
renders as a broken placeholder icon instead of a marker. `type: "custom"` and `size` are confirmed by a stored
editor template and by live preview/HTML; the actual width equals `size`. The URL in
the examples is illustrative — use only a user/gallery asset. The Image form
`{ mode: "static", static: ... }` does not work for bulletIcon.

`align`, `verticalAlign` and `columnsGap` use the forms from the corresponding sections above. `align` applies to Menu and Socials, `verticalAlign` and `columnsGap` to Split.

## 11. What cannot be removed

- Omitting an attribute = keeping the new node's default, not “removing” it.
- To reset: an empty string, zero, `{ type: "none" }` for border, `{ type: "transparent" }` for background.

## 12. Editor-template block settings (`<QuokkaBlock>`)

`visual_template_quokka_block_describe` answers with the attribute's name, its **type** and its group. The name
is opaque (`prop_SIMPLE_TEXT_6_23`), so the type is what says which shape the value takes. The rules of §13
`dsl-surface.md` say where to write the attribute; this section says what to write in it.

**Partial merge works as in §3, with two differences.** The default a value merges onto is the template's, not a
new node's. And what the email prints is already the difference, so a field that is not there is not empty — it is
the template's: add the one you were asked about, do not restore the rest. Use the shape this section gives — a
field written in another kind, a number where an object belongs, is refused by the converter, which names it.

Types whose shape is already above:

| Type | Shape |
|---|---|
| `ALIGN` | §3 `align` |
| `ALT`, `SIMPLE_TEXT` | §1, a plain string |
| `BACKGROUND` | §3 `background`; an image background is `{ type: "image", url, fileName }` |
| `BORDER` | §3 `border` |
| `BORDER_RADIUS` | §3 `borderRadius` |
| `BUTTON_SIZE` | §3 `buttonSize` |
| `COLOR` | §5; a value already in the email may be `rgba(…)` or `transparent` — leave it, do not normalise |
| `GAP` | §3 `columnsGap` |
| `IMAGE` | §7 `image` |
| `INNER_SPACING` | §3 `innerSpacing` |
| `SIMPLE_TEXT_STYLES` | §3 `simpleTextStyles` |
| `TEXT_STYLES` | §3 `style` |
| `URL` | §6, the address itself as a string |
| `VERTICAL_ALIGN` | §3 `verticalAlign` |

Types whose shape is only here:

| Type | Shape |
|---|---|
| `ABSOLUTE_SIZE` | `{ size: N }` |
| `CONTENT_DIRECTION` | `{ direction: "ltr" \| "rtl" }` |
| `DISPLAY_TOGGLE` | `{ isDisplayed: true \| false }` |
| `GRID_SIZE` | `{ rowCount: N, columnCount: N }` |
| `HEIGHT` | `{ height: N \| "auto", autosizeKey: "…" }` |
| `HEIGHTV2` | `{ settingType: "manual" \| …, height: N, autosizeKey: "…", mobile: { height: N } }` |
| `ICON` | `{ url: "<HTTPS URL>", fileName: "…" }` |
| `ICON_DISPLAY` | `{ position: "EMPTY" \| … }` |
| `IS_MOBILE_ADAPTIVE_ENABLED`, `VISIBILITY_TOGGLE` | `true` \| `false`, no wrapper |
| `NUMBER` | `{ number: N }` |
| `PADDINGS` | `{ vertical: N, horizontal: N }` |
| `PRICE` | `{ text: "…", currency: "₽" }` — `text` is markup, see §1 |
| `SIZE` | `{ type: "inherit" \| …, width: N, equalizedImageMaxWidth: "…", mobile: { … } }` |
| `SIZE_IN_PERCENTS` | `{ widthInPercents: N }` |
| `TEXT` | `{ text: "<p>…</p>" }` — markup, see §1 |
| `TEXT_SIZE` | `{ containerHeight: N, containerHeightMobile: N }` |
| `TIMER`, `VIDEO` | a large settings object this file does not spell out: change a field the email already prints, otherwise the setting is changed in the editor |
| `VERTICAL_SPACING` | `{ top: N, bottom: N }` |
