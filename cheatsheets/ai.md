# AI

## GEMINI

```ps1

[Environment]::SetEnvironmentVariable("GOOGLE_GEMINI_BASE_URL", "https://gateway.ai.cloudflare.com/v1/fa0c3c1818cd69ddde353943aa6212f6/demo/google-ai-studio")
[Environment]::SetEnvironmentVariable("GEMINI_API_KEY", "")
[Environment]::SetEnvironmentVariable("GEMINI_MODEL", "gemini-2.5-flash-lite")
```

## CODEX

```toml
# windows_wsl_setup_acknowledged = true
# model = "gpt-5.1"

# [notice]
# hide_gpt5_1_migration_prompt = true


model_provider = "cf-gateway-gemini-compat"
model = "google-ai-studio/gemini-2.5-flash-lite" 
model_reasoning_effort = "high"
disable_response_storage = true
preferred_auth_method = "apikey"
windows_wsl_setup_acknowledged = true
[model_providers.cf-gateway-gemini-compat]
name = "cf-gateway-gemini-compat"
base_url = "https://gateway.ai.cloudflare.com/v1/fa0c3c1818cd69ddde353943aa6212f6/demo/compat"
wire_api = "chat"
env_key = "GEMINI_API_KEY"


```
