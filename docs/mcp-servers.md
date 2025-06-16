Below are the places the MCP community treats as the “yellow pages” for Model Context Protocol servers.  None of them is *officially canonical*, but together they cover almost everything that’s public today, from reference builds to vendor-maintained production endpoints.

| Registry / Inventory                                | Why it’s useful                                                                                                                                                           | Quick notes                                                                                         |
| --------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| **modelcontextprotocol/servers (GitHub)**           | The original repo created by Anthropic. Lists every reference server plus hundreds of third-party integrations. Easy to PR your own server or track stars for popularity. | Raw `README.md` is machine-readable, so you can `curl`/`jq` it for automation. ([github.com][1])    |
| **Example Servers page on ModelContextProtocol.io** | Human-friendly catalogue with categories (Data, DevTools, Web, AI, etc.) and links straight to npm / PyPI packages.                                                       | Good starting point if you just want a few proven reference servers. ([modelcontextprotocol.io][2]) |
| **Awesome MCP Servers (mcpservers.org)**            | Community-curated site with faceted filters (Official, Search, Cloud Service, etc.). Fast to browse, screenshots for each entry.                                          | Great when you’re exploring and don’t know the exact tool you need. ([mcpservers.org][3])           |
| **OneMCP.io**                                       | Lightweight “app store” style registry that lets you one-click run a server locally or copy the Docker/NPM command.                                                       | Focuses on *featured* and trending servers rather than exhaustive coverage. ([onemcp.io][4])        |
| **MCP-Server-List.com**                             | The biggest scraper/aggregator (\~650 servers, plus clients & frameworks). Category counts and search-as-you-type.                                                        | Provides a JSON feed if you need to keep an internal mirror. ([mcp-server-list.com][5])             |
| **MCP Index / MCP Directory / MCP Archive**         | Smaller mirrors; useful when a main site is down or you want historical snapshots.                                                                                        |                                                                                                     |
| **Windows MCP Registry (preview)**                  | Not public web, but worth knowing: Windows 11 “AI Foundry” will ship an OS-level MCP registry so agents can discover locally-installed servers securely.                  | Still in dev-preview; aims to be the “USB-C of AI apps.” ([theverge.com][6])                        |

### How people usually pick one

1. **Start with GitHub repo or Example Servers** if you just need a stable, well-documented server (e.g., `filesystem`, `fetch`, `memory`, `git`).
2. **Jump to Awesome MCP Servers or MCP-Server-List** when you want niche tooling (Stripe billing, Kubernetes, Redis, etc.) or to compare multiple vendor builds.
3. **Use OneMCP** for quick local experiments—its “Run” button spits out the right `npx`/`pip` line.
4. **Automation**: git repo’s `README.md` and MCP-Server-List JSON feed are easiest to ingest into CI / agent configs.
5. **Security & updates**: check commit-dates or site “last updated” badges; some servers stagnate fast. Production agents usually pin to a commit hash or tag.

### Programmatic discovery tips

```bash
# Fetch the raw server list from GitHub and pull only names + repo URLs
curl -sL https://raw.githubusercontent.com/modelcontextprotocol/servers/main/README.md \
 | grep -Eo '\* \[.*\]\(https://github.com/.*/mcp-.*\)' \
 | sed -E 's/\* \[([^]]+)\]\(([^)]+)\)/\1 \2/'
```

```python
# Python snippet to load the MCP-Server-List JSON feed
import requests, pandas as pd, json
data = requests.get("https://mcp-server-list.com/api/servers.json").json()
df = pd.DataFrame(data)
print(df[['name','category','tags','repo']].head())
```

> **Heads-up:** if you’re on Windows Insider builds, the forthcoming local registry will surface installed servers via `winget ai mcp list` once the preview lands, so external registries won’t be needed for on-device discovery.

That should give you a solid map of the MCP landscape—happy server hunting!

[1]: https://github.com/modelcontextprotocol/servers "GitHub - modelcontextprotocol/servers: Model Context Protocol Servers"
[2]: https://modelcontextprotocol.io/examples "Example Servers - Model Context Protocol"
[3]: https://mcpservers.org/ "Awesome MCP Servers"
[4]: https://onemcp.io/mcp-servers "OneMCP | Model Context Protocol Registry"
[5]: https://mcp-server-list.com/ "Best MCP Servers and Clients List"
[6]: https://www.theverge.com/news/669298/microsoft-windows-ai-foundry-mcp-support "Windows is getting support for the ‘USB-C of AI apps’ | The Verge"
