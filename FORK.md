# opencode-openai-prefix fork

This fork publishes CLI binaries from `zjm54321/opencode-openai-prefix` GitHub Releases. It does not publish npm, Homebrew, AUR, Docker, or desktop artifacts.

## Install

Install the latest GitHub Release binary:

```sh
curl -fsSL https://raw.githubusercontent.com/zjm54321/opencode-openai-prefix/dev/install | bash
```

Install a specific fork release:

```sh
curl -fsSL https://raw.githubusercontent.com/zjm54321/opencode-openai-prefix/dev/install | bash -s -- --version 1.17.9-fork.1
```

On Windows, an npm-global launcher can take precedence over the release binary. Check and replace deliberately:

```powershell
where opencode
opencode --version
```

If the first path is an npm shim, either remove the npm package/shim or put `%USERPROFILE%\.opencode\bin` before the npm global bin directory in `PATH`. On Windows the installer writes `%USERPROFILE%\.opencode\bin\opencode.exe`; on Unix-like systems it writes `$HOME/.opencode/bin/opencode`.

## OpenAI provider aliases

Provider IDs beginning with `openai-` are reserved for OpenAI-compatible aliases that inherit the built-in OpenAI model catalog and model loading behavior while using their own configured options.

Minimal config:

```json
{
  "provider": {
    "openai-any": {
      "name": "AnyRouter",
      "options": {
        "baseURL": "https://example.com/v1",
        "apiKey": "sk-example"
      }
    }
  },
  "model": "openai-any/gpt-5.5"
}
```

Alias behavior:

- Inherited models are retagged to the alias provider ID, so `baseURL`, `apiKey`, and other options come from the alias.
- Aliases do not inherit `OPENAI_API_KEY` or stored OpenAI auth/API keys.
- Built-in OpenAI defaults such as `headerTimeout` and `promptCacheKey` still apply.
- OpenAI OAuth-only behavior applies to `openai-*` only when that alias is actually authenticated with OAuth; API-key aliases stay API-key based.

## Provider retry options

Retry timing can be configured per provider under `options.retry`:

```json
{
  "provider": {
    "openai-any": {
      "options": {
        "apiKey": "sk-example",
        "baseURL": "https://example.com/v1",
        "retry": {
          "initialDelay": 1000,
          "backoffFactor": 2,
          "maxDelayNoHeaders": 30000,
          "maxDelay": 60000
        }
      }
    }
  }
}
```

Values are positive finite millisecond numbers; numeric strings are accepted. Invalid or missing values fall back to the built-in defaults. Provider `Retry-After` and `retry-after-ms` headers are still honored and capped by `maxDelay`.
