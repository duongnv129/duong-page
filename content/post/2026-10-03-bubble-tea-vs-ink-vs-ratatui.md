---
title: "Bubble Tea vs Ink vs Ratatui: TUI Benchmarks"
subtitle: "I built the same terminal app three times and measured it"
description: "Bubble Tea vs Ink vs Ratatui benchmarked on the same app: startup, memory, CPU per frame and binary size, plus which TUI framework to pick for Go, Rust or Node."
date: 2026-10-03
tags: ["tech"]
chart:
  series:
    - { name: "Bubble Tea", slot: 1 }
    - { name: "Ratatui", slot: 2 }
    - { name: "Ink default", slot: 3 }
    - { name: "Ink tuned", slot: 3, texture: true }
---

I wanted to know which terminal UI framework to reach for, so I wrote the same small app in Go with [Bubble Tea](https://github.com/charmbracelet/bubbletea), in Rust with [Ratatui](https://ratatui.rs/), and in TypeScript with [Ink](https://github.com/vadimdemedes/ink), then drove all three through the same benchmark.

The short version: the two native frameworks are equally fast. Each used about **0.7 ms of CPU per frame**, against 5 to 8 ms for Ink, and both started about 20 times sooner.

- Pick **Bubble Tea** for a Go team.
- Pick **Ratatui** for the smallest and fastest binary.
- Pick **Ink** only when you are already in React and Node.

## Which one should you pick?

### Bubble Tea (Go, Elm architecture, v2.0.10)

Pick it when your team writes Go, or you want a polished-looking TUI fast.

- Most stars of the three (45.2k).
- Wrote the fewest bytes to the terminal: 67 KB, three times less than Ratatui.
- Lip Gloss, Bubbles and Huh cover styling, widgets and forms.
- Fewest commits lately: 19 in 90 days.

### Ratatui (Rust, immediate mode, v0.30.2)

Pick it when raw speed, memory or binary size matter most.

- Fastest startup (8 ms) and smallest footprint (8 MB RAM, 0.6 MB binary).
- Most active project: 308 contributors, 100 commits in 90 days.
- Used by Codex CLI, yazi, gitui and atuin.
- You write your own event loop, and Rust is slower to learn.

### Ink (TypeScript, React, v7.1.1)

Pick it when you already ship a Node CLI and know React.

- Least code: 39 lines for the same app, with Flexbox layout.
- 9.0M npm downloads a week. Claude Code and Gemini CLI were built on it.
- Needs about 100 to 165 MB of RAM and a Node runtime.
- The default 30fps cap showed only 105 of 300 frames.

## At a glance

Pulled from the GitHub, crates.io and npm APIs on 3 October 2026.

| | Bubble Tea | Ratatui | Ink |
|---|---|---|---|
| Language | Go | Rust | TypeScript / JavaScript |
| Model | Elm architecture: Model, Update, View | Immediate mode: redraw every frame in your own loop | React components with Yoga Flexbox layout |
| Latest release | `v2.0.10` (24 Sep 2026) | `v0.30.2` (19 Jun 2026) | `v7.1.1` |
| First released | Jan 2020 | Feb 2023 (fork of tui-rs, 2016) | Jun 2017 |
| License | MIT | MIT | MIT |

| Community | Bubble Tea | Ratatui | Ink |
|---|---:|---:|---:|
| GitHub stars | **45,249** | 22,847 | 40,008 |
| Contributors | 167 | **308** | 181 |
| Commits, last 90 days | 19 | **100** | 86 |
| Releases, last 12 months | 15 | 35* | 15 |
| Open issues | 113 | 135 | **17** |

\* Ratatui releases several workspace crates (`ratatui`, `ratatui-core`, `ratatui-widgets`) separately, which inflates its release count. Go modules publish no download counts; Ratatui has 56.3M total downloads on crates.io and Ink has 9.0M a week on npm.

The ecosystems differ more than the numbers suggest:

- **Bubble Tea:** Lip Gloss, Bubbles, Huh and Wish (SSH apps). Notable apps include Crush, Glow, Gum and gh-dash. Tests use `teatest` golden files.
- **Ratatui:** built-in widgets plus the awesome-ratatui list and templates. Notable apps include Codex CLI, yazi, atuin, gitui and bottom. Tests use `TestBackend` and snapshots.
- **Ink:** ink-ui and ink-testing-library. Gemini CLI runs on a fork of Ink; Claude Code started on Ink and now uses its own renderer.

## Measured: the same app in all three

Each app draws a title and a rounded box showing 30 of 10,000 rows, with a highlighted selection. In the animation test the selection advances every 16 ms for 300 updates. A Python pty driver (120x40) answers terminal queries the way a modern emulator does and records the results. Values are medians of 10 startup runs, 5 animation runs and 3 idle runs.

"Ink tuned" is Ink at 60fps with incremental rendering and the alternate screen. Hover or focus a bar for details.

{{< charts >}}
{{< bar-chart title="Startup to first frame" unit="ms, lower is better" suffix=" ms" note="Median of 10 runs. Node startup alone is about 73 ms." >}}
Bubble Tea = 13.3 | CPU 12 ms
Ratatui = 8.0 | CPU 4 ms
Ink default = 248 | CPU 249 ms
Ink tuned = 232 | CPU 233 ms
{{< /bar-chart >}}
{{< bar-chart title="Peak memory while animating" unit="MB RSS, lower is better" suffix=" MB" note="Idle: 10.9, 7.2, 99.5 and 99.0 MB." >}}
Bubble Tea = 14.5
Ratatui = 8.0
Ink default = 159.8
Ink tuned = 166.4
{{< /bar-chart >}}
{{< bar-chart title="CPU per delivered frame" unit="ms, lower is better" suffix=" ms" note="(Animation CPU minus startup CPU) divided by frames actually drawn." >}}
Bubble Tea = 0.69 | 0.218 s total, 297 frames
Ratatui = 0.71 | 0.218 s total, 300 frames
Ink default = 5.16 | 0.791 s total, 105 frames
Ink tuned = 8.41 | 1.680 s total, 172 frames
{{< /bar-chart >}}
{{< bar-chart title="Frames delivered of 300 updates" unit="frames, higher is better" suffix=" / 300" better="higher" note="Updates arrive every 16 ms. Ink's frame cap drops the rest." >}}
Bubble Tea = 297
Ratatui = 300
Ink default = 105
Ink tuned = 172
{{< /bar-chart >}}
{{< bar-chart title="Bytes written to the terminal" unit="KB for 300 updates, lower is better" suffix=" KB" note="Matters most over SSH and slow links." >}}
Bubble Tea = 67.2
Ratatui = 193.7
Ink default = 293.3
Ink tuned = 370.3
{{< /bar-chart >}}
{{< bar-chart title="Distribution size" unit="MB to ship, lower is better" suffix=" MB" note="Ink tuned shows the Bun single-file bundle." >}}
Bubble Tea = 3.9
Ratatui = 0.6
Ink default = 83.7 | Node runtime (about 61 MB) plus 23 MB node_modules
Ink tuned = 64.5 | bun build --compile output
{{< /bar-chart >}}
{{< /charts >}}

The same numbers as a table:

| Metric | Bubble Tea | Ratatui | Ink default | Ink tuned |
|---|---:|---:|---:|---:|
| Startup to first frame | 13.3 ms | **8.0 ms** | 248 ms | 232 ms |
| Peak memory while animating | 14.5 MB | **8.0 MB** | 159.8 MB | 166.4 MB |
| CPU per delivered frame | **0.69 ms** | 0.71 ms | 5.16 ms | 8.41 ms |
| Frames delivered of 300 | 297 | **300** | 105 | 172 |
| Bytes written to the terminal | **67.2 KB** | 193.7 KB | 293.3 KB | 370.3 KB |
| Distribution size | 3.9 MB | **0.6 MB** | 83.7 MB | 64.5 MB |

> **How to read these numbers.** Ink renders fewer frames than it receives updates, because its default cap is 30fps. Measuring CPU per *delivered* frame keeps that from flattering it. Bubble Tea sends the fewest bytes because its renderer moves the cursor efficiently, which matters most over SSH. All results come from one machine and one scene, so treat them as orders of magnitude, not exact figures.

## Score it for your team

I used a weighted decision matrix. First, a pass/fail gate: if you need a single self-contained binary, Ink is out. Otherwise each framework scores 1 to 5 per criterion, based on the evidence above.

| Criterion | Bubble Tea | Ratatui | Ink |
|---|:---:|:---:|:---:|
| Runtime speed | 4 | 5 | 2 |
| Footprint | 4 | 5 | 1 |
| Smoothness | 5 | 4 | 2 |
| Developer speed | 4 | 3 | 5 |
| Widgets and styling | 5 | 4 | 3 |
| Project health | 4 | 5 | 4 |
| Team language fit (Go team) | 5 | 2 | 2 |

With weights for a Go backend team (speed 20, footprint 10, smoothness 15, developer speed 20, widgets 10, health 10, language fit 15), Bubble Tea comes out on top. Drop language fit to zero and weight speed and footprint heavily, and Ratatui wins instead. Score language fit from a Node team's point of view, and Ink becomes a reasonable choice.

## The same screen, three ways

The render code from each benchmark app: 94 lines of Go, 62 of Rust, 39 of JavaScript in total.

Bubble Tea composes styled strings, and the renderer diffs cells:

```go
func (m model) View() tea.View {
    sel := m.frame % totalItems
    start := max(0, sel-visible+1)
    var b strings.Builder
    for i := start; i < start+visible; i++ {
        if i == sel {
            b.WriteString(m.selected.Render(m.items[i]))
        } else {
            b.WriteString(m.items[i])
        }
        b.WriteByte('\n')
    }
    title := m.title.Render(fmt.Sprintf("Bench frame %d/%d", m.frame, frames))
    v := tea.NewView(title + "\n" + m.box.Render(b.String()))
    v.AltScreen = true
    return v
}
```

Ratatui fills a buffer with widgets inside a loop you own, and only changed cells are written:

```rust
for frame in 0..frames {
    state.select(Some(frame % TOTAL_ITEMS));
    terminal.draw(|f| {
        let [title, body] =
            Layout::vertical([Constraint::Length(1), Constraint::Length(32)]).areas(f.area());
        f.render_widget(Line::styled(format!("Bench frame {frame}/{FRAMES}"), title_style), title);
        let list = List::new(items.iter().map(String::as_str))
            .block(Block::bordered().border_type(BorderType::Rounded))
            .highlight_style(sel_style);
        f.render_stateful_widget(list, body, &mut state);
    })?;
    // wait for the next 16 ms tick, handling key events while waiting
}
```

Ink declares React elements and lays them out with Flexbox:

```jsx
function App() {
  const [frame, setFrame] = useState(0);
  useEffect(() => {
    const t = setInterval(() => setFrame(f => f + 1), 16);
    return () => clearInterval(t);
  }, []);
  const sel = frame % TOTAL_ITEMS, start = Math.max(0, sel - VISIBLE + 1);
  return (
    <Box flexDirection="column">
      <Text bold color="#bb9af7">Bench frame {frame}/{FRAMES}</Text>
      <Box flexDirection="column" borderStyle="round" paddingX={1} width={60}>
        {items.slice(start, start + VISIBLE).map((it, k) =>
          start + k === sel
            ? <Text key={k} bold color="#000" backgroundColor="#7aa2f7">{it}</Text>
            : <Text key={k}>{it}</Text>)}
      </Box>
    </Box>
  );
}
```

## How each one gets pixels on screen

- **Renderer.** Bubble Tea uses a cell-based renderer derived from ncurses, with cursor-movement optimisation. Ratatui double-buffers and writes only the cells that changed. Ink goes from the React reconciler through Yoga layout to string output, with optional incremental line diffing.
- **Frame pacing.** Bubble Tea defaults to 60fps (120 max). Ratatui has none: you decide when to draw. Ink defaults to 30fps (`maxFps`).
- **Synchronized output (DEC mode 2026).** Bubble Tea detects it automatically. Ratatui does not use it by default, but you can add it through the backend. Ink uses it.
- **Event loop.** Bubble Tea and Ink have one built in (goroutines for commands, React hooks and timers). Ratatui brings your own: crossterm, tokio and so on.
- **Distribution.** Bubble Tea and Ratatui ship one static binary (3.9 MB and 0.6 MB). Ink needs the Node runtime (about 61 MB) plus 23 MB of `node_modules`, or a 64.5 MB Bun bundle.
- **Build time, clean / incremental.** Bubble Tea 4.6 s / 0.18 s. Ratatui 12.6 s / 3.4 s with LTO. Ink has none, since it is interpreted.

## Risks to know

1. **Bubble Tea v2 broke v1 code.** The import path is now `charm.land/bubbletea/v2`, and v1 tutorials and community components may not work yet.
2. **Bubble Tea's commit pace is slow** (19 in 90 days), though releases still ship about monthly.
3. **Ratatui is pre-1.0.** Expect breaking changes between minor versions. 0.30 needs Rust 1.86 or newer.
4. **Ink flickers in heavy apps.** Anthropic wrote its own diffing renderer for Claude Code, and Amp left Ink for the same reason. Flicker in tmux is still [an open issue](https://github.com/anthropics/claude-code/issues/37076).
5. **Ink on Bun is no faster.** Startup improved to about 220 ms, but animation used 3.5 s of CPU, about three times Node.

## How I measured

- Identical apps, release builds, stripped binaries, driven through a pty. The driver answers DECRQM, DA1, kitty-keyboard and colour queries so no framework stalls on a query timeout.
- CPU and peak RSS come from `wait4()` rusage. Frames are counted from synchronized-update markers or frame titles.
- Benchmarks ran on an Apple M4 with macOS 26, Go 1.27, Rust 1.90 and Node 26.
- Not measured: Windows, real terminal emulators, input latency, or very large layouts.

## Sources

- [Bubble Tea v2.0.0 release](https://github.com/charmbracelet/bubbletea/releases/tag/v2.0.0) and [What's new in v2](https://github.com/charmbracelet/bubbletea/discussions/1374)
- [Charm v2 announcement](https://charm.land/blog/v2/)
- [Bubble Tea: synchronized output (mode 2026)](https://github.com/charmbracelet/bubbletea/discussions/1320)
- [Ratatui: rendering under the hood](https://ratatui.rs/concepts/rendering/under-the-hood/) and the [Ratatui FAQ](https://ratatui.rs/faq/)
- [The Signature Flicker (Claude Code and Ink)](https://steipete.me/posts/2025/signature-flicker)
- [Glukhov: BubbleTea vs Ratatui (2026)](https://www.glukhov.org/post/2026/02/tui-frameworks-bubbletea-go-vs-ratatui-rust/)
- [ASQ: decision matrix](https://asq.org/quality-resources/decision-matrix)
