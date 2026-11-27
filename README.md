# Taskpocket

A pocket to-do app: make a topic for each area of life or work, keep a numbered to-do list in each, and drag items to set their order. It's a companion to [Wordpocket](https://github.com/kaanonuk/wordshelf) and shares its look and structure.

**Status:** working prototype, used as a Home Screen web app on iPhone.

## Features

### By topic
- One numbered to-do list per topic. The two most recently used topics show as tabs, the rest are in a searchable **All topics** list (most recent first, with open count and last-used date).
- **+ Topic** opens a name field. Adding a name you already have just opens that topic.
- Tap a topic's title to open its panel: **Rename** and **Delete** (asks for confirmation, with the number of to-dos it will remove).

### To-dos
- **Add:** type and press Enter or Add. The field stays focused for quick entry, and new to-dos go to the bottom.
- **Reorder:** drag the **⋮⋮** handle. Other items slide out of the way, numbers update live, and the page auto-scrolls near the top or bottom edge. Works with touch and mouse. With a keyboard, focus a handle and use ↑/↓.
- **Complete:** tap the circle. Done items move to a collapsible **Done** list (newest first). Tap again to restore (it goes back to the bottom of the list). **Clear done** removes them all.
- **Edit:** tap a to-do's text, then press Enter or tap away to save (Esc cancels).
- **Delete:** **×**, with a second tap to confirm.

### All to-dos
- Every open to-do across all topics, grouped by topic (most recent first), with search. Tap a topic name to jump to it.

### Backup & restore
- **Automatic phone copy** in the browser's storage, offered for restore if the account ever comes back empty.
- **Download backup file:** a CSV (topic, to-do, done, position, created, completed) that opens in Numbers or Excel and is saved to the Files app.
- **Restore from file:** brings a backup back, skipping to-dos that already exist in the same topic.

## How it runs

`index.html` is a single self-contained page: HTML, CSS and plain JavaScript, with no framework and no build step. It's published as a Claude artifact and uses the artifact runtime (`window.claude.use(...)`):

| Capability | Used for |
|---|---|
| `db` + `user` | Saving topics and to-dos privately per user, synced across devices |
| `downloads` | Saving the CSV backup file |

Opened outside claude.ai, the page still works and saves to `localStorage` on that device.

Drag-to-reorder uses pointer events on the handle (with `touch-action: none`), not HTML5 drag-and-drop, which doesn't work on iPhone. A reorder writes one record: the moved item gets an `order` halfway between its new neighbours, and the list is renumbered only if the gap gets too small.

## Data model

Each record is a document in `data/users/<userId>/`:

```js
// topic
{ id, kind: "topic", name, created, opened? }
// to-do
{ id, kind: "item", topicId, text, done, order, created, doneAt?, moved? }
```

A topic's "last used" time is the latest of when it was opened and when one of its to-dos was added, completed or moved.

## Install on iPhone

1. Open the Taskpocket artifact link in Safari and sign in to Claude.
2. Tap **⋯** → **Share** → **Add to Home Screen**, check the name says **Taskpocket**, then **Add**.

The bottom of the app should say "Synced to your Claude account." For the custom icon (`assets/icon-1024.png`), use the Shortcuts app: an **Open URLs** shortcut with the artifact link, added to the Home Screen with the icon as its photo.
