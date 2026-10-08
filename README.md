# My Agent Setup

## TL;DR

I try to make it easy for agents to check their own work, and I don't want to fix the same mistake twice. I only add extra tools when they have a clear job, and every task gets its own worktree. Agents run on an always-online dev machine that I can access from my computer or phone.

## Make verification part of the setup

I want the agent to start the app, get to the feature, and check its work without needing me to walk it through every step. It finds the setup commands in the repository. If I keep repeating a setup or verification step, I turn it into a script or write it down in the repo.

## Fix recurring mistakes in the project

When I correct an agent, I try to make sure I won't have to make the same correction again. I usually go in this order:

1. remove the problem with better architecture.
2. make it a lint rule or test so CI catches it.
3. make it a skill or rule.

I still review the things that architecture, tests, and rules can't catch.

## Separate the work and keep review focused

Each task gets its own Git worktree, which is basically a separate copy of the repo where the agent can work without touching what I’m doing. I review the changes before they go back to main.

I want a short summary of what changed, which checks ran, and whatever is still open. Nothing gets pushed, merged, or deployed until I say so.

## Write effective project instructions

I use `AGENTS.md` or `CLAUDE.md` to record project rules, setup and verification commands, and conventions the agent cannot reliably infer from the code. This way the agent already has the context on every task and I don’t have to repeat it in each prompt. I keep the file specific, and I edit it whenever it confuses the agent or I catch myself giving the same correction again.

For guidance on writing these files effectively, see [OpenAI's AGENTS.md guide](https://learn.chatgpt.com/docs/agent-configuration/agents-md#layer-project-instructions) and [Anthropic's Write an effective CLAUDE.md](https://code.claude.com/docs/en/best-practices#write-an-effective-claudemd).

## Use tools that help with the task

I only add a skill or an MCP server when it gives the agent something it can’t do well on its own.
Skills are reusable instructions, and MCP servers plug the agent into tools and data.

Two examples from my setup:

- **A skill for visual comparisons:** I use the [Hyperframes skill](https://github.com/heygen-com/hyperframes) to produce short before-and-after videos that make changes easier to review. The [documentation image-loading example below](#a-real-example-documentation-image-loading) shows one use.
- **An MCP server for runtime access:** For supported backend frameworks such as Rails or Phoenix, I use [Tidewave MCP](https://tidewave.hexdocs.pm/mcp.html) to read logs, query the development database, and run code in the app's context.

When I add or change a skill, I run the same task with and without it and see if the result actually gets better.

## A real example: documentation image loading

In [dittofs-web PR #19](https://github.com/marmos91/dittofs-web/pull/19), I worked on reducing the size of documentation screenshots without losing image quality. The task was:

> The documentation images are slow to load. Improve that without reducing image quality.

So the agent had to figure out the build setup and pick the image format, the implementation, and the checks on its own.

The result was smaller lossless WebP files for the images where WebP saved space. I checked desktop and mobile, and verified that the decoded pixels matched the originals exactly.

For visual changes, I use the [Hyperframes skill](https://github.com/heygen-com/hyperframes) to make a short before-and-after video. I used it for the comparison in [PR #19](https://github.com/user-attachments/assets/59a61a45-cd24-40e8-8ac9-c7791de5b63c), so the reviewer could see the difference in documentation image loading.

## Keep agents on a remote dev machine

My agents and projects usually live on a remote dev machine, not my laptop. It stays on when I disconnect, so tasks keep running between sessions.

## Access from my phone

I also use [T3 Code](https://t3.codes/) as an interface for my coding agents, on desktop and through its mobile app ([iOS](https://apps.apple.com/us/app/t3-code-remote-claude-more/id6787819824) / [Android](https://play.google.com/store/apps/details?id=com.t3tools.t3code)). From my phone, I can send tasks, follow progress, and review diffs while the agents run on the remote dev machine.

For terminal access, I use Termius over Tailscale:

```text
Phone: Termius → private connection: Tailscale → dev machine: agent + project in tmux
```

Here's what I use each one for:

- **[Tailscale](https://tailscale.com/docs/reference/ssh-over-tailscale)** puts my phone and dev machine on a private network.
- **[Termius](https://termius.com/)** gives me an SSH terminal on the phone, where I can open the remote session and talk to the agent.
- **[tmux](https://github.com/tmux/tmux/wiki)** keeps the terminal session running if the phone disconnects. The dev machine still needs to stay running.

The phone is good for short tasks and quick check-ins, but for a big code review I’d rather use a bigger screen.

## A useful talk

**Lauren Tan's** talk influenced how I approach verification repeatable, and turning recurring corrections into project checks. [here's her own talk on X](https://x.com/poteto/status/2102050467505430555).

## References

- [Cursor: best practices for coding with agents](https://cursor.com/blog/agent-best-practices) Covers project rules, reusable skills, and connecting tools through MCP.
- [Poteto's feature-map example](https://github.com/poteto/verification-skill-example) Shows how to exercise and check a feature.
- [pstack: verify the result](https://github.com/cursor/plugins/blob/main/pstack/docs/guide/06-verify-and-ship.md) Covers checking behavior in the running app.
- [pstack: encode lessons in structure](https://github.com/cursor/plugins/blob/main/pstack/skills/principle-encode-lessons-in-structure/SKILL.md) Covers enforcing recurring rules in the project.
- [pstack: make it yours](https://github.com/cursor/plugins/blob/main/pstack/docs/guide/09-make-it-yours.md) Explains how to add and evaluate focused skills.
- [pstack: recipes and pitfalls](https://github.com/cursor/plugins/blob/main/pstack/docs/guide/10-recipes-and-pitfalls.md) Includes examples of clarifying tasks and reviewing findings.
