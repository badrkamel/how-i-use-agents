# My Agent Setup

Make it cheap for an agent to prove its work, then make it impossible for the same mistake to happen twice. Instructions stay short, extra tools serve a specific purpose, and separate worktrees keep tasks apart.

## Make verification part of the setup

The agent needs to be able to start the app, reach the feature, and check the result without asking me to do each step. It finds the setup commands in the repository. Repeated setup and checking steps belong in reusable scripts or project notes.

## Fix recurring mistakes in the project

When I keep correcting the same thing, I look for a change that will catch it on the next task:

- **Improve the structure.**
- **Enforce a rule.**
- **Write down the judgment.**

## Keep instructions short and add tools deliberately

A short `AGENTS.md` or `CLAUDE.md` keeps project rules and key commands available across tasks, so I don't repeat them in every prompt. Both companies have published guidance on writing these files effectively: [OpenAI's AGENTS.md guide](https://learn.chatgpt.com/docs/agent-configuration/agents-md#layer-project-instructions) and [Anthropic's Write an effective CLAUDE.md](https://code.claude.com/docs/en/best-practices#write-an-effective-claudemd).

A specialized skill makes sense for a recurring job with its own procedure, such as [producing a before-and-after video with Hyperframes](#a-real-example-documentation-image-loading). I add skills where a task needs them. When changing one, a useful check is to try the same representative task again and see whether the result actually improves.

For supported backend frameworks such as Rails or Phoenix, I use [Tidewave MCP](https://tidewave.hexdocs.pm/mcp.html) to connect the agent to the running development app. It can read logs, query the development database, and run code in the framework's context. For example, it can investigate why an import failed and check which records were actually saved.

## Separate the work and keep review focused

Each task gets its own Git worktree: a separate working copy where the agent can make changes without mixing them into my current work. I can review those changes before bringing them back into the main branch.

I look for a short explanation of what changed, the relevant checks, and anything left unresolved. Changes stay in the working copy until I explicitly ask for a push, merge, or deployment.

## A real example: documentation image loading

In [dittofs-web PR #19](https://github.com/marmos91/dittofs-web/pull/19), I worked on reducing the size of documentation screenshots without losing image quality. The goal was simple:

> The documentation images are slow to load. Improve that without reducing image quality.

That leaves the agent to inspect the build setup and choose the image format, implementation, and checks.

The change generated smaller lossless WebP images where they saved space. Verification covered desktop and mobile. Separate checks confirmed that the optimized images decoded to exactly the same pixels as the originals.

For changes that are easier to show, I use the [Hyperframes skill](https://github.com/heygen-com/hyperframes) to make a short before-and-after video. I used it for the comparison in [PR #19](https://github.com/user-attachments/assets/59a61a45-cd24-40e8-8ac9-c7791de5b63c), so the reviewer could see the difference in documentation image loading.

## Access from my phone

With Codex/CC/Cursor and the project on a server, the phone connects to the same working session:

```text
Phone: Termius → private connection: Tailscale → server: Codex + project
```

Each part has a simple job:

- **[Tailscale](https://tailscale.com/docs/reference/ssh-over-tailscale)** connects my phone and server over a private network, with access restricted to the devices and users I allow.
- **[Termius](https://termius.com/)** gives me an SSH terminal on the phone, where I can open the server session and talk to the agent.
- **[tmux](https://github.com/tmux/tmux/wiki)** keeps the terminal session running if the phone disconnects. The server still needs to stay running.

The project files and development commands stay on the server. The session can keep working between connections, ready to return to for results or follow-up instructions. The phone is useful for short tasks and updates; a larger screen is easier for substantial code review.

## A useful talk

I took useful ideas from **Lauren Tan**, especially making verification repeatable, keeping skills focused, and turning recurring corrections into project checks. If you'd like to explore her approach, [here's her own talk on X](https://x.com/poteto/status/2102050467505430555).

## References

- [Poteto's feature-map example](https://github.com/poteto/verification-skill-example) Shows how to exercise and check a feature.
- [pstack: verify the result](https://github.com/cursor/plugins/blob/main/pstack/docs/guide/06-verify-and-ship.md) Covers checking behavior in the running app.
- [pstack: encode lessons in structure](https://github.com/cursor/plugins/blob/main/pstack/skills/principle-encode-lessons-in-structure/SKILL.md) Covers enforcing recurring rules in the project.
- [pstack: make it yours](https://github.com/cursor/plugins/blob/main/pstack/docs/guide/09-make-it-yours.md) Explains how to add and evaluate focused skills.
- [pstack: recipes and pitfalls](https://github.com/cursor/plugins/blob/main/pstack/docs/guide/10-recipes-and-pitfalls.md) Includes examples of clarifying tasks and reviewing findings.
