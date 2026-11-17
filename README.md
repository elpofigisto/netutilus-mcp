# Netutilus MCP server

Network diagnostics on your Mac for AI assistants. With the
[Netutilus](https://netutilus.com/) app, Claude Desktop, Claude Code, Codex,
Cursor and other MCP clients can ping, trace a route, test your speed, scan
your Wi-Fi and LAN or check a domain's mail records, then explain the result in
plain words.

Ask "why is my Wi-Fi slow?" and the assistant can scan the networks around you,
run a speed test and trace the route before it answers. Every tool measures
from your own Mac, with the same engine as the app's windows. The tools only
measure and read: none of them changes a setting.

- **Website and guide:** https://netutilus.com/support/mcp-server/
- **Get Netutilus:** [Mac App Store](https://apps.apple.com/app/id6758589382?mt=12&pt=119462635&ct=github-mcp)

This repository holds the documentation and client configuration. The server
itself ships inside the Netutilus app (`Netutilus.app/Contents/Helpers/netutilus-mcp`),
so there is nothing to build or install from here.

## Requirements

- macOS 14.6 or later
- Netutilus from the Mac App Store, with **Netutilus Pro** (the MCP server is a Pro feature)
- An MCP client that runs local (stdio) servers. A chat in a web browser can't reach an app on your Mac.

## Set it up

1. Open Netutilus, then **Settings › MCP**, and turn on **Allow AI assistants**.
2. Under **Connect an Assistant**, pick yours. Netutilus writes the configuration
   for Claude Desktop and Cursor, and gives you the command for Claude Code and Codex.

Settings › MCP shows the exact path of your copy. The commands below assume the
app is in `/Applications`.

**Claude Code**

```sh
claude mcp add --scope user netutilus -- /Applications/Netutilus.app/Contents/Helpers/netutilus-mcp
```

**Codex**

```sh
codex mcp add netutilus -- /Applications/Netutilus.app/Contents/Helpers/netutilus-mcp
```

**Claude Desktop, Cursor and other clients**

```json
{
  "mcpServers": {
    "netutilus": {
      "type": "stdio",
      "command": "/Applications/Netutilus.app/Contents/Helpers/netutilus-mcp",
      "args": []
    }
  }
}
```

Restart the client after you add it. When an assistant needs Netutilus and the
app isn't running, it opens by itself, without a window.

Check the connection: `claude mcp list` (Claude Code), `codex mcp list` (Codex),
Settings › Developer (Claude Desktop) or the MCP settings (Cursor). While
Netutilus is open, Settings › MCP lists each connected assistant and every
request since launch.

## Tools

Each tool with a link to its guide: https://netutilus.com/support/mcp-tools/

| Tool | What it does | Try asking |
|---|---|---|
| `internet_check` | Tells this Mac, the local network, the provider and a single site apart | "Is the problem my Wi-Fi or my provider?" |
| `ping` | Latency, jitter and packet loss to a host | "Ping 1.1.1.1 twenty times and tell me the loss." |
| `traceroute` | The route to a host, hop by hop | "Trace the route to github.com." |
| `mtr` | Ping and traceroute together: where along the route loss starts | "Run MTR to my VPN server and find where the loss starts." |
| `visual_traceroute` | The route with each hop's location and network | "Which countries does my traffic to example.com cross?" |
| `speed_test` | Download, upload and bufferbloat | "Test my speed and bufferbloat." |
| `speed_history` | Your saved speed tests | "At what time of day is my connection slowest?" |
| `dns_lookup` | DNS records of a domain | "Show the MX records of example.com." |
| `email_auth` | SPF, DKIM, DMARC and MX of a domain | "Why does mail from example.com go to spam?" |
| `dns_propagation` | One DNS record as six public resolvers answer it | "Has my new A record reached the big resolvers?" |
| `whois` | Registration of a domain | "When does example.com expire?" |
| `geoip` | Owner and approximate location of an IP address | "Who owns 8.8.8.8, and where is it?" |
| `ssl_certificate` | A site's TLS certificate | "Is the certificate on my site valid?" |
| `port_scan` | Open ports on a host | "Which ports are open on my router?" |
| `path_check` | Whether specific ports on a server are reachable | "Can I reach my server on ports 22 and 443?" |
| `subnet_calculator` | Ranges and host counts of a subnet | "How many hosts fit in 10.0.0.0/22?" |
| `iperf3` | Throughput to an iperf3 server (client only) | "Run iperf3 against 192.168.1.20." |
| `network_monitor` | Interfaces, addresses, traffic and, on request, routes | "Which interface am I on? Show my routes." |
| `uptime_monitor` | Your uptime checks and their history (read only) | "Was any of my sites down today?" |
| `lan_scanner` | Devices on your network | "What devices are on my network?" |
| `network_guard` | Untrusted devices Network Guard has seen join your network (read only) | "Has a new device joined my network?" |
| `wifi_analyzer` | Your Wi-Fi signal | "How good is my Wi-Fi signal?" |
| `wifi_networks` | Networks and channels around you | "Which channel is least crowded here?" |
| `connection_status` | Your connection as the menu bar knows it: public IP, DNS, VPN and recent changes | "Am I on a VPN, and does it leak?" |

Tools that keep running in the app's windows, such as MTR, run a set number of
rounds and then report. One speed test or iperf3 run at a time. Every tool
answers within about a minute; a big job returns what it finished and says what
is left. Nothing an assistant runs is added to your histories.

## Privacy

The assistant receives only the results of the tools it runs: the figures the
window shows. Depending on the tool, they can include your public IP address,
its approximate location and your provider, Wi-Fi network names, and the
devices on your network with their names and addresses. An access point is
named by an alias such as `ap-3f9a1c`, never by its hardware address (BSSID).

Netutilus itself sends nothing new anywhere. An assistant that runs in the
cloud passes the results to its provider, as it does with anything else in the
chat.

Turn **Allow AI assistants** off to refuse every request until you turn it back
on. To remove the server: `claude mcp remove netutilus` or
`codex mcp remove netutilus`; for Claude Desktop and Cursor, delete the
`netutilus` entry from their configuration and restart them.

## Support

Questions and bug reports: [open an issue](../../issues) or write to
support@netutilus.com.

Netutilus is made by [Poteam](https://poteam.pro/), an independent software
studio in London.
