# 🧩 Home Media Stack with Traefik, Gluetun VPN, and Pi-hole

Tento stack poskytuje kompletní prostředí pro **automatizované stahování a správu filmů a seriálů** s důrazem na:
- **Soukromí** (přes Gluetun VPN)
- **Interní DNS** (přes Pi-hole)
- **Centralizovaný přístup** (přes Traefik a interní DNS domény)

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
 [ Gluetun VPN ]
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

---

## 🧱 Sítě

- **traefik-net** – hlavní síť pro reverzní proxy (Traefik + Pi-hole)
- **gluetun-net** – izolovaná síť pro služby běžící za VPN (Sonarr, Radarr, Sabnzbd, Prowlarr)

---

## 🔒 VPN (Gluetun)

Gluetun směruje síťový provoz služeb **Sonarr, Radarr, Sabnzbd, Prowlarr** přes VPN.
Do `.env` souboru přidej přihlašovací údaje pro OpenVPN:

```env
VPN_USER=tvuj_vpn_uzivatel
VPN_PASSWORD=tvuj_vpn_heslo
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

> 💡 V Pi-hole otevři **Local DNS → DNS Records** a přidej jednotlivé názvy, které budou směřovat na IP stroje, kde běží Docker.

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
VPN_USER=tvuj_vpn_uzivatel
VPN_PASSWORD=tvuj_vpn_heslo
TZ=Europe/Prague
```

### 3️⃣ Spuštění kontejnerů
```bash
docker compose up -d
```

---

## 🧰 Poznámky
- Každá aplikace je dostupná přes interní DNS název (např. `http://sonarr.lab`).
- Traefik poskytuje reverzní proxy a správu DNS směrování.
- VPN Gluetun chrání síťový provoz všech služeb běžících v síti `gluetun-net`.

---

## 🧠 Doporučení
- Pro produkční použití je vhodné uložit citlivé údaje do `.env` souboru, který nebude součástí repozitáře (`.gitignore`).
- Pokud používáš Pi-hole i jako DHCP server, doménové názvy budou dostupné automaticky.
