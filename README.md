# Blue Berry (bb)

This program is influenced by the MCP sdk example, csharp-sdk/samples/ChatWithTools/ChatWithTools.csproj

A simple .NET console application that demonstrates how to combine a large language model (LLM) with **tool-calling**
capabilities using the **Model Context Protocol (MCP)**. The program:

* Connects to an OpenAI (or Cerebras) chat model.
* Starts two external MCP services:
    * **BashServer** – execute shell commands.
    * **FileServer** – read/write/list files.
* Exposes the tools offered by those services to the LLM via function-calling.
* **📊 Tracks token usage and costs** in real-time with comprehensive analytics.
* **🔍 Monitors context utilization** to prevent hitting model limits.
* **📈 Exports telemetry** for production monitoring via OpenTelemetry.
* Enters an interactive REPL where you can ask the model to perform actions, and the results are displayed
  automatically.

## How to create an AI Agent

Start with https://github.com/modelcontextprotocol/csharp-sdk/blob/main/samples/ChatWithTools/Program.cs which has everything you need to get started.
It ties OpenAI Chat APIs to Microsofts MCP Library.   This enables a chat with tool support.   After adding a shell/bash tool - you have everything
you nedd to haved an agent which can help you improve itself.

## Building the project

```bash
# Restore NuGet packages and build
dotnet build
```

## Running the application

### Interactive Mode (REPL)

```bash
# Ensure the required environment variables are set
export OPENAI_API_KEY=your-openai-key   # or CEREBRAS_API_KEY

# Run the console app
dotnet run
```

### Command Line Options

```bash
# Use different model and endpoint
dotnet run --model gpt-4o --endpoint https://api.openai.com/v1 --key your-key

# Get help
dotnet run --help
```

## How it works (high-level)

1. **LLM client** – `OpenAIClient` (or Cerebras) provides the chat model.
2. **Token tracking** – `TokenTracker` monitors usage, costs, and context utilization in real-time.
3. **MCP clients** – `McpClientFactory.CreateAsync` starts the Bash and File servers via a *stdio* transport.
4. **Tool aggregation** – `ListToolsAsync` gathers the available functions from both servers.
5. **Function-calling** – The `IChatClient` is built with `UseFunctionInvocation()` so the LLM can request tool
   execution.
6. **REPL loop** – Reads user input, streams the LLM response, detects `FunctionCallContent` and
   `FunctionResultContent`, prints nicely formatted JSON, tracks costs, and feeds the updates back into the
   conversation.
7. **Analytics** – Session reports and conversation history are automatically saved for analysis.

## Extending the project

* **Add more tools** – Implement another MCP server (e.g., a database client) and add it to the list of tools.
* **Custom pricing** – Update `ModelPricing.cs` with your model's pricing information.
* **Telemetry backend** – Configure OpenTelemetry exporters (OTLP, Jaeger, etc.) to collect traces and metrics.
* **Persist conversation** – Conversations are automatically saved to `.bb-history/` as JSON.
* **Custom UI** – Replace the console REPL with a minimal web UI using ASP.NET Core.
* **Budget controls** – Add spending limits and alerts based on the cost tracking.

## Production Deployment

The application includes comprehensive telemetry and monitoring capabilities:

- **Metrics**: Token usage, costs, context utilization, request counts
- **Tracing**: HTTP client instrumentation, custom spans for LLM operations
- **Logging**: Structured logs with OpenTelemetry integration
- **Cost Monitoring**: Real-time spending tracking with model-specific pricing

Configure your telemetry backend (Jaeger, Prometheus, Application Insights) to receive the exported data.

## License

This example is provided under the MIT License. Feel free to copy, modify, and redistribute.

---
*Happy hacking! 🫐*