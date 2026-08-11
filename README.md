# n8n AI & Business Intelligence Briefing

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A self-hosted n8n workflow that monitors authoritative AI and Business Intelligence sources, asks a local Ollama model to prepare a professional HTML executive briefing, and sends it through SMTP.

The project runs entirely on your infrastructure: n8n handles orchestration, Ollama generates the summary locally, and SMTP delivers the finished briefing.

## Features

- Daily scheduled collection of AI, analytics, and Business Intelligence news
- Parameterized list of 12 authoritative sources
- Configurable recipient list
- Local summary generation through Ollama
- Professional, email-compatible HTML presentation
- Docker volumes for persistent n8n and Ollama data
- Sanitized workflow export with no credentials or personal email addresses

## Architecture

- **n8n** schedules and orchestrates the briefing.
- **Ollama** runs the local LLM.
- Named Docker volumes preserve n8n configuration and downloaded models.
- The workflow calls Ollama at `http://ollama:11434` on the private Compose network.

## Requirements

- Docker Desktop with Docker Compose
- An SMTP account
- Enough memory for the selected Ollama model

## Project structure

```text
.
|-- docker-compose.yml
|-- .env.example
|-- workflows/
|   `-- n8n-ai-business-intelligence-briefing.json
|-- LICENSE
`-- README.md
```

## Start the stack

```powershell
Copy-Item .env.example .env
# Replace N8N_ENCRYPTION_KEY in .env with a long random value.
docker compose up -d
docker compose exec ollama ollama pull llama3.2:3b
```

Open <http://localhost:5678>, create the initial n8n owner account, and import `workflows/n8n-ai-business-intelligence-briefing.json`.

## Configure n8n

1. Open **Configure Sources and Recipients** and replace the example recipient addresses. The source list and Ollama model are parameterized in this node.
2. Open **Send Email Digest**, create/select an SMTP credential, and replace the example sender address.
3. Execute the workflow manually and inspect the generated email.
4. Publish/activate the workflow only after the test succeeds.

Credentials are intentionally absent from the exported workflow. n8n stores them encrypted in its persistent volume using `N8N_ENCRYPTION_KEY`.

## Change the model

Pull the desired model and change `llm.model` in **Configure Sources and Recipients**:

```powershell
docker compose exec ollama ollama pull <model-name>
```

The `OLLAMA_MODEL` value in `.env` documents the intended model; n8n does not automatically read host environment variables inside Code nodes, so keep both values aligned.

## Useful commands

```powershell
docker compose ps
docker compose logs -f n8n ollama
docker compose stop
docker compose down
```

`docker compose down` keeps named volumes. Adding `--volumes` deletes n8n configuration, credentials, execution history, and downloaded Ollama models.

## Security notes

- Never commit `.env`, SMTP credentials, real recipient addresses, or exported credentials.
- Pin image versions instead of `latest` before production deployment.
- Configure HTTPS and set `N8N_SECURE_COOKIE=true` when exposing n8n beyond localhost.
- Review generated summaries against the linked source articles before making decisions.

## License

Distributed under the [MIT License](LICENSE). Copyright (c) 2026 Rui Ribeiro.
