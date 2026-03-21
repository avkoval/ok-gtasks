# ok-gtasks Development Guide

## Development Flow

1. **Claude modifies `ok-gtasks.org`** - all code changes go into the literate source
2. **User reviews** the org file changes
3. **User tangles** from their Emacs session: `M-x org-babel-tangle` (or `C-c C-v t`) in `ok-gtasks.org`
4. **User tests** the result, provides feedback
5. Iterate

**Never edit `ok-gtasks.el` directly** - it is generated from `ok-gtasks.org` via tangle.

## Architecture

- Literate programming: `ok-gtasks.org` → tangles to → `ok-gtasks.el`
- Org structure: `*` = task list, `**` = task, `***` = sub-task
- Tracking properties: `GTASKS_LIST_ID`, `GTASKS_TASK_ID`, `GTASKS_PARENT_ID`, `GTASKS_ETAG`, `GTASKS_UPDATED`
- HTTP via curl (primary) or url.el (fallback)
- OAuth2 refresh token in `~/.emacs.d/.gtasks/token.json`
- Sync file: `~/org/gtasks.org` (configurable via `ok-gtasks-file`)

## Key conventions

- All code blocks use `:tangle ok-gtasks.el :results silent`
- Pull = remote wins, Push = update-or-create
- Rate limiting: 300ms throttle on writes, 2s backoff on 403/429
- Heading levels determine push scope (1=list+all, 2=task+subs, 3=sub only)
