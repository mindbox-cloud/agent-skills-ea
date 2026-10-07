---
description: "The divider line: attributes and their value shapes, the defaults, why unequal border sides give no line"
---

# Divider — `<Divider />`

A horizontal line across the column it sits in. No content, written as a single tag.

The line is drawn only when all four sides of `border.size` are equal; a value with different sides is accepted
without an error and draws nothing. `border={{ type: "none" }}` draws nothing as well, leaving only the spacing.

A bare `<Divider />` draws a 1px solid `#000000` line with 20px above and below. `border` merges onto that default
field by field: `border={{ color: "#cfcfcf" }}` recolours the line, while a partial `size: { top: 2 }` makes the sides
unequal.

## Attributes

| attribute | value | default |
|---|---|---|
| `innerSpacing` | `{ top: N, bottom: N, left: N, right: N, mobile: { … } }` — the space around the line, in px; any side may be omitted | `{ top: 20, bottom: 20, left: 0, right: 0 }` |
| `border` | `{ type: "none" \| "solid" \| "dashed" \| "dotted", color: "#RRGGBB", size: { top: N, right: N, bottom: N, left: N } }` with one `N` on all sides | `{ type: "solid", color: "#000000", size: 1 on all sides }` |

Plus `visibilityOnDevices`, as on any element.

Inside a product row card a divider may also carry `url`, the product chip — [product-rows.md](../product-rows.md).
