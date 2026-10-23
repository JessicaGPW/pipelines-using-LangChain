# Pipelines using LangChain

[Leia este README em português](README.md)

An introductory project demonstrating a message-processing workflow with **LangGraph**, using nodes and conditional routing. The application receives text through an API, detects whether the request is to convert it to uppercase or lowercase, and returns the result.

> In this example, routing is based on words in the text; no language model is called.

## Requirements

- Node.js 20 or later
- npm

## Setup

Install the dependencies and create a local environment file:

```powershell
npm install
Copy-Item .env.example .env
```

The `.env.example` file contains LangSmith configuration variables for tracing. They are optional for this project's text-conversion workflow. Keep real credentials in your local `.env` file and never publish them.

## Run the API

Start the server:

```powershell
npm run dev
```

The API will be available at `http://localhost:3000`.

Send a request using PowerShell:

```powershell
Invoke-RestMethod `
  -Uri http://localhost:3000/chat `
  -Method Post `
  -ContentType 'application/json' `
  -Body (@{ question = 'make this text uppercase' } | ConvertTo-Json)
```

The `POST /chat` endpoint expects a JSON object with a `question` field containing at least five characters:

```json
{
  "question": "convert this text to lowercase"
}
```

If the message contains `upper`, the text is converted to uppercase. If it contains `lower`, the text is converted to lowercase. For other requests, the API returns a message suggesting these commands.

## Graph workflow

1. `identifyIntent` checks whether the message contains `upper` or `lower`.
2. Conditional routing sends the message to the `uppercase`, `lowercase`, or `fallback` node.
3. `chatResponse` adds the result to the graph state's messages.
4. The server returns the output text.

## Tests

Run the automated tests:

```powershell
npm test
```

## LangGraph Studio

The project includes a graph configuration in `langgraph.json`. To start the LangGraph development server:

```powershell
npm run langgraph:serve
```

## Project structure

```text
src/
  graph/
    nodes/        # Intent detection, conversion, and fallback nodes
    factory.ts    # Exports the graph for the LangGraph configuration
    graph.ts      # Graph state, nodes, and conditional routing
  index.ts        # HTTP server entry point
  server.ts       # POST /chat endpoint
tests/            # API integration tests
```
