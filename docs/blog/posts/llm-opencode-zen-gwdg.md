---
date: 2025-07-18
categories:
  - Tools
  - AI
---

# Using llm with OpenCode Zen and GWDG Models

[llm](https://llm.datasette.io/){:target="_blank"} is a CLI tool by Simon
Willison for interacting with large language models from the terminal. Since
both [OpenCode Zen](https://opencode.ai/docs/zen){:target="_blank"} and
[GWDG's SAIA](https://docs.hpc.gwdg.de/services/ai-services/saia/index.html){:target="_blank"}
expose OpenAI-compatible APIs, `llm` can talk to them with a small YAML
configuration.

<!-- more -->

## Installing llm

Pick one of the following methods:

```console
$ pip install llm
$ brew install llm
$ uv tool install llm
```

## Connecting to OpenCode Zen

[OpenCode Zen](https://opencode.ai/docs/zen){:target="_blank"} is an AI
gateway that provides access to a curated list of tested models through a
single API key. Get an API key at
[opencode.ai/zen/settings/api-keys](https://opencode.ai/zen/settings/api-keys){:target="_blank"}.

Store the key:

```console
$ llm keys set opencode-zen
```

Find the configuration directory and create or edit `extra-openai-models.yaml`:

```console
$ dirname "$(llm logs path)"
```

Add model entries to `extra-openai-models.yaml`:

```yaml
- model_id: opencode-zen-claude-sonnet-4
  model_name: claude-sonnet-4
  api_base: "https://opencode.ai/zen/v1"
  api_key_name: opencode-zen

- model_id: opencode-zen-gpt-5
  model_name: gpt-5
  api_base: "https://opencode.ai/zen/v1"
  api_key_name: opencode-zen

- model_id: opencode-zen-deepseek-v4-flash-free
  model_name: deepseek-v4-flash-free
  api_base: "https://opencode.ai/zen/v1"
  api_key_name: opencode-zen
```

Run a prompt:

```console
$ llm -m opencode-zen-claude-sonnet-4 'Explain SAIA in one paragraph'
```

## Connecting to GWDG Models

[GWDG SAIA](https://docs.hpc.gwdg.de/services/ai-services/saia/index.html){:target="_blank"}
hosts open-weight models on GWDG infrastructure with an OpenAI-compatible API.
An [Academic Cloud](https://academiccloud.de){:target="_blank"} account and a
SAIA API key are required.

Store the key:

```console
$ llm keys set gwdg
```

Add model entries to the same `extra-openai-models.yaml`:

```yaml
- model_id: gwdg-llama-3.1-8b
  model_name: meta-llama-3.1-8b-instruct
  api_base: "https://chat-ai.academiccloud.de/v1"
  api_key_name: gwdg

- model_id: gwdg-qwen3.6-27b
  model_name: qwen3.6-27b
  api_base: "https://chat-ai.academiccloud.de/v1"
  api_key_name: gwdg

- model_id: gwdg-deepseek-v4-flash
  model_name: deepseek-v4-flash
  api_base: "https://chat-ai.academiccloud.de/v1"
  api_key_name: gwdg
```

Run a prompt:

```console
$ llm -m gwdg-qwen3.6-27b 'What is the capital of Lower Saxony?'
```

The full list of available GWDG models can be retrieved with:

```console
$ curl -s -H "Authorization: Bearer $(llm keys get gwdg)" \
    https://chat-ai.academiccloud.de/v1/models | python -m json.tool
```

## Useful Commands

```console
$ llm models                          # list all configured models
$ llm models default gwdg-llama-3.1-8b  # set a default model
$ llm logs -n 5                       # show last 5 prompts/responses
$ llm logs -n 1 --json                # last response as JSON
```
