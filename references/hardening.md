# Hardening Checklist (Agent Devcontainers)

## Baseline (least privilege)
- Run as a non-root user; avoid UID 0 in devcontainer.json and Dockerfile.
- Avoid privileged mode, host network, and extra capabilities.
- Mount only the workspace; do not add host mounts unless required.
- Keep image minimal; install only required toolchains.

## Escalation (only when required)
- Document why additional access is needed and how to roll it back.
- If host mounts are required, scope them to specific paths (no `/` mounts).
- If network or device access is required, use the narrowest possible flag.
- Prefer short-lived elevation (runArgs) over permanent Dockerfile changes.

## Docker-specific notes
- Prefer `--security-opt=no-new-privileges`.
- Avoid `--privileged` and `--cap-add=ALL`.

## Podman-specific notes
- Prefer rootless Podman with `--userns=keep-id`.
- Avoid `--privileged` and `--device` unless unavoidable.

## Migration prompts
- Which host paths must remain mounted?
- Which services or sockets (Docker socket, SSH agent) are required?
- What minimum permissions keep existing workflows functional?
