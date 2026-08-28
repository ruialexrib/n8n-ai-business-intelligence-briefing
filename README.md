<div align="center">

# n8n AI & Business Intelligence Briefing

### Self-hosted automated briefing for AI, Analytics, and Business Intelligence news

[![n8n](https://img.shields.io/badge/n8n-Workflow%20Automation-EA4B71?logo=n8n&logoColor=white)](https://n8n.io/)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)
[![Ollama](https://img.shields.io/badge/Ollama-Local%20LLM-black)](https://ollama.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

**Automated monitoring · Local LLM · Executive briefing · SMTP delivery**

</div>

---

## About

This project provides a self-hosted **n8n workflow** that monitors authoritative Artificial Intelligence, Analytics, and Business Intelligence sources, asks a local Ollama model to prepare a professional HTML executive briefing, and delivers it by email through SMTP.

The complete workflow runs on your own infrastructure: **n8n** handles orchestration, **Ollama** generates the summary locally, and **SMTP** delivers the finished briefing.

---

## Highlights

- Daily scheduled collection of AI, Analytics, and BI news
- Parameterised list of 12 authoritative sources
- Configurable recipient list
- Local summary generation through Ollama
- Professional email-compatible HTML briefing
- Persistent n8n and Ollama Docker volumes
- Sanitised workflow export without credentials or personal email addresses

---

## Architecture

```text
Authoritative Sources
        │
        ▼
       n8n
  Schedule + Collect
        │
        ▼
      Ollama
    Local LLM
        │
        ▼
 HTML Executive Briefing
        │
        ▼
       SMTP
        │
        ▼
     Recipients
```

- Named Docker volumes preserve n8n configuration and downloaded Ollama models.
- The workflow calls Ollama at `http://ollama:11434` on the private Compose network.

---

## Technology Stack

| Technology | Purpose |
| --- | --- |
| **n8n** | Scheduling and workflow orchestration |
| **Ollama** | Local LLM execution |
| **Docker Compose** | Self-hosted runtime and persistence |
| **SMTP** | Email delivery |
| **HTML** | Executive briefing presentation |

---

## Project Structure

```text
.
├── docker-compose.yml
├── .env.example
├── workflows/
│   └── n8n-ai-business-intelligence-briefing.json
├── LICENSE
└── README.md
```

---

## Getting Started

### Requirements

- Docker Desktop with Docker Compose
- SMTP account
- Enough memory for the selected Ollama model

### Start the Stack

```powershell
Copy-Item .env.example .env
# Replace N8N_ENCRYPTION_KEY with a long random value.
docker compose up -d
docker compose exec ollama ollama pull llama3.2:3b
```

Open n8n at `http://localhost:5678`, create the initial owner account, and import `workflows/n8n-ai-business-intelligence-briefing.json`.

---

## Configuration

1. Open **Configure Sources and Recipients** and replace the example recipient addresses.
2. Review the source list and selected Ollama model.
3. Open **Send Email Digest**, configure the SMTP credential, and replace the example sender address.
4. Execute the workflow manually and inspect the generated email.
5. Publish or activate the workflow after the test succeeds.

Credentials are intentionally absent from the exported workflow. n8n stores them encrypted in its persistent volume using `N8N_ENCRYPTION_KEY`.

### Change the Model

```powershell
docker compose exec ollama ollama pull <model-name>
```

Then update `llm.model` in **Configure Sources and Recipients**. Keep it aligned with the intended `OLLAMA_MODEL` value documented in `.env`.

---

## Useful Commands

```powershell
docker compose ps
docker compose logs -f n8n ollama
docker compose stop
docker compose down
```

`docker compose down` preserves named volumes. Adding `--volumes` deletes n8n configuration, credentials, execution history, and downloaded Ollama models.

---

## Security

- Never commit `.env`, SMTP credentials, real recipient addresses, or exported credentials.
- Pin container image versions before production deployment.
- Configure HTTPS and `N8N_SECURE_COOKIE=true` before exposing n8n beyond localhost.
- Review generated summaries against the linked source articles before using them for decisions.

---

## License

Distributed under the [MIT License](LICENSE). Copyright © 2026 Rui Ribeiro.
