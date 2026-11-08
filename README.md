# PendingDelete.net MCP server

Search the domain names about to drop, and the ones dropped recently, from Claude, ChatGPT, Cursor, VS Code or any app
that speaks the [Model Context Protocol](https://modelcontextprotocol.io).

```
https://mcp.pendingdelete.net/mcp
```

Remote MCP over Streamable HTTP · read-only · no account, no key ·
[Docs](https://pendingdelete.net/mcp-server) · [PendingDelete.net](https://pendingdelete.net)

## What it does

[PendingDelete.net](https://pendingdelete.net) lists the domain names in pending delete (the last five days before a
registry deletes a name and anyone can register it) across every extension it tracks, with each name's drop time,
registrar, age and link figures. The MCP server puts those lists inside your AI assistant, with two tools.

| Tool | What it does |
|---|---|
| `search_domains` | Searches the lists with every filter of the site's [advanced search](https://pendingdelete.net/tools/advanced-search), run by the same code. One status at a time: `pending_delete` (the default), `available` (released and not registered again), `caught` (released and registered again) or `released` (still being checked). Released names reach back 90 days. |
| `get_domain` | One name's facts when it is on the lists: status, drop or release time and whether the drop time is confirmed, length, registrar, first registered, Linking Domains, Rank, which of its .com, .net and .org are registered, and its page. |

Both tools only read: nothing is registered, bought or changed.

An answer gives how many names match, a table of the names on that page, each linked to its page on PendingDelete.net,
a link to the same search on the site, and a CSV of the whole list. Apps that read structured content get the same
answer as JSON under an output schema.

### The filters of `search_domains`

| Argument | Filter |
|---|---|
| `status` | `pending_delete`, `available`, `caught` or `released` |
| `keyword`, `match` | Text in the name before the dot: `contains`, `starts_with`, `ends_with`, `exact` or `not_contains` |
| `regex` | A regular expression over the name before the dot, such as `^[a-z]{4}$` |
| `exclude` | Up to five words to leave out |
| `letter_pattern` | C a consonant, V a vowel, L any letter, N a digit: `CVCV`, `LLLN`, `go-L` |
| `min_length`, `max_length` | Characters before the dot |
| `hyphens`, `numbers`, `idn` | `false` leaves out names with hyphens, digits, or other alphabets (xn--) |
| `extensions` | Such as `["com"]` or `["com", "net", "org"]`; every tracked extension when left out |
| `adjoining_registered` | Keep names whose same name is registered now in .com, .net or .org |
| `drop_date_from`, `drop_date_to` | The drop (or release) day: `YYYY-MM-DD`, `yesterday`, `today` or `tomorrow`, US Eastern |
| `confirmed_drop_times_only` | Only drop times worked out from the registry's own record |
| `registrars` | Up to ten, as the registry writes them (`GoDaddy.com, LLC`) |
| `registered_from_year`, `registered_to_year` | The year the name was first registered |
| `min_linking_domains` | At least 1, 10, 100 or 1,000 |
| `min_rank` | At least 1 to 5 |
| `real_words`, `syllables` | Everyday English words; 1, 2 or 3 syllables |
| `mens_names`, `womens_names`, `surnames` | A first name at the start, or a last name |
| `sort`, `order` | `drop_date`, `name`, `length`, `linking_domains`, `rank`, `registered` or `adjoining`; `asc` or `desc` |
| `limit`, `page` | Names an answer (100 unless asked otherwise) and the page, through the first 10,000 |

## Connect

### Claude

On the web or in the desktop app: **Customize → Connectors → Add custom connector**. Name it PendingDelete.net, paste
`https://mcp.pendingdelete.net/mcp`, and choose no sign-in. Turn it on for a chat from the **+** menu under
**Connectors**. On Team and Enterprise plans an Owner adds it for the organization first.

### ChatGPT

In developer mode, on the plans that have it: **Settings → Apps → Advanced settings → Developer mode**, then
**Create app** with the server URL and no authentication. In a chat, pick it from the **+** menu.

### Claude Code

```sh
claude mcp add --transport http pendingdelete https://mcp.pendingdelete.net/mcp
```

### Cursor

`~/.cursor/mcp.json` (every project) or `.cursor/mcp.json` (one project):

```json
{
  "mcpServers": {
    "pendingdelete": { "url": "https://mcp.pendingdelete.net/mcp" }
  }
}
```

### VS Code

`.vscode/mcp.json`:

```json
{
  "servers": {
    "pendingdelete": { "type": "http", "url": "https://mcp.pendingdelete.net/mcp" }
  }
}
```

### Apps that only run local servers

Bridge it with [mcp-remote](https://www.npmjs.com/package/mcp-remote):

```json
{
  "mcpServers": {
    "pendingdelete": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://mcp.pendingdelete.net/mcp"]
    }
  }
}
```

## Ask it

- Which .com names drop tomorrow that are real words of eight letters or fewer?
- Find available .com names released this week with at least 10 linking domains.
- Find pending delete .com names first registered before 2005 with at least 10 linking domains, best Rank first.
- Show the .com names dropping today whose .net and .org are both registered.
- Two-syllable .com names ending in "host", no hyphens or numbers.
- When does example.com drop, and is that time confirmed?

## What the figures mean

- **Drop time**: when the registry is due to delete the name, in US Eastern time and UTC. It is confirmed when worked
  out from the registry's own record of when the name entered pending delete, and estimated otherwise. .com and .net
  drop in Verisign's daily batch; most other registries release each name at its own time
  ([drop times](https://pendingdelete.net/drop-times)).
- **Linking Domains**: how many other domains link to the name in [Common Crawl](https://commoncrawl.org)'s web graph.
- **Rank**: the name's place among every domain in that graph, by Common Crawl's harmonic-centrality ranking, as a
  score from 0 to 10 on a log scale: 10 × (1 − ln(place) ÷ ln(domains in the graph)). The best-connected domain is 10;
  place 1,000 of about 130 million is 6.3, place 1,000,000 is 2.6.
- Linking Domains and Rank are not Moz DA, Ahrefs DR or Open PageRank. A name nobody links to has neither figure.

## Where the names come from

Names that left their zone file and that each registry's own answer (RDAP) puts in redemption or pending delete. A name
that is simply registered is never listed, and nameservers are never given.

## Limits

Per-minute limits keep the server quick for everyone; the [docs page](https://pendingdelete.net/mcp-server#limits)
shows the current numbers. Over a limit, the assistant is told to wait a minute and try again.

## Privacy and support

Calls are logged to keep the server running well; the [privacy policy](https://pendingdelete.net/privacy) says what
is kept and for how long. Questions or a problem: the [contact form](https://pendingdelete.net/contact).

## This repository

The server runs at the address above; this repository holds its documentation and its
[`server.json`](server.json) for the [MCP Registry](https://registry.modelcontextprotocol.io). The text and
configuration here are under the MIT license; the server and its data are PendingDelete.net's.
