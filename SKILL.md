---
name: agent-virtual-environment
description: Use when setting up isolated devcontainers or containerized dev environments for autonomous agents, especially when deciding between Docker and Podman, balancing least-privilege defaults vs host access, or supporting Node.js and .NET toolchains.
---

# Agent Virtual Environment

## Overview
Default to least privilege with explicit, documented escalation. Always separate Docker vs Podman guidance and ask whether the work is greenfield or migration before generating files.

## Inputs to confirm
- Greenfield or migration
- Runtime: Docker or Podman
- Toolchains: Node.js version, .NET SDK version
- Non-root username (default: `vscode`) and UID/GID if custom
- Required host integrations (mounts, sockets, network)

## Workflow
1. Ask greenfield vs migration and list required host access.
2. Choose runtime and use the matching devcontainer template (Podman should be rootless with user namespaces).
3. Apply least-privilege defaults (non-root user, minimal mounts, no privileged mode).
4. Add required toolchains.
5. If escalation is required, document reason, scope, and rollback steps.

## Templates
- Docker: `assets/devcontainer/docker/devcontainer.json` + `assets/devcontainer/Dockerfile`
- Podman: `assets/devcontainer/podman/devcontainer.json` + `assets/devcontainer/Dockerfile`

## Example (greenfield + Docker)
```powershell
New-Item -ItemType Directory -Force -Path .\.devcontainer
Copy-Item .\assets\devcontainer\Dockerfile .\.devcontainer\Dockerfile
Copy-Item .\assets\devcontainer\docker\devcontainer.json .\.devcontainer\devcontainer.json
```

## Quick reference

| Situation | Action |
| --- | --- |
| Greenfield + Docker | Use Docker devcontainer.json and Dockerfile templates |
| Greenfield + Podman | Use Podman devcontainer.json and Dockerfile templates |
| Migration | Capture required mounts/sockets first, then scope them narrowly |
| Needs extra access | Add minimal runArgs and document why + rollback |

## Common mistakes
- Skipping the greenfield vs migration question.
- Using root or privileged mode by default.
- Mixing Docker and Podman guidance into one path.
- Adding broad host mounts without documenting scope and rollback.

## Rationalizations to reject

| Excuse | Reality |
| --- | --- |
| "This is just for a demo" | Demos become defaults; keep least-privilege defaults. |
| "Migration means no permission changes" | Migration still needs scoped access and documentation. |
| "One image is fastest" | Fast is useless if it breaks isolation or security. |

## Red flags - stop and reassess
- "We can harden later" or "just this once".
- Adding `--privileged` or `--cap-add=ALL`.
- Mounting `/` or large host paths without a written reason.
- Skipping explicit Docker vs Podman choice.

## References
- `references/hardening.md` for least-privilege defaults and escalation guidance.
