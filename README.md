# Genkit Vertex Proxy Plugin

A drop-in replacement for the Genkit Vertex AI plugin that keeps default behavior but adds:

- **CredentialsJSON / CredentialsFile**: feed service account JSON directly without using env vars.
- **ProxyURL**: per-plugin proxy; other network traffic in your process stays untouched.

## Install

```
go get github.com/nemo1105/genkit-vertex-proxy
```

## Usage

```go
import (
    "context"
    "os"

    "github.com/firebase/genkit/go/genkit"
    vertexproxy "github.com/nemo1105/genkit-vertex-proxy"
)

func main() {
    ctx := context.Background()
    saJSON, _ := os.ReadFile("/secure/path/sa.json")

    g := genkit.Init(ctx,
        genkit.WithPlugins(&vertexproxy.VertexAI{
            ProjectID:       "your-project",
            Location:        "us-central1",
            CredentialsJSON: saJSON,                     // or CredentialsFile: "/secure/sa.json"
            ProxyURL:        "http://proxy.example:8080", // optional
        }),
        genkit.WithDefaultModel("vertexai/imagen-4.0-fast-generate-001"),
    )

    _ = g
}
```

- If `CredentialsJSON`/`CredentialsFile` are empty, it falls back to ADC and env vars exactly like the stock plugin.
- If `ProxyURL` is empty, it uses the same transport as the stock plugin.

## Notes
- Models/embedders registered are identical to the upstream Genkit Vertex plugin.
- This plugin only scopes proxy settings to Vertex calls; no global env or http.DefaultTransport changes.
