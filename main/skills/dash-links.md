# Dash Links

When the user asks for "links", "dash links", "dashlinks", "menu", "urls", or "my links", execute the Telegram inline keyboard script to send an interactive menu with clickable buttons.

## Action

1. **Tell the user**: "Sending your dash links menu!"
2. **Execute**: Run the command:
   ```bash
   python3 /opt/traderb/send_dash_links.py
   ```
3. **On success**: The message will be sent to their Telegram automatically
4. **On failure**: Fall back to the text version below

## Fallback Text Response (if script fails)

If the script execution fails for any reason, use this formatted message:

```
🔗 **Dash Links**

🤖 **OpenClaw** — Agent Control
https://traderb.duckdns.org/openclaw/

📊 **TraderBot** — Trading Dashboard
https://traderb.duckdns.org/

🔭 **Observatory** — Agent Monitoring & Files
https://traderb.duckdns.org/observatory/?key=7G4XfrBzDxwJIzvr_JmR7-NukCfuTvWP444PlvFaTN4

📋 **Project Management** — Tasks & Kanban
https://traderb.duckdns.org/pm/
```

## Rules
- These links are permanent and always correct
- If Haim says "update dash links" or asks to add/change a link, update this skill file AND the `_shared/URLS.md` file
- Never use Cloudflare tunnel URLs or raw port URLs
- Always maintain the Observatory URL with the security key parameter
- The inline keyboard buttons are laid out 2 per row for mobile optimization
