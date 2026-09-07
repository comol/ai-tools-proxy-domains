# Proxifier rules (top to bottom)

Proxifier applies the first matching rule. Keep Direct exceptions above the AI catch-all.

## Direct

| Rule | Applications | Hosts | Why |
| --- | --- | --- | --- |
| Localhost | Any | `localhost; 127.0.0.1; %ComputerName%; ::1; 10.100.0.*` | Local proxy and LAN must not loop |
| Docker | `C:\Program Files\Docker\**\*.exe` | Any | Containers have their own routing |
| ADB | `adb.exe` | Any | Device bridge |
| AdGuard | `adguard*.exe` | Any | Filter/DNS stays local |
| PowerShell | `pwsh.exe; windowsterminal.exe; openconsole.exe` | Any | Optional; tighten if the shell talks to AI APIs |
| Node.js (global) | `C:\Program Files\nodejs\node.exe` | Any | System Node; local `node.exe` still proxied below |
| Default | Any | Any | Everything else goes direct |

## Proxy chain (example: `127.0.0.1:8090`)

| Rule | Applications | Hosts |
| --- | --- | --- |
| AI Services | Any | contents of `ai-services-masks.txt` |
| Node.js (local) | `node.exe` | Any |
| Cursor | `cursor.exe; cursor*.exe; code-tunnel.exe; …` | Any |
| ChatGPT / Codex | `codex.exe; codex*.exe; chatgpt.exe; chatgpt*.exe; WindowsApps\OpenAI\**\*.exe` | Any |
| Google / Antigravity | `chrome.exe; antigravity.exe; …` | Any |
| VSC | `code.exe` | Any |
| Ollama | `ollama*.exe` | Any |

`chrome.exe` in the Google rule sends **the whole browser** through the proxy, not just Gemini.

## Firewall lock

A Proxifier miss is a leak if Default is Direct. The working setup adds outbound **block** rules for Cursor, Codex, VS Code, Antigravity and their helper `node.exe` copies, so those binaries cannot go to the public internet except via the local proxy.

Also block public DNS over IPv6 if the proxy stack is IPv4-only.

## Known holes

- Proxifier DNS = System DNS: hostnames can leak before CONNECT.
- Unknown vendor host + Default Direct + no firewall rule = leak.
- `*windowsupdate*` / `*microsoft.com*` / `*github*` / `*cloudflare*` are huge; expect broken or slowed Windows Update, npm, and random HTTPS.
- Do not paste other people's absolute `C:\Users\...` paths. Use your own install locations.
