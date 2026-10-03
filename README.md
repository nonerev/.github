<div align="center">

<img src="https://nonerev.de/img/nonelogo.png" alt="Logo" width="320">

<br>

**A revival that goes for the win.**

</div>

## Infrastructure

```mermaid
flowchart TB
    players(["Players<br/>browser · client · Studio · mobile"])

    subgraph edge["Cloudflare"]
        cf["Proxy + Turnstile"]
        r2[("R2<br/>uploads · renders")]
    end

    subgraph site["Web server"]
        caddy["Caddy"]
        web["PurpRev<br/>Svelte 5 + Vite"]
        api["API<br/>Bun + Elysia"]
    end

    subgraph game["Game host"]
        rccm["RCCManager<br/>Go"]
        rcc["RCCService<br/>game servers · renders"]
    end

    db[("PostgreSQL")]

    subgraph ext["Outside"]
        discord["Discord<br/>applications"]
        roblox["Roblox<br/>asset delivery"]
    end

    players --> cf --> caddy
    caddy -->|pages| web
    caddy -->|"/api and client endpoints"| api
    players -.->|images| r2
    players ==>|"game traffic (UDP)"| rcc

    api --> db
    api --> r2
    api <--> discord
    api --> roblox
    api <-->|HTTP| rccm
    rccm --> rcc
    rccm --> r2
    rcc -->|"LAN, skips Cloudflare"| caddy

    classDef ours fill:#7b2cbf,stroke:#5a189a,color:#fff
    classDef store fill:#3c096c,stroke:#240046,color:#fff
    classDef outside fill:#e9ecef,stroke:#adb5bd,color:#212529
    class caddy,web,api,rccm,rcc ours
    class db,r2 store
    class cf,discord,roblox,players outside
```

## Services

| Service | Language | Purpose |
|---|---|---|
| **PurpRev** | TypeScript, Svelte 5 + Vite | Website frontend :3 |
| **PurpRev API** | TypeScript, Bun + Elysia | Backend for everything |
| **RCCManager** | Go | Start and stop game server, queue render. Will be open-sourced soon |
| **RCCService** | C++ | Runs game server and render avatar |

## Tech Stack

| Category | Technologies |
|---|---|
| **Languages** | TypeScript, Go, SQL |
| **Frontend** | Svelte 5, Vite, three.js for the 3D avatar |
| **Backend** | Bun, Elysia, OpenAPI docs from its plugin |
| **Database** | PostgreSQL 17 (in progress of migrating from MySQL) |
| **Storage** | Cloudflare R2 |
| **Reverse proxy** | Caddy |
| **External** | Roblox asset delivery, Discord, Cloudflare Turnstile |

## Infrastructure

| Provider | Purpose |
|---|---|
| **TBA** | TBA |
| **Cloudflare** | DNS, proxy, Turnstile, R2 for uploads and renders |
| **Caddy** | Reverse proxy and HTTPS |

### Backups

TBA

### Domain routing

| Domain | Goes to | Notes |
|---|---|---|
| `nonerev.de` | Caddy | Built frontend, and the API on `:3000` for `/api` and the client endpoints |
| `rev-cdn.mrrpmeowfurry.dev` | R2 | Uploads, thumbnails, place files |
| `rev-cdn.mrrpmeowfurry.dev/setup` | R2 | Launcher and client zips |
| WireGuard (internal) | Caddy | RCCManager and RCCService reach the API from an internal ip |
