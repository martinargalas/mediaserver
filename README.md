# 🧩 Home Media Stack with Traefik, Gluetun VPN, and Pi-hole

Tento stack poskytuje kompletní prostředí pro **automatizované stahování a správu filmů a seriálů** s důrazem na:
- **Soukromí** (přes Gluetun VPN a Surfshark)
- **Interní DNS** (přes Pi-hole)
- **Centralizovaný přístup** (přes Traefik a interní DNS domény)
- **Lokalní přehrávání** (Plex server)

---

## 💻 Hardwarové prostředí

Celý stack běží na:
- **Mac Mini M4 (2024)** – tichý, úsporný a výkonný domácí server  
- **Operační systém:** macOS Sonoma  
- **Architektura:** ARM64  
- **Docker Engine:** běžící v nativním režimu (bez virtualizace)  
- **Plex server:** běží přímo na Macu, nikoli v Dockeru, protože při spuštění Plexu v Dockeru přes OrbStack byl problém se sítí a převodem videa (přenos byl max 10 Mbps)

---

## ⚙️ Architektura

### Přehled
```
Lokální síť (LAN)
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

### Popis služeb

| Služba     | Port | Přístup přes DNS        | Účel |
|-------------|------|-------------------------|------|
| **Sonarr**  | 8989 | [http://sonarr.lab](http://sonarr.lab) | Správa a automatické stahování seriálů |
| **Radarr**  | 7878 | [http://radarr.lab](http://radarr.lab) | Správa a automatické stahování filmů |
| **Sabnzbd** | 8081 | [http://sabnzbd.lab](http://sabnzbd.lab) | Download klient pro NZB |
| **Prowlarr**| 9696 | [http://prowlarr.lab](http://prowlarr.lab) | Centrální indexery pro Sonarr/Radarr |
| **Pi-hole** | 8082 | [http://pihole.lab](http://pihole.lab) | DNS resolver, blokování reklam, správa interních domén |
| **Gluetun** | —    | —                       | VPN vrstva pro ochranu soukromí |
| **Plex**    | 32400 | [http://plex.lab:32400](http://plex.lab:32400) | Streamování filmů a seriálů v lokální síti |

---

## 🧱 Sítě

- **traefik-net** – hlavní síť pro reverzní proxy (Traefik + Pi-hole)
- **gluetun-net** – izolovaná síť pro služby běžící za VPN (Sonarr, Radarr, Sabnzbd, Prowlarr)

---

## 🔒 VPN (Gluetun + Surfshark)

Gluetun směruje síťový provoz služeb **Sonarr, Radarr, Sabnzbd, Prowlarr** přes VPN.

Použitý poskytovatel VPN: **Surfshark**

Do `.env` souboru přidej přihlašovací údaje:

```env
VPN_USER=<zde vlož své VPN uživatelské jméno>
VPN_PASSWORD=<zde vlož své VPN heslo>
```

---

## 🌐 Pi-hole a interní DNS

Pi-hole běží na portu `8082` a kromě blokování reklam poskytuje interní DNS pro přístup ke službám:

| Doména | IP (Pi-hole nastaví automaticky nebo ručně) |
|---------|---------------------------------------------|
| sonarr.lab | 192.168.1.x |
| radarr.lab | 192.168.1.x |
| sabnzbd.lab | 192.168.1.x |
| prowlarr.lab | 192.168.1.x |
| pihole.lab | 192.168.1.x |
| plex.lab | 192.168.1.x |

> 💡 V Pi-hole otevři **Local DNS → DNS Records** a přidej jednotlivé názvy, které budou směřovat na IP stroje, kde běží Docker.

---

## 💰 Měsíční náklady (říjen 2025)

| Služba | Popis | Cena / měsíc |
|---------|--------|---------------|
| **Surfshark VPN** | VPN připojení | **65 Kč** |
| **Thundernews** | Usenet přístup | **279 Kč** |
| **Plex Pass** *(volitelné)* | Prémiové funkce Plexu (není nutné) | **151 Kč** |

**Celkem (bez Plex Passu):** 344 Kč / měsíc  
**Celkem (s Plex Pass):** 495 Kč / měsíc

---

## 🧭 Spuštění

### 1️⃣ Klonování projektu
```bash
git clone https://github.com/<tvoje-repo>/home-media-stack.git
cd home-media-stack
```

### 2️⃣ Nastavení proměnných
Vytvoř `.env` soubor (pokud ještě neexistuje):
```bash
VPN_USER=<tvůj VPN uživatel>
VPN_PASSWORD=<tvé VPN heslo>
TZ=Europe/Prague
```

### 3️⃣ Spuštění kontejnerů
```bash
docker compose -f docker-compose.home-media.yml up -d
```

### 4️⃣ Přístup k aplikacím
- `http://sonarr.lab`  
- `http://radarr.lab`  
- `http://sabnzbd.lab`  
- `http://prowlarr.lab`  
- `http://plex.lab`

---

## 🧰 Poznámky

- Každá aplikace je dostupná přes interní DNS název (např. `http://sonarr.lab`).  
- Traefik poskytuje reverzní proxy a správu DNS směrování.
- VPN Gluetun chrání síťový provoz všech služeb běžících v síti `gluetun-net`.
- Plex běží přímo na Macu kvůli problémům s přenosem videa při použití Dockeru přes OrbStack.

---

## 🧠 Doporučení

- Citlivé údaje (VPN, API klíče) ukládej do `.env` souboru – **nikdy** je nepřidávej do GitHub repozitáře.
- Pro přístup zvenčí můžeš přidat Traefik HTTPS terminaci a autentizaci.
- Pokud používáš Pi-hole i jako DHCP server, doménové názvy budou dostupné automaticky.

© 2025 Home Media Stack by Martin Argalaš
