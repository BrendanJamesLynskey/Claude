# Claude Code headless as a measurement arm

How to use `claude -p` on a subscription as one arm of an experiment, make it behave as much like a bare API call as the CLI allows, check that it did, and say honestly what still differs. Written from a real experiment (CVDP RTL benchmark, "M3i"). Companion to the [Max-plan field report](../max-plan-report/).

*Written 2026-11-02 against Claude Code 2.1.310. Flags and terms change; check the current documentation before relying on any of this.*

## 1. Why use the headless CLI as an arm

An experiment that compares agents or prompts needs a fixed model, many repeat runs and no surprises. Running Claude Code headless (`claude -p`) on a subscription gives:

- a fixed, named model (`--model`);
- hundreds of runs with no per-call bill (usage comes out of the plan's limits instead);
- a real, widely used agent harness as a comparison point.

The cost is control. Claude Code adds its own system prompt, tools, skills, settings and memory unless you switch them off, so by default the arm measures "Claude Code as shipped", not "the model".

## 2. Making it approximate an API call

The plain profile in the CVDP experiment used this command (task text elided):

```bash
claude -p "$TASK" --output-format stream-json --verbose --max-turns 60 --permission-mode acceptEdits \
  --system-prompt "$SYSTEM" --tools Read,Write,Edit,Bash \
  --allowedTools Read Write Edit Bash \
  --disallowedTools "Bash(curl:*)" "Bash(wget:*)" "Bash(git:*)" "Bash(pip:*)" \
  --setting-sources "" --strict-mcp-config --mcp-config '{"mcpServers":{}}' \
  --disable-slash-commands --safe-mode --model claude-haiku-5-5
```

with a clean environment:

```bash
HOME="$(mktemp -d)"            # no user settings, skills, memory or CLAUDE.md can exist
CLAUDE_CODE_DISABLE_AUTO_MEMORY=1
unset CLAUDE_CONFIG_DIR
```

| Flag | Why |
|---|---|
| `--system-prompt` | Replaces Claude Code's system prompt. `--append-system-prompt` would add to it. |
| `--tools Read,Write,Edit,Bash` | Limits the tool set itself, not just permissions. |
| `--allowedTools` / `--disallowedTools` | Permissions; keep both arms' denials identical. |
| `--setting-sources ""` | Load no user, project or local settings. |
| `--strict-mcp-config` + an empty `--mcp-config` | No MCP server from any source. |
| `--disable-slash-commands` | Drops bundled skills and commands. In the experiment `--safe-mode` alone left 19 skills. |
| `--safe-mode` | Starts with CLAUDE.md, skills, plugins, hooks, MCP, custom agents and output styles off. |
| empty `HOME`, auto-memory off | Nothing from the user's own setup leaks in. |
| `--model`, `--max-turns` | Pin the model and cap the turns. |
| `--output-format stream-json --verbose` | A machine-readable event stream, starting with an `init` event. |

**Pin the CLI version** (here 2.1.310, installed from npm and recorded in the image). The flags above were read from that version's `--help`; a later version may behave differently.

**Why not `--bare`?** The headless page recommends `--bare` for scripted calls, but also says that in bare mode Claude Code "never reads OAuth credentials" and uses an API key instead ([headless docs](https://code.claude.com/docs/en/headless)). The experiment ran on a plan token, so `--bare` was not usable; each thing it switches off was covered by a separate flag above.

The harness code is [`agent/cvdp_agent/claude_code.py`](https://github.com/BrendanJamesLynskey/CVDP_RTL_Agent/blob/m3i-plain/agent/cvdp_agent/claude_code.py) (`plain_command`, `plain_env`).

## 3. Check that it took effect

Never assume the flags worked. The first event in the stream is `system`/`init`; it lists the model, tools, MCP servers and skills. Read it as it arrives and kill the run on any mismatch:

```python
def profile_problems(ev):
    if ev is None:
        return ["no init event"]
    out = []
    if sorted(ev.get("tools") or []) != sorted(["Read", "Write", "Edit", "Bash"]):
        out.append("wrong tool set")
    if ev.get("mcp_servers"): out.append("MCP server present")
    if ev.get("skills"):      out.append("skills present")
    return out
```

Also:

- Record in every result row the tools, the skill and MCP counts, the model from `init`, and the sha256 of the system prompt.
- Give a failed check its own stop reason (`profile_error`) and treat the row as **not run**, not as a failure.
- Because `init` is printed first, a killed run has changed nothing yet.

In the experiment all 392 plain-profile runs showed exactly the four tools, 0 skills, 0 MCP servers, the pinned model and the same prompt hash.

## 4. The residual: what still differs from a bare API call

Reading the request body Claude Code sent to a local capture server (no model call) showed what the flags do not remove. State these as known differences:

1. Two fixed system lines before your prompt (a billing/entrypoint header and "You are a Claude agent, built on Anthropic's Claude Agent SDK.").
2. An environment message (working directory, platform, shell, date, model name).
3. An attribution `<system-reminder>` in the first user turn.
4. Claude Code's own tool names, JSON schemas and descriptions for Read, Write, Edit and Bash.
5. A placeholder tool for deferred tool loading, not listed in `init`.
6. Thinking and effort defaults, plus a context-management edit. Effort was not passed, so it is "the CLI default", recorded as such and not asserted.
7. Built-in agent definitions and plugins listed in `init` that add no usable tools.
8. Auto-compaction, retries and the permission system.
9. Time: arms run on different days can see different model serving.

So the arm is "Claude Code stripped down", not "the API with four tools". Put the list in the pre-registration and repeat the relevant items beside every headline number.

## 5. Measuring it

- **Paired design.** Run both arms on the same problems and compare per problem, with a paired bootstrap interval and an exact sign test on the problems that differ.
- **Held-out set.** Decide the primary comparison on problems not used to develop anything; use the development set only as a sign check.
- **Pre-register** the arms, the flags, the known differences, the decision rule and the expected power before the first scored run.

Worked example (M3i, Haiku 5.5, Claude Code 2.1.310): default Claude Code (D) against the plain profile with the LangGraph agent's own prompt and four tools (P), 100 held-out problems, 3 runs per arm.

| | D | P | P minus D [95% CI] | better / worse / same |
|---|---|---|---|---|
| held-out pass@1 | 58.3% | 58.0% | -0.3 [-4.0, +3.3] | 9 / 11 / 80 |

No detectable effect, and P used about a third of D's prompt tokens (37%). Full write-up: [M3i report](https://github.com/BrendanJamesLynskey/CVDP_RTL_Agent/blob/m3i-plain/docs/reports/M3i_report.md) and [pre-registration](https://github.com/BrendanJamesLynskey/CVDP_RTL_Agent/blob/m3i-plain/docs/reports/m3i/preregistration.md) (on branch `m3i-plain`, [PR #37](https://github.com/BrendanJamesLynskey/CVDP_RTL_Agent/pull/37), until it merges). Earlier related reports: [M3h](https://github.com/BrendanJamesLynskey/CVDP_RTL_Agent/blob/main/docs/reports/M3h_report.md) and the [Claude Code model ladder](https://github.com/BrendanJamesLynskey/CVDP_RTL_Agent/blob/m3i-plain/docs/reports/claude_code_ladder.md).

## 6. Rate limits

- A plan limit is not a model failure. Detect it (HTTP 429, a `usage_limit_reached` error, or a `rate_limit_event` whose status is `rejected`), label the row `rate_limited`, count it as **not run** and rerun it.
- Never rerun failures; rerun only rows that did not run.
- Run one sweep at a time. In the ladder experiment four models at 4 threads each used about 63% of the 5-hour session limit in 10 minutes.
- Read usage before and after each sweep and write both numbers in the report. Claude Code's own cost figure (`total_cost_usd`) is a client-side estimate at API rates; keep it apart from any real cost and never report it as spend.

## 7. Subscription and API terms

What the official pages say today (checked 2026-11-02; check again, they change):

- The [legal and compliance page](https://code.claude.com/docs/en/legal-and-compliance) says OAuth authentication "is intended exclusively for purchasers of Claude Free, Pro, Max, Ultra, Team, and Enterprise subscription plans and is designed to support ordinary use of Claude Code". It also says advertised plan limits "assume ordinary, individual usage of Claude Code and the Agent SDK".
- The same page says developers building products or services that use Claude, including with the Agent SDK, "should use API key authentication" through the Claude Console or a supported cloud provider, and that routing requests through plan credentials on behalf of other users is not permitted.
- The [headless page](https://code.claude.com/docs/en/headless) describes `claude -p` as the Agent SDK's CLI form for scripts and CI/CD, and says `--bare` does not use subscription login.

This guide draws no conclusion about whether a given experiment fits those terms. The experiment above was one person's own benchmark runs on their own plan, with no product, no other users and no resale. If yours differs, or you are unsure, read the current terms and contact Anthropic.
