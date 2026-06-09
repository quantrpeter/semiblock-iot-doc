# Chart Types

The widget form offers four visual styles. All of them are rendered with Chart.js on the client.

## Line (default)

Classic sequential plot. X-axis is the reading index (or time if you switch to timeseries). Good for "the last N values".

- Tension 0.4 (slightly smoothed).
- Area fill under the line.
- Best when you just want "recent trend" without caring about exact wall-clock spacing.

## Timeseries

Special line variant that treats the X-axis as real time.

- Uses a linear time scale.
- Gaps between readings are visually correct (a device that sleeps for an hour will show a flat gap).
- Falls back to index if any label is unparseable.
- Recommended for any long-running deployment where you care about "when" rather than "in what order".

## Bar

Vertical bars, one per reading (or per category when used with doughnut-style data).

- Useful for "compare the last few samples side by side".
- Also works well when the "labels" are discrete categories rather than timestamps (the widget form currently always supplies time labels; future categorical widgets may expose this more naturally).

## Doughnut

Pie/doughnut chart.

- Uses the most recent reading's values across multiple keys, or the last N readings' values for a single key (depending on how the widget was configured).
- Colour palette is fixed and pleasant for small numbers of segments.
- Excellent for "current distribution" (e.g. last-known state of several digital inputs, or binned sensor values).

## Choosing the right one

| Goal                              | Recommended type |
|-----------------------------------|------------------|
| "Show temperature over the last hour" | Timeseries or Line |
| "Compare the last 8 samples visually" | Bar |
| "What is the current split between the three zones?" | Doughnut |
| "I have a device that only reports every 30 min and I want the gaps to look right" | Timeseries |
| "I just want something pretty that moves when data arrives" | Line (default) |

You can change the type of an existing widget at any time via the edit dialog; the underlying data series stays the same.