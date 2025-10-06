# Search Agent

Original code: https://github.com/Infotrend-Inc/CoreAI-DemoProjects/tree/main/Agent

## About

The Agent demo shows a tool-using LLM agent that can call a web search tool and route results through an LLM to answer queries.

This version was modified to use Ollama and Perplexity's OpenAI API.
You will need to bring your own keys for [Perplexity](https://docs.perplexity.ai/guides/api-key-management) and [Ollama](https://docs.ollama.com/web-search).

High-level steps:
1.	Set up the LLM client and load API keys/config.
2.	Define a web search tool and a simple text fetch/summarize helper.
3.	Agent loop: the LLM plans actions, chooses a tool, executes it, and observes the result (tool → observation).
4.	Retrieve and summarize: aggregate top search hits, extract snippets, and compress into a focused context.
5.	Synthesize the final answer: the LLM uses the gathered context to produce a grounded response (often with links/citations).
