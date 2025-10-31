# Media Server Stack (Docker Compose)

Tento stack kombinuje oblíbené aplikace pro automatizaci stahování a správy médií (Sonarr, Radarr, Bazarr, Prowlarr, SABnzbd, Overseerr) za použití VPN přes Gluetun a reverzního proxy Traefik.

Celý systém je připraven tak, aby:
- Byl bezpečně připojen k internetu přes VPN (např. Surfshark),
- Byl přístupný v lokální síti přes **DNS názvy spravované Pi-hole** (např. `sonarr.lab`, `radarr.lab`, `traefik.lab`),
- Byl snadno rozšiřitelný o další kontejnery.

---

## ⚙️ Služby

| Aplikace | Popis | Výchozí port | Přístup přes DNS |
|-----------|--------|---------------|------------------|
| **Traefik** | Reverzní proxy + dashboard | `8085` | `http://traefik.lab` |
| **Gluetun** | VPN gateway pro všechny ostatní kontejnery | — | — |
| **SABnzbd** | Stahování z Usenetu | `8080` | `http://sabnzbd.lab` |
| **Sonarr** | TV seriály | `8989` | `http://sonarr.lab` |
| **Radarr** | Filmy | `7878` | `http://radarr.lab` |
| **Bazarr** | Titulky | `6767` | `http://bazarr.lab` |
| **Prowlarr** | Indexery | `9696` | `http://prowlarr.lab` |
| **Overseerr** | Požadavky od uživatelů | `5055` | `http://overseerr.lab` |

---

## 🧩 Struktura adresářů

Před spuštěním si vytvořte následující strukturu složek (můžete upravit dle potřeby):

MediaServer/
├─ config/
│ ├─ gluetun/
│ ├─ sabnzbd/
│ ├─ sonarr/
│ ├─ radarr/
│ ├─ prowlarr/
│ ├─ bazarr/
│ └─ overseerr/
├─ Filmy/
├─ Seriály/
└─ Temp/


---

## 🔑 Proměnné k doplnění

V `docker-compose.yml` doplňte:

| Proměnná | Popis |
|-----------|--------|
| `<zde_vložte_své_vpn_uživatelské_jméno>` | Uživatelské jméno pro VPN |
| `<zde_vložte_své_vpn_heslo>` | Heslo k VPN |
| `<zde_vložte_název_souboru_ovpn>` | Název `.ovpn` konfigurace vašeho VPN poskytovatele |
| `<zde_vložte_uid>` / `<zde_vložte_gid>` | UID/GID uživatele, pod kterým běží Docker (např. 1000/1000) |
| `<zde_vložte_cestu_...>` | Lokální cesty na hostiteli (např. `/home/user/MediaServer/...`) |

---

## 🚀 Spuštění

```bash
docker compose up -d

Poté budete moci přistupovat k aplikacím přes své lokální DNS (např. přes Pi-hole), např.:

http://sonarr.lab
http://radarr.lab
http://prowlarr.lab

🧠 Poznámka k DNS (Pi-hole)

Pokud používáte Pi-hole jako DNS server, přidejte do /etc/hosts (nebo přímo do Pi-hole DNS records) následující záznamy:

192.168.x.x sonarr.lab
192.168.x.x radarr.lab
192.168.x.x prowlarr.lab
192.168.x.x overseerr.lab
192.168.x.x traefik.lab
192.168.x.x bazarr.lab
192.168.x.x sabnzbd.lab

Požadavky

Docker & Docker Compose (v2+)

Funkční Pi-hole (volitelné)

VPN účet (např. Surfshark, Mullvad apod.)

64bit Linux / macOS systém


📜 Licence

MIT License © 2025
Vytvořeno jako open-source domácí media stack s důrazem na bezpečnost a přehlednost.



