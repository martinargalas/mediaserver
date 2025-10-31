# 🧩 Home Media Stack (Anonymized)

This repository provides an **anonymized, production-ready Home Media Stack** — a self-hosted solution for **automated movie and TV show management** using Docker Compose with **Traefik**, **Gluetun VPN**, and **Pi-hole**.

> 🧠 All sensitive data (usernames, passwords, API keys, volume paths) have been replaced with placeholders. You’ll need to fill them in locally before deployment.

---

## 🚀 Overview
This stack brings together:
- **Traefik** – Reverse proxy and internal routing via DNS hostnames
- **Gluetun (VPN)** – Privacy layer using Surfshark or any custom OpenVPN configuration
- **Usenet tools** – Sonarr, Radarr, SABnzbd, Prowlarr
- **Subtitle management** – Bazarr
- **Request system** – Overseerr
- **Optional Pi-hole DNS** – For ad blocking and internal `.lab` domain resolution

All components are containerized for easy management and isolation.

---

## 💻 Hardware & Environment
This setup runs smoothly on:
- **Mac Mini (Apple Silicon, ARM64)** or any low-power home server
- **OS:** macOS Sonoma (or Linux)
- **Docker Engine:** Native mode (no VM layer required)

Plex is intentionally **not containerized** in this setup due to performance issues with hardware transcoding over virtualized networking on macOS (e.g., OrbStack). Run Plex directly on the host machine.

---

## ⚙️ Architecture
```
Local Network (LAN)
        │
        ▼
   [ Traefik Proxy ]
        │
 ┌──────┼────────────────────────────────────────┐
 │      │                                        │
 ▼      ▼                                        ▼
Sonarr  Radarr  Sabnzbd  Prowlarr          Pi-hole DNS
 │       │        │         │                   │
 │       └────────┴─────────┴───────────────────┘
 │
 ▼
 [ Gluetun VPN (Surfshark) ]
```

---

## 🧱 Networks
- **traefik-net** – Main network for Traefik and services exposed via internal DNS
- **gluetun-net** – Isolated VPN network for Sonarr, Radarr, SABnzbd, Prowlarr, and Bazarr

---

## 🧩 Services & Default Ports
| Service | Port | Description |
|----------|------|-------------|
| **Sonarr** | 8989 | TV show management and automation |
| **Radarr** | 7878 | Movie management and automation |
| **SABnzbd** | 8081 | NZB download client |
| **Prowlarr** | 9696 | Unified indexer manager |
| **Bazarr** | 6767 | Subtitle management |
| **Overseerr** | 5055 | Request management and Plex integration |
| **Traefik Dashboard** | 8085 | Web UI for Traefik monitoring |

Each service is accessible through **internal DNS** (managed by Pi-hole), e.g., `http://sonarr.lab`, `http://radarr.lab`, etc.

---

## 🔐 VPN & Privacy Layer
**Gluetun** tunnels traffic from Sonarr, Radarr, SABnzbd, Prowlarr, and Bazarr through a secure VPN connection.

### Example `.env` configuration
```env
# VPN credentials
OPENVPN_USER=your_vpn_username
OPENVPN_PASSWORD=your_vpn_password
OPENVPN_CUSTOM_CONFIG=/gluetun/surfshark.ovpn

# General settings
TZ=Europe/Prague
PUID=1000
PGID=1000
```

Ensure your `.env` file is **never committed** to your GitHub repository.

---

## 💰 Monthly Cost Estimate (October 2025)
| Service | Description | Cost / Month |
|----------|--------------|---------------:|
| **Surfshark VPN** | VPN provider | **€2.60 (≈65 CZK)** |
| **Thundernews** | Usenet access | **€11.10 (≈279 CZK)** |
| **Titulky.com VIP** | Subtitle provider | **€1.10 (≈27 CZK)** |
| **Plex Pass** *(optional)* | Plex premium features | **€6.00 (≈151 CZK)** |

**Total (without Plex Pass):** €14.80 / month (≈371 CZK)  
**Total (with Plex Pass):** €20.80 / month (≈522 CZK)

---

## 🧭 Quick Start Guide
1️⃣ **Clone the repository**
```bash
git clone https://github.com/<your-username>/home-media-stack.git
cd home-media-stack
```

2️⃣ **Create and fill your `.env` file** (see example above)

3️⃣ **Start the containers**
```bash
docker compose -f docker-compose.anonymized.yml up -d
```

4️⃣ **Access your applications** (via Pi-hole DNS)
- `http://sonarr.lab`
- `http://radarr.lab`
- `http://sabnzbd.lab`
- `http://prowlarr.lab`
- `http://bazarr.lab`
- `http://overseerr.lab`
- `http://traefik.lab:8085` (Traefik Dashboard)

---

## 🧰 Notes & Recommendations
- Use **Pi-hole** or another local DNS solution to resolve internal `.lab` domains.
- All **media directories** (`./media/...`) and **config folders** (`./config/...`) are relative — adjust paths to your system.
- Run **Plex directly on the host machine** (not in Docker) for better transcoding performance on macOS.
- Never expose Traefik or Gluetun directly to the internet without authentication or TLS.

---

## 🧾 Repository Files
- `docker-compose.anonymized.yml` – Main Docker Compose file (anonymized)
- `.env.example` – Template for environment variables
- `README.md` – This documentation file

---

© 2025 Home Media Stack (Anonymized) by Martin Argalaš

