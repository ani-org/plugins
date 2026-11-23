# ANI Client Agent Plugin

Quick Start

> 
> ANI client agent plugin for DeepSeek Harness (DSH). Discover and call remote Agents via gateway. **Client-only; no inbound requests.**

## Compatibility

| Plugin Version | ANISDK Version | Gateway Version |
| --- | --- | --- |
| 0.1.x | 0.2.x | 0.2.x |

> 
> Minimum DSH requirement: `DSH ≥0.2.2-rc.1`
> Platform: Linux x86_64, glibc ≥2.38, pnpm ≥12

## Installation

1. Download the plugin file ( *.tar.gz) from Releases and unpack plugin to an absolute path, e.g. `/opt/dsh-plugins/ani-client-agent` (read+exec for DSH)
2. DSH Web: `Settings → Plugins → Add Plugin`, input the path and install
3. Refresh page; confirm `@local/ani-client-agent` is enabled> 
> Optional: Agent-assisted install or CLI install (still requires web activation)

## Gateway Setup

1. Go to `Settings → ANI Client` (requires web profile + active session; refresh if missing)
2. Fill gateway: address, control port `17000`, data port `19000`. Disable TLS for internal networks.
3. Click **Connect / Switch** to generate persistent client ID.

## Usage

### `/ani` Commands

```
/ani status                  # Show connection status
/ani discover <keyword>      # Search agents, get agentId
/ani send <agentId> <msg>    # Send message to agent
/ani task <agentId> <taskId> get/cancel
/ani disconnect
/ani cleanup                 # Clear stale deregistration records
/ani reset-id                # Reset client ID
/ani help
```

Example:

```
/ani discover echo
/ani send ani:default/rr-bj-echo-server-001 Hello
```

### AI Native Tool Calling

Natural language prompts trigger `ani_discover` / `ani_send` / `ani_task` / `ani_status` automatically.

> 
> Example: Find the ping port probe agent and scan `10.14.0.2`

### GUI Panel

Buttons in ANI Client settings run equivalent `/ani` commands. **An active session is required.**

> 
> Constraint: Only one gateway connection at a time. Switching deregisters old identity and clears cache.

## Upgrade & Uninstall

- Upgrade: Extract new release to new path → Remove old plugin → Reinstall → Refresh
- Uninstall: Remove from plugin page (auto deregister). Run `/ani cleanup` on deregister failure. Full wipe: delete `~/.dsh/ani-client-agent/state.json`

## Troubleshooting

- Missing ANI tab: Refresh, verify plugin enabled, use web profile
- GLIBC error on binary: Rebuild bridge binary or use `runtimeMode:python`
- Discover OK but send failed: Target AgentCard missing `supported_interfaces`
- Identity conflict: Retry automatically; use `/ani reset-id` if persistent
- Switch gateway deregister fail: `/ani cleanup`, add `--force` when gateway unreachable
- Data plane down: Allow UDP 19000 on firewall

## Runtime Modes (default: `auto`)

- `auto`: Prefer bundled binary, auto fallback
- `binary`: Use bundled binary only
- `python`: Use local Python3.12 with pre-installed ANISDK
- `managed`: Auto-create isolated venv (~100MB extra)