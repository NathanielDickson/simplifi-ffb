# Claude instructions

## Session title (every turn)

The owner scans the list of Claude sessions to see what needs them without opening each
one. **At the end of every turn, the top-level session sets its own title to
`<status> · <topic>`**, and keeps it current as the work moves:

| Status | When |
| --- | --- |
| `⛔ Blocked: <reason>` | nobody can move until something outside the session changes — a credential, billing, a broken dependency |
| `🟡 Needs you: <action>` | the next move is the owner's — a decision, an approval, a merge, a local run — or the turn ends with work left and nothing running to pick it up (`Needs you: say continue`) |
| `🔵 Working: <step>` | the next move is this session's **and** something is actually running to carry it: a background task, a scheduled check-in, or a PR watch this session holds |
| `✅ Done` | nothing is left for this session: the change is merged (and deployed, where the repo deploys) or dropped, or the question is answered |

- If the session is carrying more than one thing, the most urgent wins: ⛔, then 🟡, then
  🔵, then ✅.
- `<topic>` is what the session is about in a few words. Set it once, and on every later
  turn reuse the text after ` · ` in the current title rather than rewording it from
  memory. Keep the whole title under about 70 characters, e.g.
  `🟡 Needs you: merge #12 · Waiver advice` or `🔵 Working: CI · Replay parser`.
- How: on the first turn, `get_session` with no `session_id` returns this session's id
  and current title (claude-code-remote tools; load them with ToolSearch if deferred).
  Reuse that id afterwards, and call `set_session_title` only when the title would
  change. Make it the last call before the turn's final message.
- **Delegated agents never do this.** A subagent's `get_session` resolves to its parent,
  so a subagent that follows this rule overwrites the parent session's title. Only the
  top-level session renames, and only itself.
- If those tools are not available (a local CLI session, for one), skip this silently. It
  must never block or delay a turn.
