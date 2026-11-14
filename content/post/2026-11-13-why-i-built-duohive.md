---
title: "From iTerm2 Panes to an AI Agent Team: Why I Built DuoHive"
subtitle: "The story of a pile of split panes, a manager agent, and a very busy October"
description: "The story of DuoHive, my open-source Rust app that runs Claude Code and OpenCode agents as a team: why I built it, what it fixed, and where it's going next."
date: 2026-11-13
tags: ["me", "tech"]
---

Picture my Mac on a busy day. iTerm2, split into panes, with three to five Claude Code and OpenCode sessions running side by side. One is mid-task. One finished ten minutes ago and is waiting for you to notice. One has been asking permission to edit a file since before your coffee went cold.

Which one? You don't know. You start clicking through panes like a detective in a bad movie.

That was my life, juggling 3 to 5 active coding agents at once. And it was the start of a story that ended with me building [DuoHive](https://github.com/duongnv129/duohive).

## Act 1: The chaos

I use Claude Code and OpenCode every day, and three things kept wearing me down:

- **I lost track.** Split panes in iTerm2 won't tell you when an agent needs you. A blocked agent just sits there, quietly wasting your time.
- **I kept repeating myself.** The same kind of work goes to the same kind of agent, like a backend agent per repo. But every new session started from zero.
- **The tools I found didn't fit.** Session managers like Claude Squad and Conductor run tasks in parallel really well. Still, each task is a throwaway session. I didn't want disposable workers. I wanted a team.

## Act 2: The research

Before building anything, I looked around. Surely someone had solved this?

The closest match is [herdr](https://herdr.dev/tui/), an open-source Rust terminal app ([repo](https://github.com/herdrdev/herdr)), and it's genuinely good. It runs your agents in a workspace, tab and pane layout, shows which ones are blocked, working or done, and by its own count detects 22 agent CLIs out of the box. I read its docs and its users' wish lists. Its usage numbers are self-reported, so I won't repeat them here.

But there's a catch for me. herdr helps you manage **panes**. As an engineering manager, I wanted to manage **agents**.

| | herdr | DuoHive |
|---|---|---|
| What you manage | Panes in workspaces and tabs | A team of named agents with roles |
| Instructions | Each agent's own config | Written once, inherited from org to team to role to agent |
| Manager agent | None built in; agents can call its API | Duo, with approval cards for org changes |
| Giving work | You pick a pane | Hand work to a role; a free agent takes it |
| Agent CLIs | 22 | 2 (Claude Code, OpenCode) |
| Remote machines and Windows | SSH machines, Windows in beta | Local only, macOS and Linux |
| Terminal | Its own server, replaces tmux | Sits on top of tmux |

herdr wins on breadth: more CLIs, remote machines, plugins. I'm fine with that. They built a great runtime. I'm building the management layer, the part where you say "ask the backend agent" instead of "go find pane 4".

Reading their users' wish lists even gave me a feature for the DuoHive roadmap: marking an agent as unread, so a finished one can pull your attention back.

## Act 3: Why I built my own

So why not just use herdr and call it a day? Because the best tool isn't always the one that fits *you*. Here's my case:

- **My job is managing, not multiplexing.** The same kind of work goes to the same kind of agent, with the same instructions. I wanted to write those once and pick a teammate, not a task. Standing instructions, a manager agent and approval cards aren't things you bolt onto a pane layout. They're the whole point, so they had to be the foundation.
- **Two CLIs are enough for now.** Claude Code and OpenCode are what I use, so I'm doing those two well first. 22 supported CLIs is impressive, but I'd rather go deep than wide. I do plan to add more, and the design is plug and play, so adding a new CLI is meant to be easy.
- **tmux is the quickest way to get started.** herdr builds its own terminal engine. I don't have the knowledge to build something like tmux, and it would take far too much effort. So I stand on tmux: agents keep running when I quit, and `tmux attach` stays as an escape hatch.
- **It's a pet project.** Half the fun is building exactly the thing I wished existed.

One more thing: I only researched herdr's features. I didn't fork it or clone it. I wanted to build my own solution 100%, from my own problem.

Is DuoHive better than herdr? No, it's different. If you want a fast runtime for lots of agent CLIs, go use herdr. If you want to run your agents like a team and you're the one managing it, that's the itch DuoHive scratches.

## Act 4: The idea

Then the thought that changed everything: **what if agents weren't tasks, but teammates?**

A real team has names, roles and a manager. You don't re-introduce your backend engineer every morning. You say "hey, can you take this?" and they already know the codebase.

So DuoHive shows your agents as an **org chart**. Teams, roles, and agents that stick around, each with its own name, instructions, directory and CLI. At the top sits **Duo**, a manager agent you can just talk to:

> *"What's blocking the Payments team?"*

Duo looks across the whole org and tells you. If it wants to change the org, it hands you a card, and nothing happens until you approve it. One screen, and you can see who's working, who's waiting, and who's done.

## Act 5: The build

Here's the twist: I built it with AI coding agents, and I tried a new way of building software. I didn't start with code. I started with a product requirements doc (PRD), then a UX design, then a plan, and the agents worked from those. The docs stayed in the repo as living documents, so the agents always knew what we were building and why.

The part I'm proudest of is the pace. I [benchmarked three TUI frameworks](/post/2026-10-03-bubble-tea-vs-ink-vs-ratatui/) on October 3, and the repo started the next day. It's written in Rust with Ratatui.

By October 19 the one-screen experience was done, and I ran it for three days without a single `tmux attach`. By October 25 the benchmark was running in CI, and every target for 100 agents passed. Setting goals like "under 1% idle CPU at 10 agents" is nerdy fun, and the nice part is that a computer checks whether I met them.

Agents keep running in tmux even when you quit DuoHive, so closing the app never kills your team.

## Act 6: Where it is now

I use DuoHive every day. I open one screen instead of hunting through panes. I write an agent's instructions once and they stick. I think "ask the backend agent" instead of "start a new task".

It's still **alpha** (v0.1.0-alpha.4) for macOS and Linux, so expect rough edges. It's open source under Apache-2.0, and I'd love to see it grow.

## The big dream

Here's the part that really excites me. Imagine you drop in a story ticket. It splits into small subtasks: backend, QA, frontend. Each one lands with the right agent on your team, with clear acceptance criteria. You review the results instead of doing every step yourself.

I call it an **Engineer Factory**. DuoHive is the first floor of that building, and I'm building the rest.

If you run a lot of agents, come try it. Star the [DuoHive repo](https://github.com/duongnv129/duohive), open an issue, or tell me how you keep your own agents organized. I'd love to hear your story too.
