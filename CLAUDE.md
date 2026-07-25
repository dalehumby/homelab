# CLAUDE.md

This is a homelab infrastructure repo managing Docker Swarm stacks and Docker Compose services on a 3-node cluster (2x Raspberry Pi 4B + 1x Dell x86 server). All persistent data lives under `/media/cluster/<service>/` — a shared network mount available across nodes.

## Deployment

**Docker Swarm stacks** (multi-node, managed services):
```bash
docker stack deploy -c home-stack.yaml home
docker stack deploy -c proxy-stack.yaml proxy
docker stack deploy -c cron-stack.yaml cron
docker service ls
```

**Docker Compose** (single-host only; used when device passthrough is needed, e.g. USB Zigbee stick):
```bash
docker compose up -d        # uses compose.yaml
docker compose -f crowdsec-compose.yaml up -d
```

## Architecture

| File | Purpose |
|------|---------|
| `home-stack.yaml` | IoT/home automation: Mosquitto MQTT, Node-RED, Home Assistant, ESPHome, Homepage, syslog-ng, ConvertX, BentoPDF |
| `proxy-stack.yaml` | Traefik v3 reverse proxy (TLS via Let's Encrypt HTTP challenge) + Fail2ban |
| `cron-stack.yaml` | Scheduled jobs via `swarm-cronjob`: dynamic DNS update every 5 min, weekly `docker system prune` |
| `compose.yaml` | Device-dependent services on the Dell host: Zigbee2MQTT, govee2mqtt, iSponsorBlockTV |
| `crowdsec-compose.yaml` | CrowdSec intrusion detection (reads Traefik + syslog + HA logs) |

**Secrets**: Swarm stacks use Docker secrets (`external: true`) — never committed. Compose uses `.env` (gitignored). Don't add secrets to YAML files.

**Networking**: Traefik is the ingress for `*.humby.co.za`. Services that need external access join the `proxy_default` network and set `traefik.enable=true` deploy labels. Services that don't need external access set `traefik.enable=false`.

**Traefik TLS**: Uses `mytlschallenge` cert resolver (HTTP-01). HTTP (`web`, port 80) → HTTPS (`websecure`, port 443) redirection is handled by a `catchall-redirect` router in `/media/cluster/traefik/dynamic_conf.yml` (not Traefik's native entrypoint-level redirection — that runs at near-max priority and would override the intentionally plain-HTTP `nodered-insecure` route used by TLS-incapable ESP/e-paper devices). Dashboard runs insecurely on port 8080 (internal only).

**Placement**: The Traefik service and `swarm-cronjob` manager are pinned to `node.role==manager`. The prune cron job runs `mode: global` (all nodes).

## Homepage

When adding or removing a service, also update `/media/cluster/homepage/services.yaml` to keep the dashboard in sync. Add entries under the appropriate section with `icon`, `href`, and `description`.

## Linting

```bash
yamllint .
```

Config is in `.yamllint`: 2-space indentation, max line length 200, document-start disabled, truthy allows `true/false/on/off`.
