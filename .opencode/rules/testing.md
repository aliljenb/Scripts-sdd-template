# Testing

- Use Playwright for UI/end-to-end tests whenever the behavior under test involves the browser or user-facing UI.
- Prefer Playwright over alternative browser automation tools.
- UI acceptance criteria should be covered by Playwright tests where technically applicable.
- Do not introduce another browser-testing framework without approval.
- Do not use OpenCode's built-in `browser` tool, Claude in Chrome, or any other agent-driven browser automation for UI testing (both are denied in `opencode.json`).