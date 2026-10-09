# ssa-watch

Watches open, non-draft GitHub PRs that involve you and (ignoring comments from bot accounts):

- prints them as a table (repo, PR, author, title, review, CI, labels) with clickable links
- sends a macOS notification when a new PR appears
- sends a notification when someone @-mentions you or replies in a review thread you commented in
- on your own PRs, sends a notification for every comment and review (approved, changes requested, commented)
- once one of your PRs has all required approvals, enables auto-merge and notifies you; if someone else left comments (conversation comments, review bodies, or unresolved threads), only notifies you to check them
- auto-approves PRs labelled `ssa-ship` (not your own, only by `-a` authors if any are given, not where you requested changes, and not if they change files under an `-x` path)

## Requirements

- macOS
- [`gh`](https://cli.github.com), authenticated (`gh auth login`)
- `jq`
- Optional: [`terminal-notifier`](https://github.com/julienXX/terminal-notifier) (`brew install terminal-notifier`) so clicking a notification opens the PR or comment

## Install

```sh
ln -s "$PWD/ssa-watch" ~/.local/bin/ssa-watch
```

## Usage

```
ssa-watch -r owner/repo [-r owner/repo]... [-i seconds] [-l label] [-a author]... [-x path]... [-n] [-h]
  -r  repo to watch, repeatable (required)
  -i  poll interval in seconds (default: 60)
  -l  label that triggers auto-approve (default: ssa-ship)
  -a  only auto-approve PRs by this author, repeatable (default: any author)
  -x  never auto-approve PRs changing files under this path, repeatable
  -n  dry run: log instead of approving or enabling auto-merge
  -h, --help  show this help
```

Each kind of notification plays its own sound; change them via the `sound_*` constants at the top of the script.

State lives in `~/.cache/ssa-watch`. The first run records existing PRs and comments without notifying.

## Limits

- At most 30 PRs are watched, which keeps each poll at about 20 GraphQL points.
- "Replies" only covers review threads. Conversation-tab comments notify only when they mention you.
