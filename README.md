# Kinetic UI

**Design the motion. Transform the experience.**

English · [Türkçe](README.tr.md)

Kinetic UI is a reusable skill for adding motion to existing websites and web apps. Use it with Codex or Claude Code to plan and implement page entrances, scroll effects, interactive feedback and state transitions while keeping your brand, content and working features intact.

It fits landing pages, product pages, portfolios, dashboards and SaaS interfaces. Start with a whole page or limit the request to a single component.

## Choose a motion profile

| Profile | Best for | What to expect |
| --- | --- | --- |
| **Calm** | Dashboards, tables and forms | Short feedback and clear state changes that stay comfortable during long sessions. |
| **Expressive** | Landing pages and product pages | A coordinated hero entrance, section reveals and noticeable interactions. The default profile. |
| **Cinematic** | Creative portfolios and visual stories | More deliberate timing, masked reveals and scroll storytelling where the content supports it. |

Profiles share duration, easing, spring, distance and stagger tokens. The skill adapts the movement to your visual style; it doesn't impose a color palette or typography.

## Install

1. Download the repository with **Code → Download ZIP**, then extract it.
2. Open a terminal in the extracted folder containing `kinetic-ui/`.
3. Copy the **whole `kinetic-ui` folder**, including its references, to your tool's skills directory.

| Tool | All your projects | One project only | Invoke with |
| --- | --- | --- | --- |
| Codex | `~/.agents/skills/kinetic-ui/` | `<project>/.agents/skills/kinetic-ui/` | `$kinetic-ui` |
| Claude Code | `~/.claude/skills/kinetic-ui/` | `<project>/.claude/skills/kinetic-ui/` | `/kinetic-ui` |

For a personal installation, choose one command below. If the skill is already installed, review the existing copy before updating it.

<details>
<summary>Windows — PowerShell</summary>

**Codex**

```powershell
New-Item -ItemType Directory -Force "$HOME/.agents/skills" | Out-Null
Copy-Item -LiteralPath ./kinetic-ui -Destination "$HOME/.agents/skills/kinetic-ui" -Recurse
```

**Claude Code**

```powershell
New-Item -ItemType Directory -Force "$HOME/.claude/skills" | Out-Null
Copy-Item -LiteralPath ./kinetic-ui -Destination "$HOME/.claude/skills/kinetic-ui" -Recurse
```

</details>

<details>
<summary>macOS / Linux — terminal</summary>

**Codex**

```bash
mkdir -p ~/.agents/skills
cp -R ./kinetic-ui ~/.agents/skills/
```

**Claude Code**

```bash
mkdir -p ~/.claude/skills
cp -R ./kinetic-ui ~/.claude/skills/
```

</details>

After copying, open your target project in the tool and invoke the skill. If it isn't listed, restart the session and check that `SKILL.md` sits directly inside `kinetic-ui/`.

For project-only use, copy the folder into the project path in the table instead. Choose one installation scope to avoid duplicate copies.

Other tools can use the instruction pack if they support [Agent Skills](https://agentskills.io). Check their installation path and invocation syntax. `agents/openai.yaml` supplies Codex display metadata; the shared instructions live in `SKILL.md` and `references/`.

Official setup details: [Codex skills](https://learn.chatgpt.com/docs/build-skills#where-codex-loads-local-skills) · [Claude Code skills](https://code.claude.com/docs/en/skills).

## Use it

Open the project you want to improve, then describe the scope and profile. The skill responds in the language of your request.

**Codex**

```text
$kinetic-ui Transform this landing page with expressive motion. Keep the brand,
content and working features. Cover the hero entrance, scroll effects,
interactive feedback and state transitions.
```

**Claude Code**

```text
/kinetic-ui Transform this landing page with expressive motion. Keep the brand,
content and working features. Cover the hero entrance, scroll effects,
interactive feedback and state transitions.
```

You can narrow the request:

- **Dashboard:** “Use calm motion for this orders table and editing drawer. Preserve filters, keyboard navigation and form state.”
- **Portfolio:** “Give this portfolio cinematic motion. Use one main visual scene and keep a normal reading flow on mobile.”
- **One component:** “Animate only the pricing cards. Leave the rest of the page unchanged.”
- **Design only:** “Write a motion specification for the hero. Include timing, triggers and mobile alternatives. Don't edit code.”

Start these requests with `$kinetic-ui` in Codex or `/kinetic-ui` in Claude Code.

## What the skill does

1. Reads the existing design, components, dependencies and project rules.
2. Identifies the main content, important actions and useful animation opportunities.
3. Writes a short motion plan covering purpose, trigger, effect and device alternatives.
4. Defines shared motion tokens and implements the requested scope.
5. Checks responsive behavior, accessibility, cleanup and performance where the project environment allows it.

Simple interactions use CSS or the Web Animations API. React/Next.js projects reuse a compatible Motion setup already in the project. GSAP/ScrollTrigger is an option for more complex timelines and scroll scenes. New dependencies need a concrete benefit, and each animated property has one owner.

Mobile layouts keep natural scrolling and usable controls. Reduced-motion settings replace heavy movement with static content or short fades. Keyboard focus, readable content and functioning actions take priority throughout.

## Files

```text
kinetic-ui/
├── SKILL.md
├── agents/
│   └── openai.yaml
└── references/
    ├── motion-patterns.md
    ├── implementation-guide.md
    └── accessibility-performance.md
```

| File | Purpose |
| --- | --- |
| [SKILL.md](kinetic-ui/SKILL.md) | Core workflow, scope and profile selection. |
| [motion-patterns.md](kinetic-ui/references/motion-patterns.md) | Profile values and animation patterns, including mobile and reduced-motion alternatives. |
| [implementation-guide.md](kinetic-ui/references/implementation-guide.md) | Engine selection, code examples, SSR and lifecycle cleanup. |
| [accessibility-performance.md](kinetic-ui/references/accessibility-performance.md) | Accessibility checks, device adaptation and performance validation. |
| [openai.yaml](kinetic-ui/agents/openai.yaml) | Codex display name, description and starter prompt. |

The skill instructions and technical references are written in Turkish. You can invoke them with either Turkish or English requests.

## Verification

The package structure, YAML metadata and local reference links have been checked. Seven isolated JavaScript tests cover token adapters, reveal behavior, reduced-motion changes, mobile distances, cleanup, remounts and pin conditions. Landing page, dashboard, cinematic portfolio and design-only requests were also reviewed at the plan/specification level.

Browser rendering, real-device behavior and framework integration still need to be checked in the target project. Smooth motion is the goal; frame rate must be measured on the devices that matter to your users.
