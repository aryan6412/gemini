---
name: mcp-builder
description: Design, develop, test, and package Model Context Protocol (MCP) servers and tools in TypeScript/Node.js or Python. Covers stdio and SSE transports, tool definitions with Zod schemas, resources, and Antigravity IDE configuration.
---

# Model Context Protocol (MCP) Server Builder

You are an MCP systems engineer. When creating custom MCP servers to give Antigravity or AI agents access to tools, databases, or APIs, follow this architecture:

---

## 🛠️ 1. Core Server Structure (TypeScript / `@modelcontextprotocol/sdk`)

```typescript
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import {
  CallToolRequestSchema,
  ListToolsRequestSchema,
} from "@modelcontextprotocol/sdk/types.js";
import { z } from "zod";

const server = new Server(
  { name: "custom-mcp-server", version: "1.0.0" },
  { capabilities: { tools: {}, resources: {} } }
);

// 1. Define Available Tools
server.setRequestHandler(ListToolsRequestSchema, async () => ({
  tools: [
    {
      name: "fetch_data",
      description: "Fetches records from the external service by query",
      inputSchema: {
        type: "object",
        properties: {
          query: { type: "string", description: "Search query" },
          limit: { type: "number", description: "Max results", default: 10 }
        },
        required: ["query"]
      }
    }
  ]
}));

// 2. Handle Tool Execution
server.setRequestHandler(CallToolRequestSchema, async (request) => {
  if (request.params.name === "fetch_data") {
    const { query } = request.params.arguments as { query: string };
    return {
      content: [
        {
          type: "text",
          text: JSON.stringify({ results: [`Item for ${query}`] }, null, 2)
        }
      ]
    };
  }
  throw new Error(`Unknown tool: ${request.params.name}`);
});

// 3. Connect via Standard I/O
const transport = new StdioServerTransport();
await server.connect(transport);
```

---

## ⚙️ 2. Registering in Antigravity / Gemini Configuration
In `~/.gemini/config/mcp_config.json`:
```json
{
  "mcpServers": {
    "my-custom-tool": {
      "command": "node",
      "args": ["C:/path/to/custom-mcp/dist/index.js"],
      "env": {
        "API_KEY": "secure_env_var"
      }
    }
  }
}
```

---

## 🧪 3. Best Practices
* **Stdio Hygiene**: Never write logs using `console.log()` inside a stdio MCP server (it corrupts JSON-RPC communication). Use `console.error()` for logging.
* **Error Handling**: Return errors inside tool responses (`isError: true`) with helpful actionable messages rather than crashing the server.
