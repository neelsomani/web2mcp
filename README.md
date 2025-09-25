[![MseeP.ai Security Assessment Badge](https://mseep.net/pr/neelsomani-web2mcp-badge.png)](https://mseep.ai/app/neelsomani-web2mcp)

# web2mcp

A system that automatically generates expressive Model Context Protocol (MCP) implementations for any web application (SaaS or consumer-facing).

## Problem Statement

Design a system that, given any web application (SaaS or consumer-facing), can automatically generate an expressive Model Control Protocol (MCP) implementation.

This is a fairly hard problem for (at least) three reasons:

- **Behavior tree size**: Apps have many buttons and pages. We must traverse at least the important subset of these buttons and pages to establish an application's capabilities.

- **Semantic gap**: While links/buttons can be easily identified, it's not clear when these links/buttons correspond to a meaningful tool to expose via the MCP. Similarly, what textual information on the page is relevant for read operations?

- **Under-specified hierarchy**: We must infer the object hierarchy. What are the "resources" that the MCP should allow agents to act on? Which tools correspond to which resources?

I break the problem into two separate tasks: (1) define the MCP specification and (2) implement the specification.

## Installation

### Prerequisites

1. Python Dependencies (for the test application):
   ```bash
   cd test
   pip3 install -r requirements.txt
   ```

2. Node.js Setup:
   ```bash
   nvm use 18
   npm install
   ```

3. Environment Variables:
   ```bash
   cp .env.example .env
   ```
   
   Then edit `.env` and set the following variables:
   ```bash
   BROWSERBASE_PROJECT_ID="YOUR_BROWSERBASE_PROJECT_ID"
   BROWSERBASE_API_KEY="YOUR_BROWSERBASE_API_KEY"
   OPENAI_API_KEY="THIS_IS_OPTIONAL_WITH_OPENAI_KEY"
   # SPA Crawler Configuration
   SPA_CRAWLER_URL="http://localhost:8000"
   SPA_CRAWLER_USERNAME="example@example.com"
   SPA_CRAWLER_PASSWORD="password123"
   SPA_CRAWLER_MAX_DEPTH="1"
   SPA_CRAWLER_ENV="LOCAL"
   ```
   
   By default, the SPA_* environment variables work for the demo app.

## Getting Started

### Example MCP Implementation

You can try a derived MCP for the demo email application:

1. Run the demo app:
   ```bash
   cd test
   python3 app.py
   ```

2. In a new terminal in the root directory:
   ```bash
   npm run build:mcp
   npx @modelcontextprotocol/inspector node dist/dynamicMcpServer.js test/exampleMcpConfig.json test/exampleSummaryPageActions.json
   ```

3. Try the MCP in the launched browser window

### Example MCP Derivation

1. Start the demo app:
   ```bash
   cd test
   python3 app.py
   ```

2. In the root directory, run the crawler:
   ```bash
   npx tsx spaCrawler.ts
   ```

3. The resulting output is in `outputMcpConfig.json` and `outputSummaryPageActions.json`

4. Use those with the MCP server:
   ```bash
   npm run build:mcp
   node dist/dynamicMcpServer.js outputMcpConfig.json outputSummaryPageActions.json
   ```

## Project Structure

- `spaCrawler.ts` - Main crawler that analyzes web applications and generates MCP configurations
- `dynamicMcpServer.ts` - Dynamic MCP server implementation
- `sharedUtils.ts` - Shared utilities for the project
- `test/` - Demo Flask application for testing
  - `app.py` - Simple email application demo
  - `exampleMcpConfig.json` - Example MCP configuration
  - `exampleSummaryPageActions.json` - Example page action definitions

## How It Works

The system works in two phases:

1. **Analysis Phase**: The `spaCrawler.ts` analyzes a web application by:
   - Crawling through pages and identifying interactive elements
   - Inferring the application's object hierarchy
   - Determining which actions correspond to meaningful MCP tools
   - Generating configuration files that define the MCP interface

2. **Execution Phase**: The `dynamicMcpServer.ts` provides:
   - A dynamic MCP server that loads configurations at runtime
   - Tool execution that translates MCP calls into web application actions
   - Real-time interaction with the target web application

## Development

### Building

```bash
npm run build:mcp
```

### Running in Development

```bash
npx tsx spaCrawler.ts
npx tsx dynamicMcpServer.ts
```

### Testing with MCP Inspector

```bash
npx @modelcontextprotocol/inspector node dist/dynamicMcpServer.js config.json actions.json
```

## License

See [LICENSE.txt](LICENSE.txt) for details.
