# MCP (Model Context Protocol)

## What is it?
MCP (Model Context Protocol) is an open standard created by Anthropic for connecting LLMs with external tools and data sources. It standardizes how AI models access context (files, databases, APIs) and take actions (send emails, run code, query databases). Think of it as "USB-C for AI" — a universal connector instead of custom integrations for each tool.

## Why do we use it?
Previously, every LLM-tool integration was custom-built (different APIs, authentication, data formats). MCP provides a single protocol: any MCP-compatible client can use any MCP-compatible server. This reduces development time, improves interoperability, and creates an ecosystem of reusable tool servers. Companies use MCP to connect LLMs to internal systems without custom code.

## Real-Time Example 1
**Internal Developer Tool** — A company has a GitHub repo, Jira board, and PostgreSQL database. Step 1: Install MCP servers for GitHub, Jira, and SQL. Step 2: Developer asks Claude "What issues assigned to me in Jira that are blocked by PRs on GitHub?" Step 3: Claude queries Jira (via MCP) to get issues. Step 4: Claude queries GitHub (via MCP) to check PR status. Step 5: Claude cross-references and answers. All through one standardized protocol. No custom API integration code needed.

## Real-Time Example 2
**AI-Powered IDE** — VS Code with Claude integration. Step 1: User installs MCP server for file system operations. Step 2: User asks "Refactor this function to use async/await." Step 3: Claude reads the file (MCP filesystem tool). Step 4: Claude writes the refactored code (MCP filesystem tool). Step 5: Claude runs tests (MCP terminal tool). Step 6: Tests pass, file is updated. All file operations go through MCP — the IDE doesn't need custom plugins for each capability.

## Concept Example 1
**MCP Server and Client (Simplified)**
```python
# MCP Server (provides a tool)
# Run as: python mcp_server.py
from mcp.server import Server, stdio_server

class WeatherServer(Server):
    async def handle_call_tool(self, name, arguments):
        if name == "get_weather":
            city = arguments["city"]
            # Simulate API call
            return {"temperature": 22, "condition": "sunny", "city": city}

server = WeatherServer("weather-server")
server.add_tool(
    name="get_weather",
    description="Get current weather for a city",
    parameters={
        "type": "object",
        "properties": {"city": {"type": "string"}},
        "required": ["city"]
    }
)

# Start server
import asyncio
asyncio.run(stdio_server.run(server))
```
**Input:** MCP server defining a "get_weather" tool
**Output:** A server that exposes weather data through the MCP protocol
**Explanation:** The MCP server defines available tools with their schemas (name, description, parameters). Any MCP client (Claude Desktop, custom app) can discover and call these tools without knowing how they're implemented.

## Concept Example 2
**MCP Client (Using MCP Tools)**
```python
# MCP Client (uses any MCP server)
import asyncio
from mcp import ClientSession, StdioServerParameters

async def main():
    # Connect to an MCP server
    server_params = StdioServerParameters(
        command="python",
        args=["weather_server.py"]
    )
    
    async with ClientSession(server_params) as session:
        # Discover available tools
        tools = await session.list_tools()
        print("Available tools:", [t.name for t in tools])
        
        # Call a tool
        result = await session.call_tool(
            "get_weather", 
            {"city": "Tokyo"}
        )
        print(f"Weather in Tokyo: {result['temperature']}°C, {result['condition']}")

asyncio.run(main())
```
**Input:** MCP client connecting to a weather server
**Output:** Client discovers and calls the weather tool through MCP
**Explanation:** The client doesn't import the server's code or know its internals. It just speaks MCP — discovers tools via `list_tools`, calls them via `call_tool`. This is the decoupling that makes MCP powerful.

## Common Mistakes
- Thinking MCP replaces all API integrations — it standardizes them, not eliminates them.
- Building custom MCP servers for every simple task when existing ones work.
- Not securing MCP servers — they run locally and have file system access.
- Confusing MCP with LLM providers (OpenAI vs Anthropic — MCP works with both).
- Over-engineering: start with simple stdio transport, add SSE (Server-Sent Events) when needed.

## How Interviewers Ask This
- "What is MCP and why was it created?"
- "How does MCP differ from custom tool integrations?"
- "What are the components of MCP?"
- "How does MCP improve AI-tool interoperability?"
- "What is the difference between MCP server and client?"

## How To Impress The Interviewer
- Explain that MCP has three layers: Transport (stdio, SSE, WebSocket), Protocol (JSON-RPC messages), and Application (tools, resources, prompts).
- Discuss MCP's discovery mechanism: clients call `initialize` → server returns capabilities → `tools/list` → server returns tool schemas.
- Mention MCP resources vs tools: Resources are data the model can read (files), Tools are actions the model can take (APIs).
- Know that Anthropic open-sourced MCP and it's supported by Claude Desktop, Zed, and VS Code extensions.
- Talk about the security model: MCP servers run locally and the user controls which servers are installed.

## Most Asked Questions

**Q1: What is MCP?**
A: Model Context Protocol — an open standard for connecting LLMs with external tools and data sources. Created by Anthropic. Think of it as "USB-C for AI" — a universal connector instead of custom integrations for each tool.

**Q2: How does MCP work?**
A: MCP uses a client-server architecture. The MCP client (e.g., Claude Desktop) connects to MCP servers (e.g., filesystem server, GitHub server). Servers expose tools and resources. Clients discover and call them. Communication is JSON-RPC over stdio or SSE.

**Q3: What problem does MCP solve?**
A: Before MCP, every tool integration was custom-built (different APIs, auth, formats). MCP standardizes: one protocol, any client can use any server. Example: Instead of building custom GitHub integration for each AI app, you build one MCP GitHub server.

**Q4: What is the difference between MCP and function calling?**
A: Function calling (OpenAI) is an API feature where the model requests function calls. MCP is a protocol for discovering and calling tools across different applications. MCP can use function calling as a transport layer. Example: OpenAI API calls a function; MCP allows that function to be defined once and reused across apps.

**Q5: What are MCP servers and clients?**
A: Server: provides tools and resources (filesystem, database, API). Client: connects to servers and uses their capabilities (Claude Desktop, IDE plugins). Example: A SQL MCP server lets any client run database queries without building the integration.

**Q6: What transport protocols does MCP support?**
A: stdio (subprocess communication, simplest), SSE (Server-Sent Events, for remote servers), WebSocket (future). Example: Local tools use stdio, cloud tools use SSE.

**Q7: What is the difference between MCP tools and resources?**
A: Tools are actions (callable, may have side effects). Resources are data the model can read (files, database rows). Example: A tool creates a file, a resource reads its contents.

**Q8: How does MCP handle authentication?**
A: Authentication is handled by the MCP server, not the protocol. The server connects to external services using its own auth. Example: A GitHub MCP server stores its own GitHub token, the client doesn't need it.

**Q9: What are the benefits of MCP over custom integrations?**
A: 1) Reusability (one server works with all clients), 2) Standardization (consistent API), 3) Ecosystem (community servers available), 4) Security (contained server process), 5) Simplicity (no custom code for each tool).

**Q10: How do you create an MCP server?**
A: Define tools with name, description, parameters (JSON Schema). Implement handler function. Register with MCP SDK. Example: 50 lines of Python to create a weather server with one tool.

**Q11: What security considerations does MCP have?**
A: 1) Server has local file access — only install trusted servers, 2) Server can make network requests, 3) User controls which servers run, 4) Servers can't access each other. Example: A filesystem MCP server can read/write files — only connect it if you trust the server.

**Q12: How does MCP relate to other protocols like A2A (Agent-to-Agent)?**
A: MCP focuses on model-to-tool communication. A2A focuses on agent-to-agent communication (Google's protocol). They're complementary: agents use MCP for tools, A2A for talking to other agents. Example: A travel agent uses MCP to access flight APIs and A2A to coordinate with a hotel agent.
