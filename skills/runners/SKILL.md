---
name: runners
description: Use when setting up, sizing, checking, moving agents onto or removing a Selan self-hosted runner (Reactor), a Linux server or a Mac a company keeps on so its remote agents start in seconds, or when a runner shows no version, stays offline, or its runs wait queued.
allowed-tools: mcp__selan__selan_remote_runners_list mcp__selan__selan_remote_agent_detail mcp__selan__selan_remote_agents_overview mcp__selan__selan_agent_run_wait Read
---

# Selan runners

A runner is a machine your company keeps on that takes an agent's runs in seconds, each in a
container of its own. The default, a vm made per run, needs nothing from you and leaves nothing
behind; use a runner for speed, your own network or hardware, or a Mac for Xcode builds. Every
run sent to it is handed its agent's secrets, so put only machines you would trust with them.
The full reference is [selan.ai/docs/runners](https://selan.ai/docs/runners); this is the order
that works, and the checks that catch what went wrong before.

## 1. Add it

`selan_remote_runner_create` (owner only) with a name and tags. It answers three install lines;
the token in them is shown once, so keep the answer until the runner is online. A new runner
starts at 2 slots; set the real number with `selan_remote_runner_update` (step 3).

## 2. Install the line that fits the machine

| Machine | Line | Notes |
| --- | --- | --- |
| Ubuntu 24.04 server, amd64 or arm64 | `linux`, as a user who can sudo | Installs Docker, Sysbox and a daily update. Not Ubuntu: Docker first. |
| Mac, Apple Silicon or Intel | `mac`, as the user who logs in, never with sudo | Makes a Lima VM (`selan-runner`, no mounts) of the Mac's own architecture and starts it at login. Size it with `bash -s -- --cpus N --memory GiB --disk GiB`. Needs Lima 2+ or Homebrew. |
| Mac that builds with Xcode | `macPrivileged` (`--mac-host`) | Any run on it can run commands on the Mac as `selan-build`: pin only agents you trust. |

Never install the Linux line straight on macOS or under Docker Desktop: runs need Sysbox, which
only a Linux VM gives. A VM you make by hand instead of the Mac line must have no mounts
(`limactl create … --mount-none`), or every run can read the host's home.

A Mac also needs, outside the line:

- **No sleep.** A sleeping Mac is an offline runner. `caffeinate -ims` as a LaunchAgent keeps
  it awake on power; with the lid closed it needs `sudo pmset -a disablesleep 1`.
- **A login after every reboot.** The VM starts at the user's login, and FileVault stops at its
  own login screen, so a Mac that restarted has no runner until somebody logs in.

## 3. Size the slots

Each run gets its slot's share of the machine: the CPUs, and nine tenths of the memory, divided
by the slots. An agent that builds or runs a browser needs 3-4 GiB of that, so on a 16 GiB VM
keep to 4. A machine that also runs CI or anything else gets fewer.

Size to the agents pinned there: count the `parallelRuns` of those that may run at the same
time. When every listed runner that is online is full, a run waits `queued` on the one with the
shortest queue, and its 4-hour limit runs while it waits; when none is online it ends at once as
`unprovisioned`, unless a Google Cloud project or Selan Isolated VM is also in its Runs on.

## 4. Check it before saying done

1. `selan_remote_runners_list`: the runner is `online`, its `version` is a release number such
   as `1.0.1`, and it reports a `load`. A 40-character commit as its version and a null `load`
   mean it came up on the old runner: Reactor's screen then shows no version and no load. Ask
   Selan to move it to the current runner; it switches at its next daily update, or at once
   with `sudo /usr/local/sbin/selan-runner-install` (on a Mac, inside `limactl shell selan-runner`).
2. One smoke run on it alone: give the runner a tag no other runner carries, then
   `selan_remote_agent_run_start` on a small agent with `runner_tags` set to that tag, and
   `selan_agent_run_wait` until it ends. A run that asks tags ignores the agent's Runs on.

## 5. Move agents onto it

`selan_remote_agent_update` replaces every field it is sent. Read `selan_remote_agent_detail`
first and send name, model, effort, tags, workspace and `systemPrompt` back exactly as they
are, changing only `runnerId`; a prompt retyped is a prompt changed. Then fix any line in the
prompt that names the old machine ("runs on the office server, little memory…"): the agent
believes it. Every update keeps the old version, which `selan_remote_agent_revert` puts back.

## 6. Remove it

Move its agents first: Delete stays off while `agents` is above 0, and that count includes
archived agents and ones deleted in the last 7 days. Delete it in Reactor (no MCP tool removes
a runner); its token is refused from then on. Then remove it from the machine: on a Mac
`limactl stop selan-runner && limactl delete selan-runner` (turn off `--mac-host` first), on
Linux the container, its crontab line, `/etc/selan-runner.env` and the installed script, as the
docs list.
