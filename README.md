# 🧩 Home Media Stack

A self-hosted, privacy-focused media stack running on Docker Compose with **Traefik**, **Gluetun VPN**, and **Plex**.

> All sensitive data (usernames, passwords, API keys, volume paths) have been replaced with placeholders. Copy `.env.example` to `.env` and fill in your values before deployment.

---

## 🚀 Overview

This stack automates the full lifecycle of media acquisition, organization, and playback:

- **Gluetun** – Routes all download traffic through a Surfshark VPN tunnel
- **Traefik** – Reverse proxy with internal `.lab` DNS routing (external network)
- **Sonarr / Radarr / Lidarr** – Automated TV, movie, and music management
- **SABnzbd** – Usenet download client
- **qBittorrent** – BitTorrent download client
- **Prowlarr / Jackett** – Indexer management
- **FlareSolverr** – Cloudflare bypass for indexers
- **Bazarr** – Subtitle automation
- **Seerr** – Media request system integrated with Plex
- **Subsonic (Airsonic Advanced)** – Music streaming server

Plex is intentionally **not containerized** due to hardware transcoding limitations on macOS with virtualized networking. Run it directly on the host.

---

## 💻 Hardware & Environment

- **Mac Mini (Apple Silicon, ARM64)**
- **OS:** macOS
- **Docker Engine:** OrbStack or Docker Desktop

---

## ⚙️ Architecture

Most download services run **inside Gluetun's network namespace** — their traffic exits through the VPN. Traefik accesses them via lightweight `socat` proxy containers that forward ports from `traefik-net` into `gluetun-net`.

```
Local Network (LAN)
        │
        ▼
  [ Traefik Proxy ]  ←── traefik-net (external)
        │
   socat proxies  ──────────────────────────────┐
        │                                       │
        ▼                                       ▼
  [ Gluetun VPN ]  ←── gluetun-net (bridge)
        │
  ┌─────┴──────────────────────────────────────┐
  │  Sonarr · Radarr · Lidarr · Prowlarr       │
  │  Jackett · FlareSolverr · SABnzbd          │
  │  qBittorrent · Bazarr                      │
  └────────────────────────────────────────────┘

  Seerr · Subsonic  ←── traefik-net (direct, no VPN)
```

---

## 🧱 Networks

| Network | Type | Purpose |
|---------|------|---------|
| `traefik-net` | external | Traefik routing and host access |
| `gluetun-net` | bridge | Isolated VPN traffic |

---

## 🧩 Services & Ports

| Service | Internal Port | Traefik URL | Description |
|---------|:---:|-------------|-------------|
| **Gluetun** | — | — | VPN gateway |
| **SABnzbd** | 8089 | `http://sabnzbd.lab` | Usenet download client |
| **qBittorrent** | 8081 | `http://qbittorrent.lab` | BitTorrent client |
| **Sonarr** | 8989 | `http://sonarr.lab` | TV show automation |
| **Radarr** | 7878 | `http://radarr.lab` | Movie automation |
| **Lidarr** | 8686 | `http://lidarr.lab` | Music automation |
| **Prowlarr** | 9696 | `http://prowlarr.lab` | Indexer manager |
| **Jackett** | 9117 | `http://jackett.lab` | Indexer manager (legacy) |
| **FlareSolverr** | 8191 | `http://flaresolverr.lab` | Cloudflare bypass |
| **Bazarr** | 6767 | `http://bazarr.lab` | Subtitle automation |
| **Seerr** | 5055 | `http://seerr.lab` | Media request system |
| **Subsonic** | 4040 | `http://subsonic.lab` | Music streaming |

---

## 🔐 VPN & Privacy

**Gluetun** wraps Sonarr, Radarr, Lidarr, Prowlarr, Jackett, FlareSolverr, SABnzbd, qBittorrent, and Bazarr in a VPN tunnel. Services attach via `network_mode: "service:gluetun"`.

Seerr and Subsonic connect directly to `traefik-net` — no VPN needed for media serving.

---

## 🚀 Quick Start

**1. Clone the repository**
```bash
git clone https://github.com/martinargalas/mediaserver.git
cd mediaserver
```

**2. Create your `.env` file**
```bash
cp .env.example .env
# Edit .env and fill in all values
```

**3. Start the stack**
```bash
docker compose up -d
```

**4. Access your services** (requires local DNS resolving `.lab` — e.g. via Pi-hole or `/etc/hosts`)

| App | URL |
|-----|-----|
| Sonarr | http://sonarr.lab |
| Radarr | http://radarr.lab |
| Lidarr | http://lidarr.lab |
| SABnzbd | http://sabnzbd.lab |
| qBittorrent | http://qbittorrent.lab |
| Prowlarr | http://prowlarr.lab |
| Jackett | http://jackett.lab |
| Bazarr | http://bazarr.lab |
| Seerr | http://seerr.lab |
| Subsonic | http://subsonic.lab |

---

## 📁 Repository Structure

```
.
├── docker-compose.yaml       # Main Docker Compose file
├── .env.example              # Environment variable template
├── README.md
└── config/
    ├── bazarr/               # Bazarr configuration
    ├── jackett/              # Jackett configuration
    ├── lidarr/               # Lidarr configuration
    ├── prowlarr/             # Prowlarr configuration
    ├── qbittorrent/          # qBittorrent configuration
    ├── radarr/               # Radarr configuration
    ├── sabnzbd/              # SABnzbd configuration
    ├── seerr/                # Seerr configuration
    └── sonarr/               # Sonarr configuration
```

---

## 💰 Monthly Cost Estimate

| Service | Description | Cost / Month |
|---------|-------------|-------------:|
| **Surfshark VPN** | VPN provider | €2.60 (~65 CZK) |
| **Thundernews** | Usenet access | €11.10 (~279 CZK) |
| **Titulky.com VIP** | Subtitle provider | €1.10 (~27 CZK) |
| **Plex Pass** *(optional)* | Plex premium features | €6.00 (~151 CZK) |

**Total (without Plex Pass):** ~€14.80 / month  
**Total (with Plex Pass):** ~€20.80 / month

---

## 📝 Notes

- Use **Pi-hole** or add entries to `/etc/hosts` to resolve `.lab` domains locally.
- All config folders are mapped to `${MEDIA_BASE_PATH}/config/<service>` — set this in your `.env`.
- Run **Plex directly on the host** for best transcoding performance on macOS.
- Never commit your `.env` file — it is listed in `.gitignore`.

---

© 2025 Martin Argalaš
