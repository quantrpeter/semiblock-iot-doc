# Widget Layout, Sizing, Colours, and Reordering

## Grid and spanning

The dashboard uses a simple responsive CSS grid (usually 4 columns on desktop). Each widget declares a `col_span` from 1 to 4:

- 1 = quarter width (good for single-value or narrow series)
- 2 = half width (most common)
- 3 = three-quarters
- 4 = full width (great for important overview charts or very long time series)

The height of the chart area inside the card is also configurable per widget (default 160 px, with presets up to several hundred pixels). Taller cards are useful when you want to see more vertical detail or a legend without scrolling.

## Colour

Each widget has its own colour (chosen from a pleasant preset swatch or a custom picker in future). The colour is used for:

- The line / bar / segment colour in the chart.
- The small accent on the widget header.
- The background tint under line charts.

Having per-widget colours makes it easy to keep "temperature" traces consistently red/orange across multiple dashboards even when the underlying devices differ.

## Reordering

Widgets are rendered in the order they are stored in the `iot_dashboard_widgets` table (ordered by a `sort_order` or simply by creation time + id, depending on the current implementation). Drag-and-drop reordering in the UI updates this order for the current user.

There is no concept of "pages" or "tabs" of dashboards today — everything lives on the single personal dashboard. If you accumulate dozens of widgets, use the width/height controls and logical grouping (all greenhouse sensors together, all battery levels in a narrow strip at the bottom, etc.).

## Responsiveness

On narrower screens the grid gracefully collapses. A 3-span widget may become full-width; very tall cards may need internal scrolling or the user may choose to edit them to be shorter for mobile viewing. The current design is desktop-first (matching the editors themselves).

## Persistence

All layout choices (span, height, colour, order, title) are stored per widget row and are therefore durable across logins and browser restarts. They are private to the owning user; there is no "share this exact dashboard layout" feature yet.