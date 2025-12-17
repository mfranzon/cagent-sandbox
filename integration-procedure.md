# Minimal Integration Procedure: Running cagent in Docker Sandboxes

This document outlines the steps to run a cagent agent team (e.g., dev-team.yaml) securely inside a Docker Sandbox environment.

## Prerequisites
- Docker Desktop 4.50+ with sandbox support installed.
- cagent binary available on host (included in Docker Desktop 4.49+).
- A cagent config file (e.g., dev-team.yaml) in the workspace directory.

## Limitations
- `docker sandbox run` currently supports only "claude" or "gemini" agents natively. cagent requires a workaround using a detached sandbox container.
- API keys (e.g., ANTHROPIC_API_KEY) must be set in the container environment.

## Procedure

1. **Create a Detached Sandbox**:
   ```bash
   docker sandbox run -d --name my-cagent-sandbox claude
   ```
   - This creates a container with workspace mounted at the same host path (e.g., /home/user/project).

2. **Copy cagent Binary into the Sandbox**:
   ```bash
   # Find cagent path on host
   which cagent  # e.g., /usr/bin/cagent
   
   # Copy to container
   docker cp /usr/bin/cagent <container-id>:/usr/bin/cagent
   ```
   - Replace <container-id> with the sandbox ID from `docker sandbox ls`.

3. **Set Environment Variables (API Keys)**:
   ```bash
   docker exec <container-id> bash -c "export ANTHROPIC_API_KEY=your_key_here"
   # Or set other required keys like OPENAI_API_KEY
   ```

4. **Run cagent in the Sandbox**:
   ```bash
   docker exec -it <container-id> bash -c "cd /path/to/workspace && cagent run dev-team.yaml"
   ```
   - Replace /path/to/workspace with the mounted workspace path.
   - The agent team will run interactively, with root delegating to sub-agents.

5. **Verify Integration**:
   - cagent should load the config and start the root agent.
   - Agents can access mounted files (e.g., dev-team.md) and use tools like filesystem/shell.
   - On successful API auth, agents will collaborate (e.g., designer creates wireframes, engineer implements).

6. **Cleanup**:
   ```bash
   docker sandbox rm my-cagent-sandbox
   ```

## Expected Outcome
- Secure execution: Agents run isolated in container, no host risks.
- Workspace persistence: Changes to files sync between host/container.
- Collaboration: Multi-agent team functions as designed, with delegation and tool use.

## Future Improvements
- Update Docker Sandbox to natively support "cagent" as an agent type.
- Include cagent in sandbox template images.
- Automate API key injection via sandbox credentials.

## Test Results
- ✅ cagent loads config in sandbox.
- ✅ Workspace mounting works.
- ✅ Agent initialization succeeds (fails only on invalid API key).
- ✅ Tools (filesystem) accessible.

