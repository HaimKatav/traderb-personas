# DevOps Reference — Deployment & Monitoring

## KVM 2 Deployment Checklist
- [ ] OS updated (Ubuntu 24.04 LTS)
- [ ] Docker Engine + Docker Compose v2 installed
- [ ] Firewall: deny all inbound, allow SSH (22), HTTPS (443), OpenClaw gateway (18789 localhost only)
- [ ] SSH: key-only auth, password disabled, root login disabled
- [ ] OpenClaw gateway running via Docker Compose
- [ ] TraderB bot running as systemd service with auto-restart
- [ ] SSL via Let's Encrypt for web access
- [ ] Monitoring: health check cron job every 5 minutes
- [ ] Backups: daily config + data snapshot to separate storage
- [ ] Log rotation configured for all services
- [ ] Secrets in `.env` files with 600 permissions

## TraderB Service Configuration
```ini
# /etc/systemd/system/traderb.service
[Unit]
Description=TraderB Trading Bot
After=network.target

[Service]
Type=simple
User=traderb
WorkingDirectory=/opt/traderb
EnvironmentFile=/opt/traderb/.env
ExecStart=/opt/traderb/.venv/bin/python run_server.py
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

## Monitoring Alerts

| Level | Event | Action |
|-------|-------|--------|
| CRITICAL | Bot process dead | Auto-restart + notify Haim |
| CRITICAL | API disconnect with open positions | Notify Haim immediately |
| CRITICAL | Daily loss limit hit | Log + notify Haim |
| HIGH | Bot restarted (crash recovery) | Log + notify Haim |
| HIGH | Disk space <10% | Notify Haim |
| MEDIUM | High latency (tick >5s) | Log |
| LOW | Log rotation event | Log only |
