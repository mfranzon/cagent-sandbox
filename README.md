# cagent + Docker Sandboxes

Build and run AI agent teams securely using [cagent](https://github.com/docker/cagent) and Docker Sandboxes.

This repository contains the companion material for the blog post **"Building AI Teams: How Docker Sandboxes and cagent Transform Development"**.

## What's Inside

```
├── blog.md                    # Full blog post
├── dev-team.yaml              # Example agent team configuration
├── integration-procedure.md   # Step-by-step setup guide
└── assets/images/             # Diagrams and screenshots
```

## Quick Start

### Prerequisites

- Docker Desktop 4.49+ with sandbox support
- cagent (included in Docker Desktop 4.49+)
- API key for your model provider (OpenAI, Anthropic, or Google)

### Run an Agent Team

```bash
# 1. Create a sandbox
docker sandbox run -d --name my-cagent-sandbox claude

# 2. Copy cagent into the sandbox
docker cp $(which cagent) $(docker sandbox ls --filter name=my-cagent-sandbox -q):/usr/bin/cagent

# 3. Run your agent team
docker exec -it -e OPENAI_API_KEY=your_key my-cagent-sandbox bash -c \
  "cd /path/to/workspace && cagent run dev-team.yaml"

# 4. Cleanup when done
docker sandbox rm my-cagent-sandbox
```

## Agent Team Structure

The included `dev-team.yaml` defines a 5-agent development team:

| Agent | Role |
|-------|------|
| **root** | Product Manager - coordinates iterations |
| **designer** | UI/UX Designer - creates wireframes |
| **awesome_engineer** | Developer - implements features |
| **qa** | QA Specialist - identifies bugs |
| **fixer_engineer** | Integration Engineer - fixes issues |

## Resources

- [cagent Documentation](https://docs.docker.com/ai/cagent/)
- [Docker Sandboxes Documentation](https://docs.docker.com/ai/sandboxes/)
- [cagent GitHub Repository](https://github.com/docker/cagent)
- [Docker Desktop Download](https://www.docker.com/products/docker-desktop/)

## License

This material is provided for educational purposes.
