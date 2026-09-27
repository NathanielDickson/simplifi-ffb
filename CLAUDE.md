# Claude instructions

## Session title (every turn)

The owner scans the list of Claude sessions to see what needs them without opening each
one. **At the end of every turn, set this session's title to `<status> · <topic>`**, and
keep it current as the work moves:

| Status | When |
| --- | --- |
| `⛔ Blocked: <reason>` | nobody can move until something outside the session changes — a credential, billing, a broken dependency |
| `🟡 Needs you: <action>` | the next move is the owner's — a decision, an approval, a merge, a local run |
| `🔵 Working: <step>` | the next move is this session's — including watching CI or a deploy it started |
| `✅ Done` | nothing is left for this session: the change is merged (and deployed, where the repo deploys), or the question is answered |

- If the session is carrying more than one thing, the most urgent wins: ⛔, then 🟡, then
  🔵, then ✅.
- `<topic>` is what the session is about in a few words, set once and then left alone;
  only the status part changes. Keep the whole title under about 70 characters, e.g.
  `🟡 Needs you: merge #12 · Waiver advice` or `🔵 Working: CI · Replay parser`.
- How: `get_session` with no `session_id` returns this session's id, then
  `set_session_title` (claude-code-remote tools; load them with ToolSearch if they are
  deferred). Make it the last call before the turn's final message.
- If those tools are not available (a local CLI session, for one), skip this silently. It
  must never block or delay a turn, and a session renames only itself.
