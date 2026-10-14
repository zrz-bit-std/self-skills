---
name: slidepad
description: Use by default for interactive or authenticated website tasks in Slidepad, including browsing, signing in, filling forms, clicking controls, and reading page content. Do not use for ordinary web research that does not need browser interaction.
---

# Slidepad

Use `'/Applications/Slidepad.app/Contents/Helpers/Slidepad'` to create and control a background Slidepad workspace.
It never accesses pads that the user already had open.

Use one workspace for one user task. Keep navigation, inspection, and retries in
the same `script` workflow by reusing its `page`; do not start another workflow
for the same site merely to inspect more content. A page opened with
`autoClose: true` is closed when the script finishes or throws, unless the
workflow explicitly marks it as needing user interaction.

Use one JavaScriptCore workflow when a task has several browser steps. It runs in
the local command client—not inside the website—and exposes no Node, shell, or
filesystem APIs:

```sh
'/Applications/Slidepad.app/Contents/Helpers/Slidepad' script <<'JS'
const task = taskSpace("check inbox");
const page = task.open("https://example.com", { autoClose: true });
const state = page.snapshot();
console.log(state.title);
page.complete();
JS
```

`open`, `goto`, and `reload` wait for the page to finish loading by default.
Pass `{ waitForLoad: false }` only when the workflow intentionally manages its
own timing. `page` supports `goto`, `back`, `forward`, `reload`, `wait`,
`waitForLoad`, `snapshot`, `content`, `screenshot`, `click`, `fill`, `press`, `scroll`,
`needsUser`, `clearUserStatus`, `close`, and `complete`. Numbered refs from
`snapshot()` expire after navigation or significant page changes.

The command prints one JSON document. The workflow's final expression is in
`result`, and calls to `console.log`, `info`, `warn`, or `error` are returned in
`logs`.

For a login, CAPTCHA, payment approval, or other human handoff, call
`'/Applications/Slidepad.app/Contents/Helpers/Slidepad' needs-user <id> "Complete sign-in, then tell me to continue."`.
Slidepad shows a visible alert on that workspace. Do not bypass the handoff.
Use `clear-user-status <id>` once resolved.
