# Nextcloud MCP Server

This repository provides a production-ready setup for the [Nextcloud Model Context Protocol (MCP) Server](https://github.com/cbcoutinho/nextcloud-mcp-server), enabling Large Language Models (LLMs) to interact with your Nextcloud instance.

It includes:
- **Nextcloud MCP Server**: The core service that bridges LLMs with Nextcloud APIs.
- **Caddy Proxy**: A reverse proxy that adds a layer of security by requiring an `X-API-Key` for all requests.

## Features

- **File Operations**: List, read, and manage files in your Nextcloud.
- **Notes & Calendar**: Access and manage your Nextcloud Notes and Calendar events.
- **Contacts**: Interact with your Nextcloud Contacts.
- **Secure**: Authentication via API Key enforced by Caddy.
- **Easy Deployment**: Fully containerized using Docker Compose.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) and [Docker Compose](https://docs.docker.com/compose/install/)
- A Nextcloud instance
- A Nextcloud App Password (recommended over your main password)

## Setup

1. **Clone the repository**:
   ```bash
   git clone git@github.com:oliveiraigorm/nextcloud-mcp.git
   cd nextcloud-mcp
   ```

2. **Configure Environment Variables**:
   Copy the `.env.example` file to `.env` and fill in your details:
   ```bash
   cp .env.example .env
   ```
   Edit `.env`:
   - `NEXTCLOUD_HOST`: The full URL to your Nextcloud instance (e.g., `https://nextcloud.example.com`).
   - `NEXTCLOUD_USERNAME`: Your Nextcloud username.
   - `NEXTCLOUD_PASSWORD`: A **Nextcloud App Password**. Go to Settings > Security > Devices & sessions in Nextcloud to create one.
   - `MCP_API_KEY`: A secure random key to protect your MCP server. You will need this to authenticate your client.

3. **Start the services**:
   ```bash
   docker-compose up -d
   ```

The server will be available at `http://localhost:8000`.

## Usage with MCP Clients

This server uses the **SSE (Server-Sent Events)** transport.

### Claude Desktop Configuration

Claude Desktop supports SSE. Add the following to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "nextcloud": {
      "url": "http://localhost:8000/sse"
    }
  }
}
```

**Note on Authentication:**
If you are using the Caddy proxy with `MCP_API_KEY`, ensure your client is capable of sending the `X-API-Key` header. If your client does not support custom headers for SSE, you may need to disable authentication in `Caddyfile` or use a local unauthenticated connection if the client is on the same machine.

Alternatively, you can use a wrapper script that adds the header.

### Testing the connection

You can test the connection using `curl`:

```bash
curl -H "X-API-Key: YOUR_MCP_API_KEY" http://localhost:8000/sse
```

## Security

- The MCP server is not exposed directly.
- Caddy acts as a gatekeeper, checking for the `X-API-Key` header.
- Ensure your `.env` file is kept secure and never committed to version control.

## Credits

This project uses the [nextcloud-mcp-server](https://github.com/cbcoutinho/nextcloud-mcp-server) developed by [cbcoutinho](https://github.com/cbcoutinho).
