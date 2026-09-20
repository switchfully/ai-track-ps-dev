# Copilot in Your IDE: Where to Find Things

Copilot works differently depending on which IDE you use. This page is a quick reference so you
don't waste workshop time hunting for a button. Pick your IDE below.

## IntelliJ IDEA

**Installing the plugin (if not already installed):** Settings/Preferences → Plugins → search
"GitHub Copilot" → Install → restart IntelliJ → sign in with your GitHub account when prompted.

**Opening Copilot Chat:** a Copilot icon appears in the right-hand tool window bar, or use the
menu: Tools → GitHub Copilot → Open Chat. There's also a default keyboard shortcut, usually
`Ctrl+Shift+I` (`Cmd+Shift+I` on Mac), but check Settings → Keymap if that doesn't respond, as
it can vary by IntelliJ version.

**Switching to agent mode:** inside the Chat panel, look for a mode selector near the input
box, usually a dropdown labeled "Ask" or "Agent." Agent mode in IntelliJ can read and edit
multiple files and run Maven/Gradle commands with your approval.

**Inline suggestions:** these appear automatically as gray "ghost text" while typing. Tab
accepts, Esc dismisses. If you see nothing, check Tools → GitHub Copilot → make sure it's
enabled for the current file type.

**Configuring MCP (Block 3):** Settings → Tools → GitHub Copilot → MCP → Configure. See
`what-is-mcp.md` for what you're actually connecting to and why.

## Visual Studio

**Installing (if not already installed):** Extensions → Manage Extensions → search "GitHub
Copilot" → Install → restart Visual Studio → sign in with your GitHub account.

**Opening Copilot Chat:** View → GitHub Copilot Chat, or the Copilot Chat icon in the top
toolbar. Default keyboard shortcut is usually `Ctrl+\, Ctrl+\` (yes, twice), but this varies by
version, check View → Copilot Chat if unsure.

**Agent mode:** the Chat panel has a mode selector, look for "Ask," "Edit," or "Agent." Agent
mode in Visual Studio can edit multiple files across your solution and run build/test commands.

**Inline suggestions:** enabled by default once signed in, shown as ghost text while typing.

**Configuring MCP (Block 3):** Visual Studio auto-detects an `.vscode/mcp.json` file if one
exists in your solution, or you can configure it manually via Tools → Options → GitHub Copilot
→ MCP Servers. See `what-is-mcp.md`.

## VS Code

VS Code is the IDE most of today's written materials assume, so the block-by-block prompt
ladders already walk through Copilot Chat and agent mode directly where needed. As a quick
reference:

**Opening Copilot Chat:** the chat icon in the Activity Bar (left side), or `Ctrl+Alt+I`
(`Cmd+Ctrl+I` on Mac).

**Agent mode:** a mode dropdown at the top of the Chat panel, select "Agent."

**Configuring MCP (Block 3):** Command Palette → "MCP: Add Server," or edit `.vscode/mcp.json`
directly.

## Keyboard shortcuts at a glance

| Action | IntelliJ | Visual Studio | VS Code |
|---|---|---|---|
| Open Copilot Chat | Ctrl+Shift+I | Ctrl+\, Ctrl+\ | Ctrl+Alt+I |
| Accept inline suggestion | Tab | Tab | Tab |
| Dismiss inline suggestion | Esc | Esc | Esc |

Shortcuts can be remapped or may differ slightly by plugin version. If a shortcut doesn't work,
use the menu path listed above instead, and check your Keymap/Options settings.

## If Copilot isn't showing up at all

Confirm you're signed in (look for your GitHub avatar in the Copilot icon or status bar), and
confirm your organization's Copilot license is active. If neither resolves it, see
`../../ai-tooling-fallback.md`.
