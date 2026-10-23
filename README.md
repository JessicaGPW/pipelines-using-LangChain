# Pipelines usando LangChain

[Read this README in English](README.en.md)

Projeto introdutório que demonstra um fluxo de processamento de mensagens com **LangGraph**, usando nós e roteamento condicional. A aplicação recebe um texto pela API, identifica se o pedido é para converter o texto para maiúsculas ou minúsculas e retorna o resultado.

> Neste exemplo, o roteamento é baseado em palavras no texto; não há chamada a um modelo de linguagem.

## Requisitos

- Node.js 20 ou superior
- npm

## Configuração

Instale as dependências e crie o arquivo de ambiente local:

```powershell
npm install
Copy-Item .env.example .env
```

O `.env.example` contém variáveis de configuração do LangSmith para rastreamento. Elas são opcionais para o fluxo de conversão deste projeto. Mantenha credenciais reais somente no `.env` local e nunca as publique.

## Executar a API

Inicie o servidor:

```powershell
npm run dev
```

A API ficará disponível em `http://localhost:3000`.

Envie uma solicitação pelo PowerShell:

```powershell
Invoke-RestMethod `
  -Uri http://localhost:3000/chat `
  -Method Post `
  -ContentType 'application/json' `
  -Body (@{ question = 'make this text uppercase' } | ConvertTo-Json)
```

O endpoint `POST /chat` espera um JSON com o campo `question`, que deve conter pelo menos cinco caracteres:

```json
{
  "question": "convert this text to lowercase"
}
```

Se a mensagem contiver `upper`, o texto será convertido para maiúsculas. Se contiver `lower`, será convertido para minúsculas. Para outros pedidos, a API retorna uma mensagem sugerindo esses comandos.

## Fluxo do grafo

1. `identifyIntent` verifica se a mensagem contém `upper` ou `lower`.
2. O roteamento condicional encaminha a mensagem ao nó `uppercase`, `lowercase` ou `fallback`.
3. `chatResponse` adiciona o resultado às mensagens do estado do grafo.
4. O servidor retorna o texto de saída.

## Testes

Execute os testes automatizados:

```powershell
npm test
```

## LangGraph Studio

O projeto inclui a configuração do grafo em `langgraph.json`. Para iniciar o servidor de desenvolvimento do LangGraph:

```powershell
npm run langgraph:serve
```

## Estrutura do projeto

```text
src/
  graph/
    nodes/        # Nós de identificação, conversão e resposta alternativa
    factory.ts    # Exporta o grafo para a configuração do LangGraph
    graph.ts      # Estado, nós e roteamento condicional
  index.ts        # Inicialização do servidor HTTP
  server.ts       # Endpoint POST /chat
tests/            # Testes de integração da API
```
