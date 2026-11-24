# Set up Code Mode in Claude Code

What you get: Claude Code writes a short piece of JavaScript that makes many tool calls in one step (your MCP tools
included), and reads back only the part of the output it needs, instead of asking for each tool call and reading
every result.

## Before you start
- **Claude Code v2.1.323 or later**, the native build, on either release channel.
- **An interactive `claude` session in a terminal.** Code Mode doesn't work in the VS Code panel, the Desktop app or
  cloud sessions.
- **A Claude subscription or Console account.** The Code Mode docs (checked 2026-11-24) say it doesn't work through a
  gateway, Amazon Bedrock, Claude Platform on AWS, Google Cloud's Agent Platform or Microsoft Foundry. The v2.1.331
  changelog adds Code Mode in terminal sessions on Amazon Bedrock, Google Cloud's Agent Platform, Microsoft Foundry
  and Claude Platform on AWS, so on those, update to v2.1.331 or later and check the docs.
- **A supported model:** Claude Opus 5.5, Sonnet 5.5, Haiku 5.5 or Fable 5.2.
- Your organization doesn't use the HIPAA configuration or zero data retention.
- On Windows: Git for Windows is installed.
- Using a mod that hooks tool calls? You need v2.1.327 or later. Using agent teams? v2.1.326 or later.

## Steps
1. **Check your version, and update if it's older than v2.1.323.**
   ```bash
   claude --version
   claude update
   ```
   On the `stable` channel, if the update doesn't reach v2.1.323, wait for `stable` to catch up or switch to `latest`.

2. **Start Claude Code with Code Mode on.** To try it once, set the variable in your shell:
   ```bash
   CLAUDE_CODE_EXPERIMENTAL_CODE_MODE=1 claude
   ```
   On Windows PowerShell:
   ```powershell
   $env:CLAUDE_CODE_EXPERIMENTAL_CODE_MODE = "1"
   claude
   ```
   To turn it on for every new session, save this in `~/.claude/settings.json` (your user settings) instead:
   ```json
   {
     "env": {
       "CLAUDE_CODE_EXPERIMENTAL_CODE_MODE": "1"
     }
   }
   ```
   A project's settings files can't turn it on, so a repository you clone can't switch Code Mode on for you.

3. **Confirm.** Before your first Code Mode session, Claude Code asks you to confirm. Select
   **Yes, turn on Code Mode**. Claude Code restarts.

4. **Give Claude a task with many related tool calls.** Code Mode helps most with a change that repeats across many
   files, a search whose results need filtering before Claude reads them, or several independent commands that can
   run at the same time. The docs' first example:
   ```text
   Read every Markdown file in this project and list the links that point to local files that don't exist.
   ```

5. **Read the code Claude wrote.** In the conversation you see the tool calls (such as an edit's diff), not the code.
   Press `Ctrl+O`, expand a **Code** row to see the JavaScript, and press `Ctrl+O` again to return.

6. **Optional: approve each piece of code before it runs.** Add an `ask` rule for `REPL`, the tool that runs the
   code, in `~/.claude/settings.json`:
   ```json
   {
     "permissions": {
       "ask": ["REPL"]
     }
   }
   ```

7. **Resume with Code Mode on.** Keep `CLAUDE_CODE_EXPERIMENTAL_CODE_MODE` set to `1` when you run
   `claude --continue` or `claude --resume`: with Code Mode off, Claude Code refuses to resume a Code Mode session.

## Check it works
Near the top of the session you should see:
```text
Code Mode (experimental) is on · this session can be resumed only with Code Mode on
```
If you see `Code Mode (experimental) isn't available` instead, the reason follows on the same line (also in
`/status`). "It isn't open to your account yet" on v2.1.323 or later: check your connection and start again; if it
stays, try again another day. For the other reasons, see the troubleshooting table in the Code Mode docs.

## Good to know
- **Your rules still apply to every call.** Each tool call in the code goes through your `deny` and `ask` permission
  rules and `PreToolUse` hooks under the tool's usual name, such as `Bash` or an MCP tool's name. In auto mode, the
  classifier reviews each piece of code, not each tool call, so keep rules or hooks for calls you must control.
- **It isn't a sandbox.** The JavaScript and its file, web and MCP tool calls run outside the sandbox. Only the shell
  commands it runs follow your sandbox settings. To isolate everything, run Claude Code in a container or virtual
  machine.
- **Time limit.** Unless the code can run in the background (start in auto or `bypassPermissions` mode), you wait
  for it, and it stops after ten minutes. Press `Esc` to stop it.
- **Not available in a Code Mode session:** Remote Control, Claude in Chrome and computer use.
- **Turn it off:** remove the variable from your shell and from `~/.claude/settings.json`, or set it to `0`, then
  start a new session.

## Status
**Experimental, off by default** (checked 2026-11-24). The docs say it "may change or be removed in any release".
They don't call it a preview or a beta. It arrived in Claude Code v2.1.323 (12 November 2026); the latest version on
2026-11-24 is v2.1.331.

## Sources
- https://code.claude.com/docs/en/code-mode
- https://code.claude.com/docs/en/env-vars
- https://code.claude.com/docs/en/settings-reference
- https://github.com/anthropics/claude-code/releases/tag/v2.1.323
- https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md
