<p align="center">
  <h1 align="center">🛰️ S H A D O W B R O K E R</h1>
  <p align="center"><strong>Global Threat Intercept — Real-Time Geospatial Intelligence Platform</strong></p>
  <p align="center">

  </p>
</p>

## 🏗️ Architecture

ShadowBroker v0.9.7 is composed of three vertically-stacked planes — the **Operator UI**, the **Backend Service Plane**, and the **Decentralized Layer (InfoNet)** — plus two cross-cutting bridges (the **Time Machine** and the **Agentic AI Channel**, which is the protocol that OpenClaw and any other compatible agent connects through) and a **Privacy Core** Rust crate that backstops both the legacy mesh and the future shielded coin / DEX work.

```
╔═════════════════════════════════════════════════════════════════════════════╗
║                       OPERATOR UI  (Next.js + MapLibre)                     ║
║                                                                             ║
║  ┌────────────────┐  ┌──────────┐  ┌────────────────┐  ┌────────────────┐   ║
║  │ MapLibre GL    │  │ NewsFeed │  │ Sovereign Shell│  │   Mesh Chat    │   ║
║  │  WebGL render  │  │  SIGINT  │  │  Petitions /   │  │  + Mesh Term.  │   ║
║  │  + clusters    │  │  GDELT   │  │  Upgrades /    │  │  (Infonet /    │   ║
║  │                │  │  Threat  │  │  Disputes /    │  │   Mesh /       │   ║
║  │                │  │          │  │  Gates /       │  │   Dead Drop)   │   ║
║  │                │  │          │  │  Bootstrap /   │  │                │   ║
║  │                │  │          │  │  Function Keys │  │                │   ║
║  └──────┬─────────┘  └────┬─────┘  └────────┬───────┘  └────────┬───────┘   ║
║         │                 │                 │                   │           ║
║  ┌──────┴─────────────────┴─────────────────┴───────────────────┴───────┐   ║
║  │  Time Machine ◀── snapshot playback ── snapshotMode toggle ──▶ Live │   ║
║  │  hourly index │ frame interpolation │ profile-aware │ per-tier ETag  │   ║
║  └──────────────────────────────────┬───────────────────────────────────┘   ║
║                                     │ REST  +  /api/[...path] proxy         ║
╠═════════════════════════════════════╪═══════════════════════════════════════╣
║                       BACKEND SERVICE PLANE  (FastAPI)                      ║
║                                     │                                       ║
║  ┌──────────────────────────────────┴────────────────────────────────────┐  ║
║  │              Data Fetcher  (APScheduler — fast / slow tiers)          │  ║
║  │                                                                       │  ║
║  │  ┌───────────┬───────────┬───────────┬───────────┬───────────┐        │  ║
║  │  │  OpenSky* │ adsb.lol  │ CelesTrak │   USGS    │   AIS WS  │        │  ║
║  │  │  Flights  │ Military  │   Sats    │  Quakes   │   Ships   │        │  ║
║  │  ├───────────┼───────────┼───────────┼───────────┼───────────┤        │  ║
║  │  │  Carrier  │   GDELT   │ CCTV (12) │ DeepState │   NASA    │        │  ║
║  │  │  Tracker  │ Conflict  │  Cameras  │ Frontline │   FIRMS   │        │  ║
║  │  ├───────────┼───────────┼───────────┼───────────┼───────────┤        │  ║
║  │  │   GPS     │  KiwiSDR  │  Shodan   │  Amtrak   │  SatNOGS  │        │  ║
║  │  │  Jamming  │   Radios  │  Devices  │ DigiTraf  │  TinyGS   │        │  ║
║  │  ├───────────┼───────────┼───────────┼───────────┼───────────┤        │  ║
║  │  │ Volcanoes │  Weather  │  Fishing  │ Mil Bases │   IODA    │        │  ║
║  │  │  Air Qual │  Alerts   │  Activity │ PwrPlants │  Outages  │        │  ║
║  │  ├───────────┼───────────┼───────────┼───────────┼───────────┤        │  ║
║  │  │ Sentinel  │   MODIS   │   VIIRS   │   Data    │ Meshtastic│        │  ║
║  │  │  Hub/STAC │   Terra   │ Nightlts  │  Centers  │   APRS    │        │  ║
║  │  ├───────────┴───────────┴───────────┴───────────┴───────────┤        │  ║
║  │  │  SAR (NEW v0.9.7)                                         │        │  ║
║  │  │   Mode A: ASF Search catalog (free, no account)           │        │  ║
║  │  │   Mode B: NASA OPERA / Copernicus EGMS / GFM / EMS /      │        │  ║
║  │  │           UNOSAT  ground-change anomalies (opt-in)        │        │  ║
║  │  └───────────────────────────────────────────────────────────┘        │  ║
║  │   * OpenSky: REQUIRED for global flight coverage                      │  ║
║  └───────────────────────────────────────────────────────────────────────┘  ║
║                                     │                                       ║
║  ┌──────────────────────────────────┴────────────────────────────────────┐  ║
║  │                   Snapshot Store  (Time Machine source)               │  ║
║  │   Hourly index  │  per-snapshot layer manifest  │  profile metadata   │  ║
║  └───────────────────────────────────────────────────────────────────────┘  ║
║                                                                             ║
║  ┌───────────────────────────────────────────────────────────────────────┐  ║
║  │   Agentic AI Channel  (HMAC-SHA256, tier-gated  —  OpenClaw + others) │  ║
║  │                                                                       │  ║
║  │   POST /api/ai/channel/command   →  one tool call                     │  ║
║  │   POST /api/ai/channel/batch     →  up to 20 concurrent tool calls    │  ║
║  │                                                                       │  ║
║  │   Tier:   restricted (read-only)   │   full (read + write + inject)   │  ║
║  │   Auth:   X-SB-Timestamp + X-SB-Nonce + X-SB-Signature                │  ║
║  │   Sig  =  HMAC-SHA256(secret, METHOD|path|ts|nonce|sha256(body))      │  ║
║  └───────────────────────────────────────────────────────────────────────┘  ║
╠═════════════════════════════════════════════════════════════════════════════╣
║                  DECENTRALIZED LAYER  (InfoNet Testnet — signed events)     ║
║                                                                             ║
║  ┌────────────────────────────┐    ┌──────────────────────────────────┐     ║
║  │    Mesh Hashchain          │    │   Sovereign Shell Governance     │     ║
║  │                            │    │                                  │     ║
║  │  Ed25519 signed events     │    │  Petitions  (DSL: UPDATE_PARAM,  │     ║
║  │  Public-key binding        │    │              ENABLE_FEATURE …)   │     ║
║  │  Replay / sequence guard   │    │  Upgrade-Hash voting (80% / 40%  │     ║
║  │  Two-tier finality         │    │             quorum / 67% Heavy)  │     ║
║  │   ├ Tier 1 (CRDT, fast)    │    │  Resolution & Dispute markets    │     ║
║  │   └ Tier 2 (epoch finality)│    │  Gate suspend / shutdown / appeal│     ║
║  │  Identity rotation         │    │  Bootstrap eligible-node-1-vote  │     ║
║  │  Constitutional invariants │    │   (Argon2id PoW, Heavy-Node only)│     ║
║  │  (MappingProxyType)        │    │  Function Keys (5 of 6 pieces)   │     ║
║  └─────────────┬──────────────┘    └─────────────┬────────────────────┘     ║
║                │                                 │                          ║
║                └──────────────┬──────────────────┘                          ║
║                               │                                             ║
║  ┌────────────────────────────┴──────────────────────────────────────┐      ║
║  │            Wormhole / InfoNet Relay  (transport layer)            │      ║
║  │   Gate personas │ canonical signing │ Dead Drop epoch mailboxes   │      ║
║  └───────────────────────────────────────────────────────────────────┘      ║
╠═════════════════════════════════════════════════════════════════════════════╣
║              PRIVACY CORE  (Rust crate — locked Protocol contracts)         ║
║                                                                             ║
║   privacy-core/  ─►  Argon2id │ Ed25519/X25519 │ AESGCM │ HKDF              ║
║                      Ring sigs* │ Stealth addrs* │ Pedersen* │ Bulletproofs*║
║                      Blind-sig issuance* (RSA / BBS+ / U-Prove / Idemix)    ║
║                                                                             ║
║   * = locked Protocol contract; cryptographic primitive lands Sprint 11+    ║
╚═════════════════════════════════════════════════════════════════════════════╝

   Distribution
   ────────────
     GitHub (primary):  ghcr.io/bigbodycobain/shadowbroker-{backend,frontend}
     GitLab (mirror):   registry.gitlab.com/bigbodycobain/shadowbroker/{backend,frontend}
     Multi-arch:        linux/amd64  +  linux/arm64  (Raspberry Pi 5 supported)
     Desktop:           Tauri shell  →  packaged backend-runtime  +  Next.js frontend
```

---

## 📊 Data Sources & APIs

| Source | Data | Update Frequency | API Key Required |
|---|---|---|---|
| [OpenSky Network](https://opensky-network.org) | Commercial & private flights | ~60s | **Yes** |
| [adsb.lol](https://adsb.lol) | Military aircraft | ~60s | No |
| [aisstream.io](https://aisstream.io) | AIS vessel positions | Real-time WebSocket | **Yes** |
| [CelesTrak](https://celestrak.org) | Satellite orbital positions (TLE + SGP4) | ~60s | No |
| [USGS Earthquake](https://earthquake.usgs.gov) | Global seismic events | ~60s | No |
| [GDELT Project](https://www.gdeltproject.org) | Global conflict events | ~6h | No |
| [DeepState Map](https://deepstatemap.live) | Ukraine frontline | ~30min | No |
| [Shodan](https://www.shodan.io) | Internet-connected device search | On-demand | **Yes** |
| [OpenSanctions](https://www.opensanctions.org) | OFAC SDN sanctions index (recon + entity graph) | 24h cache | No |
| [abuse.ch Feodo + URLhaus](https://abuse.ch) | Malware C2 / distribution URLs | ~5min (opt-in layer) | No |
| [CISA KEV](https://www.cisa.gov/known-exploited-vulnerabilities-catalog) | Known exploited CVEs | ~5min (opt-in layer) | No |
| [ip-api.com](https://ip-api.com) | IP geolocation (recon, entity graph) | On-demand | No |
| [Google Public DNS](https://dns.google) | DNS-over-HTTPS lookups (recon) | On-demand | No |
| [RDAP.org](https://rdap.org) | Domain registration data (recon) | On-demand | No |
| [crt.sh](https://crt.sh) | Certificate transparency (recon) | On-demand | No |
| [bgpview.io](https://bgpview.io) | BGP/ASN routing (recon) | On-demand | No |
| TeleGeography (static) | Submarine cable routes | Static | No |
| [ASFINAG](https://www.asfinag.at) | Austria motorway webcams | ~10min | No |
| [Amtrak](https://www.amtrak.com) | US train positions | ~60s | No |
| [DigiTraffic](https://www.digitraffic.fi) | European rail positions | ~60s | No |
| [Global Fishing Watch](https://globalfishingwatch.org) | Fishing vessel activity events | ~1hr | **Yes** (`GFW_API_TOKEN`) |
| [Telegram public previews](https://t.me/s) | War/OSINT channel posts (`telegram_osint`) | ~1hr | No (optional `TELEGRAM_OSINT_CHANNELS`) |
| Transport for London, NYC DOT, TxDOT | CCTV cameras (UK, US) | ~10min | No |
| Caltrans, WSDOT, GDOT, IDOT, MDOT | CCTV cameras (5 US states) | ~10min | No |
| Spain DGT, Madrid City | CCTV cameras (Spain) | ~10min | No |
| [Singapore LTA](https://datamall.lta.gov.sg) | Singapore traffic cameras | ~10min | **Yes** |
| [Windy Webcams](https://www.windy.com) | Global webcams | ~10min | No |
| [SatNOGS](https://satnogs.org) | Amateur satellite ground stations | ~30min | No |
| [TinyGS](https://tinygs.com) | LoRa satellite ground stations | ~30min | No |
| [Meshtastic MQTT](https://meshtastic.org) | Mesh radio node positions | Real-time | No |
| [APRS-IS](https://www.aprs-is.net) | Amateur radio positions | Real-time TCP | No |
| [KiwiSDR](https://kiwisdr.com) | Public SDR receiver locations | ~30min | No |
| [OpenMHZ](https://openmhz.com) | Police/fire scanner feeds | Real-time | No |
| [Smithsonian GVP](https://volcano.si.edu) | Holocene volcanoes worldwide | Static (cached) | No |
| [OpenAQ](https://openaq.org) | Air quality PM2.5 stations | ~120s | No |
| NOAA / NWS | Severe weather alerts & polygons | ~120s | No |
| [WRI Global Power Plant DB](https://datasets.wri.org) | 35,000+ power plants | Static (cached) | No |
| Military base datasets | Global military installations | Static (cached) | No |
| [NASA FIRMS](https://firms.modaps.eosdis.nasa.gov) | NOAA-20 VIIRS fire/thermal hotspots | ~120s | No |
| [NOAA SWPC](https://services.swpc.noaa.gov) | Space weather Kp index & solar events | ~120s | No |
| [IODA (Georgia Tech)](https://ioda.inetintel.cc.gatech.edu) | Regional internet outage alerts | ~120s | No |
| [DC Map (GitHub)](https://github.com/Ringmast4r/Data-Center-Map---Global) | Global data center locations | Static (cached 7d) | No |
| [NASA GIBS](https://gibs.earthdata.nasa.gov) | MODIS Terra daily satellite imagery | Daily (24-48h delay) | No |
| [Esri World Imagery](https://www.arcgis.com) | High-res satellite basemap | Static (periodically updated) | No |
| [MS Planetary Computer](https://planetarycomputer.microsoft.com) | Sentinel-2 L2A scenes (right-click) | On-demand | No |
| [Copernicus CDSE](https://dataspace.copernicus.eu) | Sentinel Hub imagery (Process API) | On-demand | **Yes** (free) |
| [VIIRS Nightlights](https://eogdata.mines.edu) | Night-time light change detection | Static | No |
| [RestCountries](https://restcountries.com) | Country profile data | On-demand (cached 24h) | No |
| [Wikidata SPARQL](https://query.wikidata.org) | Head of state data | On-demand (cached 24h) | No |
| [Wikipedia API](https://en.wikipedia.org/api) | Location summaries & aircraft images | On-demand (cached) | No |
| [OSM Nominatim](https://nominatim.openstreetmap.org) | Place name geocoding (LOCATE bar) | On-demand | No |
| [CARTO Basemaps](https://carto.com) | Dark map tiles | Continuous | No |

**Outbound privacy & audit (#348–#366):** Each self-hosted install uses its own backend IP and per-install User-Agent handle. See [docs/OUTBOUND_DATA.md](docs/OUTBOUND_DATA.md) for what contacts third parties, opt-in/env controls, and accepted tradeoffs (CCTV Referer, basemap CDN, LiveUAMap, etc.).

---
## 🔧 Performance

The platform is optimized for handling massive real-time datasets:

* **Gzip Compression** — API payloads compressed ~92% (11.6 MB → 915 KB)
* **ETag Caching** — `304 Not Modified` responses skip redundant JSON parsing
* **Viewport Culling** — Only features within the visible map bounds (+20% buffer) are rendered
* **Imperative Map Updates** — High-volume layers (flights, satellites, fires) bypass React reconciliation via direct `setData()` calls
* **Clustered Rendering** — Ships, CCTV, earthquakes, and data centers use MapLibre clustering to reduce feature count
* **Debounced Viewport Updates** — 300ms debounce prevents GeoJSON rebuild thrash during pan/zoom; 2s debounce on dense layers (satellites, fires)
* **Position Interpolation** — Smooth 10s tick animation between data refreshes
* **React.memo** — Heavy components wrapped to prevent unnecessary re-renders
* **Coordinate Precision** — Lat/lng rounded to 5 decimals (~1m) to reduce JSON size

---

## 📁 Project Structure

```
Shadowbroker/
├── openclaw-skills/shadowbroker/   # OpenClaw skill — SKILL.md, sb_query.py client, alerts/monitor helpers
├── backend/
│   ├── main.py                     # FastAPI app, middleware, API routes (~4,000 lines)
│   ├── cctv.db                     # SQLite CCTV camera database (auto-generated)
│   ├── config/
│   │   └── news_feeds.json         # User-customizable RSS feed list
│   ├── services/
│   │   ├── data_fetcher.py         # Core scheduler — orchestrates all data sources
│   │   ├── ais_stream.py           # AIS WebSocket client (25K+ vessels)
│   │   ├── carrier_tracker.py      # OSINT carrier position estimator (GDELT news scraping)
│   │   ├── cctv_pipeline.py        # 14-source CCTV camera ingestion pipeline
│   │   ├── ssrf_guard.py           # SSRF validation for operator recon fetches
│   │   ├── sanctions/ofac.py       # OpenSanctions OFAC SDN index
│   │   ├── osint/lookups.py        # Server-side recon lookups (Osiris port)
│   │   ├── osint/openclaw_recon.py # OpenClaw dispatch for recon + entity_expand
│   │   ├── osint_intel/resolve.py  # Entity graph resolver (Wikidata + OFAC)
│   │   ├── scm/suppliers.py        # Supply-chain risk overlay
│   │   ├── intel_feeds/            # Country risk index helpers
│   │   ├── fetchers/malware.py     # abuse.ch Feodo + URLhaus
│   │   ├── fetchers/cyber_status.py # CISA KEV feed
│   │   ├── fetchers/telegram_osint.py # Public Telegram channel scrape + geoparse
│   │   ├── third_party/osiris/     # MIT attribution for Osiris-derived code
│   │   ├── geopolitics.py          # GDELT + Ukraine frontline + air alerts
│   │   ├── region_dossier.py       # Right-click country/city intelligence
│   │   ├── radio_intercept.py      # Police scanner feeds + OpenMHZ
│   │   ├── kiwisdr_fetcher.py      # KiwiSDR receiver scraper
│   │   ├── sentinel_search.py      # Sentinel-2 STAC imagery search
│   │   ├── shodan_connector.py     # Shodan device search connector
│   │   ├── sigint_bridge.py        # APRS-IS TCP bridge
│   │   ├── network_utils.py        # HTTP client with curl fallback
│   │   ├── api_settings.py         # API key management
│   │   ├── news_feed_config.py     # RSS feed config manager
│   │   ├── fetchers/
│   │   │   ├── flights.py          # OpenSky, adsb.lol, GPS jamming, holding patterns
│   │   │   ├── geo.py              # AIS vessels, carriers, GDELT, fishing activity
│   │   │   ├── satellites.py       # CelesTrak TLE + SGP4 propagation
│   │   │   ├── earth_observation.py # Quakes, fires, volcanoes, air quality, weather
│   │   │   ├── infrastructure.py   # Data centers, power plants, military bases
│   │   │   ├── trains.py           # Amtrak + DigiTraffic European rail
│   │   │   ├── sigint.py           # SatNOGS, TinyGS, APRS, Meshtastic
│   │   │   ├── meshtastic_map.py   # Meshtastic MQTT + map node aggregation
│   │   │   ├── military.py         # Military aircraft classification
│   │   │   ├── news.py             # RSS intelligence feed aggregation
│   │   │   ├── financial.py        # Global markets data
│   │   │   └── ukraine_alerts.py   # Ukraine air raid alerts
│   │   └── mesh/                   # InfoNet / Wormhole protocol stack
│   │       ├── mesh_protocol.py    # Core mesh protocol + routing
│   │       ├── mesh_crypto.py      # Ed25519, X25519, AESGCM primitives
│   │       ├── mesh_hashchain.py   # Hash chain commitment system (~1,400 lines)
│   │       ├── mesh_router.py      # Multi-transport router (APRS, Meshtastic, WS)
│   │       ├── mesh_wormhole_persona.py  # Gate persona identity management
│   │       ├── mesh_wormhole_dead_drop.py # Dead Drop token-based DM mailbox
│   │       ├── mesh_wormhole_ratchet.py   # Double-ratchet DM scaffolding
│   │       ├── mesh_wormhole_gate_keys.py # Gate key management + rotation
│   │       ├── mesh_wormhole_seal.py      # Message sealing + unsealing
│   │       ├── mesh_merkle.py      # Merkle tree proofs for data commitment
│   │       ├── mesh_reputation.py  # Node reputation scoring
│   │       ├── mesh_oracle.py      # Oracle consensus protocol
│   │       └── mesh_secure_storage.py # Secure credential storage
│   ├── routers/
│   │   ├── osint.py                # /api/osint/* recon routes (local operator)
│   │   ├── entity_graph.py         # /api/entity/expand
│   │   ├── scm.py                  # /api/scm-suppliers
│   │   └── intel_feeds.py          # /api/malware, /api/cyber-threats, /api/telegram-feed, /api/country-risk
├── frontend/
│   ├── public/data/
│   │   └── submarine-cables.json   # Static undersea cable GeoJSON
│   ├── src/
│   │   ├── app/
│   │   │   └── page.tsx            # Main dashboard — state, polling, layout
│   │   └── components/
│   │       ├── MaplibreViewer.tsx   # Core map — all GeoJSON layers
│   │       ├── MeshChat.tsx        # InfoNet / Mesh / Dead Drop chat panel
│   │       ├── MeshTerminal.tsx    # Draggable CLI terminal
│   │       ├── NewsFeed.tsx        # SIGINT feed + entity detail panels
│   │       ├── WorldviewLeftPanel.tsx   # Data layer toggles (40+ layers)
│   │       ├── ShodanPanel.tsx          # Shodan device search overlay
│   │       ├── ReconPanel.tsx           # Server-side OSINT recon toolkit
│   │       ├── ScmPanel.tsx             # Supply-chain risk command panel
│   │       ├── EntityGraphPanel.tsx     # Entity graph on map selection
│   │       ├── MaplibreViewer/popups/TelegramOsintPopup.tsx  # Threat-intercept styled Telegram pin popups
│   │       ├── WorldviewRightPanel.tsx  # Search + filter sidebar
│   │       ├── AdvancedFilterModal.tsx  # Airport/country/owner filtering
│   │       ├── MapLegend.tsx       # Dynamic legend with all icons
│   │       ├── MarketsPanel.tsx    # Global financial markets ticker
│   │       ├── RadioInterceptPanel.tsx # Scanner-style radio panel
│   │       ├── FindLocateBar.tsx   # Search/locate bar
│   │       ├── ChangelogModal.tsx  # Version changelog popup (auto-shows on upgrade)
│   │       ├── SettingsPanel.tsx   # API Keys + News Feed + Shodan config
│   │       ├── ScaleBar.tsx        # Map scale indicator
│   │       └── ErrorBoundary.tsx   # Crash recovery wrapper
│   └── package.json
```
---

### 💻 Developer Setup

If you want to modify the code or run from source:

#### Prerequisites

* **Node.js** 18+ and **npm** — [nodejs.org](https://nodejs.org/)
* **Python** 3.10, 3.11, or 3.12 with `pip` — [python.org](https://www.python.org/downloads/) (**check "Add to PATH"** during install)
  * ⚠️ Python 3.13+ may have compatibility issues with some dependencies. **3.11 or 3.12 is recommended.**
* API keys for: `aisstream.io` (required), and optionally `opensky-network.org` (OAuth2), `lta.gov.sg`

### Installation

```bash
# Clone the repository
git clone https://github.com/BigBodyCobain/Shadowbroker.git
cd Shadowbroker

# Backend setup
cd backend
python -m venv venv
venv\Scripts\activate        # Windows
# source venv/bin/activate   # macOS/Linux
pip install .

# Optional helper scripts (creates venv + installs dev deps)
# Windows PowerShell
# .\backend\scripts\setup-venv.ps1
# macOS/Linux
# ./backend/scripts/setup-venv.sh

# Optional env check (prints warnings for missing keys)
# Windows PowerShell
# .\backend\scripts\check-env.ps1
# macOS/Linux
# ./backend/scripts/check-env.sh

# Create .env with your API keys
echo "AIS_API_KEY=your_aisstream_key" >> .env
echo "OPENSKY_CLIENT_ID=your_opensky_client_id" >> .env
echo "OPENSKY_CLIENT_SECRET=your_opensky_secret" >> .env

# Frontend setup
cd ../frontend
npm ci
```

### Running

```bash
# From the frontend directory — starts both frontend & backend concurrently
npm run dev
```

This starts:

* **Next.js** frontend on `http://localhost:3000`
* **FastAPI** backend on `http://localhost:8000`

### Pre-commit (Optional)

If you use pre-commit, install hooks once from repo root:

```bash
pre-commit install
```

### Local AIS Receiver (Optional)

You can feed your own AIS ship data into ShadowBroker using an RTL-SDR dongle and [AIS-catcher](https://github.com/jvde-github/AIS-catcher), an open-source AIS decoder. This gives you real-time coverage of vessels in your local area — no API key needed.

1. Plug in an RTL-SDR dongle
2. Install AIS-catcher ([releases](https://github.com/jvde-github/AIS-catcher/releases)) or use the Docker image:
   ```bash
   docker run -d --device /dev/bus/usb \
     ghcr.io/jvde-github/ais-catcher -H http://host.docker.internal:4000/api/ais/feed interval 10
   ```
3. Or run natively:
   ```bash
   AIS-catcher -H http://localhost:4000/api/ais/feed interval 10
   ```

AIS-catcher decodes VHF radio signals on 161.975 MHz and 162.025 MHz and POSTs decoded vessel data to ShadowBroker every 10 seconds. Ships detected by your SDR antenna appear alongside the global AIS stream.

**Docker (ARM/Raspberry Pi):** See [docker-shipfeeder](https://github.com/sdr-enthusiasts/docker-shipfeeder) for a production-ready Docker image optimized for ARM.

**Note:** AIS range depends on your antenna — typically 20-40 nautical miles with a basic setup, 60+ nm with a marine VHF antenna at elevation.

---

## 🔑 Environment Variables

### Backend (`backend/.env`)

```env
# Required for airplane telemetry (NEW in v0.9.7 — startup env check flags these as critical)
# Free registration: https://opensky-network.org/index.php?option=com_users&view=registration
OPENSKY_CLIENT_ID=your_opensky_client_id      # OAuth2 — global flight state vectors
OPENSKY_CLIENT_SECRET=your_opensky_secret     # OAuth2 — paired with Client ID above

# Optional (enhances data quality)
AIS_API_KEY=your_aisstream_key                # Maritime vessel tracking (aisstream.io) — ships layer empty without it
LTA_ACCOUNT_KEY=your_lta_key                  # Singapore CCTV cameras
SHODAN_API_KEY=your_shodan_key                # Shodan device search overlay
SH_CLIENT_ID=your_sentinel_hub_id             # Copernicus CDSE Sentinel Hub imagery
SH_CLIENT_SECRET=your_sentinel_hub_secret     # Paired with Sentinel Hub Client ID
MESH_SAR_EARTHDATA_USER=                      # NASA Earthdata user (SAR Mode B — OPERA products)
MESH_SAR_EARTHDATA_TOKEN=                     # NASA Earthdata token (paired with user above)
MESH_SAR_COPERNICUS_USER=                     # Copernicus Data Space user (SAR Mode B — EGMS / EMS)
MESH_SAR_COPERNICUS_TOKEN=                    # Copernicus token (paired with user above)
OPENCLAW_ACCESS_TIER=restricted               # OpenClaw agent tier: "restricted" (read-only) or "full"
GFW_API_TOKEN=your_gfw_token                  # Global Fishing Watch — fishing_activity layer (Settings → Maritime)
TELEGRAM_OSINT_ENABLED=true                   # Telegram OSINT layer (default on)
TELEGRAM_OSINT_CHANNELS=osintdefender,...     # Comma-separated public channel slugs (see .env.example)

# Private-lane privacy-core pinning (required when Arti or RNS is enabled)
PRIVACY_CORE_MIN_VERSION=0.1.0
PRIVACY_CORE_ALLOWED_SHA256=your_privacy_core_sha256
# Optional override if you load a non-default shared library path
PRIVACY_CORE_LIB=
```

When `MESH_ARTI_ENABLED=true` or `MESH_RNS_ENABLED=true`, backend startup now fails closed unless the loaded `privacy-core` artifact reports a parseable version at or above `PRIVACY_CORE_MIN_VERSION` and matches one of the hashes in `PRIVACY_CORE_ALLOWED_SHA256`.

Generate the hash from the artifact you intend to ship:

```powershell
Get-FileHash .\privacy-core\target\release\privacy_core.dll -Algorithm SHA256
```

```bash
sha256sum ./privacy-core/target/release/libprivacy_core.so
```

Then confirm authenticated `GET /api/wormhole/status` or `GET /api/settings/wormhole-status` shows the same `privacy_core.version`, `privacy_core.library_path`, and `privacy_core.library_sha256`.

### Frontend

| Variable | Where to set | Purpose |
|---|---|---|
| `BACKEND_URL` | `environment` in `docker-compose.yml`, or shell env | URL the Next.js server uses to proxy API calls to the backend. Defaults to `http://backend:8000`. **Runtime variable — no rebuild needed.** |
| `BACKEND_PORT` | repo-root `.env` or shell env before `docker compose up` | Host port used to expose the backend API for local diagnostics. Defaults to `8000`; set `BACKEND_PORT=8001` if port 8000 is already in use. Does not change Docker-internal `BACKEND_URL`. |

**How it works:** The frontend proxies all `/api/*` requests through the Next.js server to `BACKEND_URL` using Docker's internal networking. Browsers only talk to port 3000; the backend host port is only for local diagnostics. For local dev without Docker, `BACKEND_URL` defaults to `http://localhost:8000`.

---

## 🤝 Contributors

ShadowBroker is built in the open. These people shipped real code:

| Who | What | PR |
|-----|------|----|
| [@Alienmajik](https://gitlab.com/Alienmajik) | Raspberry Pi 5 support — ARM64 packaging, headless deployment notes, runtime tuning for Pi-class hardware | — |
| [@wa1id](https://github.com/wa1id) | CCTV ingestion fix — threaded SQLite, persistent DB, startup hydration, cluster clickability | #92 |
| [@AlborzNazari](https://github.com/AlborzNazari) | Spain DGT + Madrid CCTV sources, STIX 2.1 threat intel export | #91 |
| [@adust09](https://github.com/adust09) | Power plants layer, East Asia intel coverage (JSDF bases, ICAO enrichment, Taiwan news, military classification) | #71, #72, #76, #77, #87 |
| [@Xpirix](https://github.com/Xpirix) | LocateBar style and interaction improvements | #78 |
| [@imqdcr](https://github.com/imqdcr) | Ship toggle split (4 categories) + stable MMSI/callsign entity IDs | — |
| [@csysp](https://github.com/csysp) | Dismissible threat alerts + stable entity IDs for GDELT & News | #48, #63 |
| [@suranyami](https://github.com/suranyami) | Parallel multi-arch Docker builds (11min → 3min) + runtime BACKEND_URL fix | #35, #44 |
| [@chr0n1x](https://github.com/chr0n1x) | Kubernetes / Helm chart architecture for HA deployments | — |

---

## ⚠️ Disclaimer

This tool is built entirely on publicly available, open-source intelligence (OSINT) data. No classified, restricted, or non-public data is used. Carrier positions are estimates based on public reporting. The military-themed UI is purely aesthetic.

---

## 📜 License

This project is for educational and personal research purposes. See individual API provider terms of service for data usage restrictions.

---

<p align="center">
  <sub>Built with ☕ and too many API calls</sub>
</p>
