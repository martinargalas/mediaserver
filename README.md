# 🧩 Home Media Stack (anonymized)

Tento repozitář obsahuje anonymizovanou a připravenou verzi README pro *Home Media Stack* — domácí server pro automatizované stahování a správu filmů a seriálů, postavený na Docker Compose s Traefikem, Gluetun VPN a Pi-hole.

> **Poznámka:** Všechny citlivé údaje (uživatelská jména, hesla, API klíče, cesty k volumes) byly nahrazeny placeholdery. Před nasazením doplňte soubor `.env` a upravte cesty podle vašeho systému.

---

## 🚀 Co tento repozitář obsahuje
- Anonymizovaný `docker-compose.yml` s následujícími kontejnery (počet kontejnerů zachován):
  - gluetun (VPN)
  - sabnzbd + sabnzbd-proxy
  - sonarr + sonarr-proxy
  - radarr + radarr-proxy
  - prowlarr + prowlarr-proxy
  - bazarr + bazarr-proxy
  - overseerr
  - traefik
- Pokyny pro spuštění a nastavení interního DNS (Pi-hole není součástí tohoto compose; nastavte ho samostatně nebo použijte existující instanci).

---

## 🔒 Bezpečnost a citlivá data
- Uložte všechna citlivá data do souboru `.env` a **nikdy** je necommitujte do GitHubu.
- Příklady proměnných v `.env`:

```env
# VPN
OPENVPN_USER=your_vpn_user
OPENVPN_PASSWORD=your_vpn_password
OPENVPN_CUSTOM_CONFIG=/gluetun/surfshark.ovpn

# Obecné
TZ=Europe/Prague
PUID=1000
PGID=1000
```

---

## 🧭 Síťová architektura (přehled)
Stack běží v lokální síti s reverzní proxy (Traefik) a oddělenou sítí pro služby směrované přes VPN (gluetun-net). Traefik vystavuje služby přes interní DNS názvy (např. `sonarr.lab`).

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
 [ Gluetun VPN ]
```

---

## 📌 Služby a standardní porty (anonymizováno)
| Služba | Port | Popis |
|---|---:|---|
| Sonarr | `8989` | Správa seriálů |
| Radarr | `7878` | Správa filmů |
| SABnzbd | `8081` | NZB download klient |
| Prowlarr | `9696` | Indexery |
| Bazarr | `6767` | Titulky a správa titulků |
| Overseerr | `5055` | Žádanky na obsah |
| Traefik dashboard | `8085` | Admin dashboard |

> Traefik směruje požadavky na tyto služby přes interní DNS (např. `sonarr.lab`).

---

## 💰 Měsíční náklady (říjen 2025)
| Služba | Popis | Cena / měsíc |
|---|---|---:|
| Surfshark VPN | VPN připojení | **65 Kč** |
| Thundernews | Usenet přístup | **279 Kč** |
| Titulky.com VIP | Přístup k titulkovým databázím | **27 Kč** |
| Plex Pass (volitelné) | Prémiové funkce Plexu | **151 Kč** |

**Celkem (bez Plex Passu):** **371 Kč / měsíc**  
**Celkem (s Plex Pass):** **522 Kč / měsíc**

---

## 🧭 Spuštění (rychlý návod)
1. Naklonujte repozitář a přejděte do složky:

```bash
git clone https://github.com/<tvoje-repo>/home-media-stack.git
cd home-media-stack
```

2. Vytvořte `.env` podle příkladu výše a upravte cesty (`./config/...`) pokud je třeba.

3. Spusťte stack:

```bash
docker compose -f docker-compose.anonymized.yml up -d
```

4. Přístup k aplikacím přes interní DNS (přizpůsobte podle Pi-hole):
- `http://sonarr.lab`
- `http://radarr.lab`
- `http://sabnzbd.lab`
- `http://prowlarr.lab`
- `http://bazarr.lab`
- `http://overseerr.lab`
- `http://traefik.lab:8085` (Traefik dashboard)

---

## 🧾 Poznámky k úpravám
- Cesty k volumes byly změněny na relativní (`./config/...`) — upravte je podle vašeho Macu.
- Všechny skutečné přihlašovací údaje, API klíče a soubory OVPN byly nahrazeny placeholdery.
- Pokud provozujete Plex mimo Docker (na hostu), ponechte jej mimo tento compose; přidejte `plex.lab` záznam do Pi-hole.

---

© 2025 Home Media Stack (anonymized) by Martin Argalaš

