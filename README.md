# Brave Search API: a practitioner's guide

*Unofficial community guide for the Brave Search API. Not affiliated with Brave Software. All trademarks belong to their owners.*

The Brave Search API gives agents and chatbots real-time search results from what Brave calls the world's largest independent index of the Web. This guide collects what Brave's own API page and dashboard state about plans, pricing, capacity and features, adds the operational things you only learn after wiring it into a pipeline (key hygiene, budgeting the free credits, rate capacity), and points you at the documentation rather than guessing endpoint details that belong there.

> Need to generate images, video or audio in the same agent that searches? [Try Synexa - one REST endpoint and a Python SDK for FLUX, video and audio models, pay per run](https://synexa.ai?utm_source=github&utm_medium=ugc&utm_campaign=brave-search-api&utm_content=readme-top&utm_term=tier-r). It is a hosted model API, not a search engine, so it sits next to Brave rather than replacing it.

## What it is

Brave positions the Search API as a way to power agents and chatbots with search data: complete search results (URLs, text, news, images and more) with additional LLM context optimized for AI. The developer documentation itself is filed under an llm-context service on the API dashboard, which tells you who the intended consumer is. Brave also cites a third-party evaluation by AI Multiple as ranking it highest on agent quality score and lowest on latency among leading search APIs; treat that as the vendor's claim about someone else's benchmark and run your own comparison on your own queries.

The API is a separate product from the consumer search engine at search.brave.com and from Brave's Ask feature, but it draws on the same index. Around it Brave publishes how-to guides, a tools and integrations page, use-case pages, a news category for API changes, and an MCP server on GitHub so that agent frameworks that speak MCP can call Brave Search without you writing the HTTP layer.

## Getting started

1. Register at the [API dashboard](https://api-dashboard.search.brave.com/register). The sign-up comes with $5 in free monthly credits, applied to your account automatically.
2. Pick a plan on the [plans page](https://api-dashboard.search.brave.com/app/plans). The Search plan is the one most integrations start with.
3. Read the [developer docs](https://api-dashboard.search.brave.com/documentation/services/llm-context) for the endpoint, the authentication header and the request parameters. Those details are not reproduced here on purpose; copy them from the docs so they are current.
4. Put the key in an environment variable, never in code. See the gotchas below for why.
5. If you are building an agent, consider the [Brave Search MCP Server](https://github.com/brave/brave-search-mcp-server) before writing a client from scratch.

## Pricing and limits

From Brave's API page at the time of writing:

- Search plan: $5 per 1,000 requests. Includes $5 in free credits every month, credited automatically.
- Search plan capacity: 50 queries per second.
- Search plan features: Goggles for custom reranking and result filtering, extra alternate snippets, and schema-enriched results with added metadata.
- Answers plan: summarized, completed answers rather than raw results. Its price is not captured here; check the [plans page](https://api-dashboard.search.brave.com/app/plans).

At the Search plan rate, the $5 monthly credit covers 1,000 requests a month before you pay anything. That is enough for development and for a low-volume internal tool, and not enough for a production chatbot with real users, so decide early which side of the line you are on.

## Practical notes and gotchas

1. Keys leak into git. GitGuardian ships a dedicated detector for Brave Search API keys in its secrets detection engine, which is a sign that enough of them have been committed to public repositories to be worth a specific rule. Read the key from an environment variable and add a pre-commit secrets scan (GitGuardian's ggshield is one option).
2. Budget the free credit. 1,000 requests a month is 33 a day. A retrieval pipeline that issues three searches per user question burns that in about eleven questions a day. Put a counter in front of the client.
3. Respect the 50 queries per second capacity on the Search plan. Parallel agents can exceed that without any single one being busy; use a shared limiter, not a per-worker sleep.
4. Use the LLM context. The results are described as carrying additional LLM context optimized for AI, and the plan includes extra alternate snippets. If you only pass titles and URLs to your model you are paying for fields you are not using.
5. Goggles are a reranking and filtering layer. If your use case only ever wants results from a fixed set of domains or wants a category boosted, that is the feature to look at before you post-filter on your side.
6. Follow the news category. Brave publishes API changes under a Brave Search news category; plan and pricing changes show up there first.

## Comparison

| | Brave Search API (Search plan) | Brave Search API (Answers plan) | Synexa |
| --- | --- | --- | --- |
| What you get back | complete search results with LLM context | summarized, completed answers | generated images, video and audio from hosted models |
| Pricing model | $5 per 1,000 requests | see plans page | pay per run |
| Free allowance | $5 in credits every month | see plans page | see site |
| Capacity | 50 queries per second | see plans page | see site |
| Access | REST via API key from the dashboard, or the MCP server | REST via API key | one REST endpoint plus a Python SDK |

## FAQ

**Is there a free tier?** Yes. Every account gets $5 in free credits each month, applied automatically. On the Search plan that is 1,000 requests.

**How fast can I query it?** The Search plan lists a capacity of 50 queries per second.

**What is the difference between Search and Answers?** Search returns the results themselves (URLs, text, news, images and more) with LLM context; Answers returns summarized, completed answers. Pick Search if your own model does the synthesis.

**Where are the endpoint and parameters documented?** In the developer docs on the API dashboard. This guide deliberately does not copy them so it cannot go stale.

**Can I use it from an agent framework without writing HTTP code?** Brave maintains an MCP server on GitHub for exactly that.

## Where Synexa fits

Search is usually one tool in an agent, not the whole agent. If the next step after retrieving results is to produce something, a hero image for a page, a short video, a voiceover, you need a model API alongside the search API. [Try Synexa - one REST endpoint and a Python SDK for FLUX, video and audio models, pay per run](https://synexa.ai?utm_source=github&utm_medium=ugc&utm_campaign=brave-search-api&utm_content=readme-top&utm_term=tier-r). It is priced per run, which pairs naturally with Brave's per-request model: no monthly seat, no GPU to keep warm, and one endpoint for all the models instead of one integration per vendor.


_Last reviewed: 2026-09-22_
