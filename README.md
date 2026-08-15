# cdn.iamrp.dev

This repository acts as the origin server for the `iamrp.dev` global Content Delivery Network (CDN). 

By utilizing GitHub Pages as the origin and routing traffic through a strict Cloudflare proxy, this architecture offloads all static asset delivery (images, diagrams, custom CSS) from the primary bare-metal Docker hosts. This achieves three primary engineering objectives:
1. **Zero Storage Bloat:** Application servers retain a near-zero storage footprint, storing only raw text/Markdown in local databases.
2. **Impenetrable Asset Hosting:** Static repositories served through edge nodes possess no database or backend attack surface, rendering them immune to standard web exploitation.
3. **Telemetric Sovereignty:** Retains absolute control over data hosting without sacrificing the global delivery speeds of enterprise edge networks.

## Architecture & Routing

This CDN explicitly avoids legacy "domain sharding" (e.g., splitting assets across `images.`, `css.`, `static.`). Under modern HTTP/2 and HTTP/3 protocols, multiplexing over a single TCP connection is vastly superior. All static assets flow through this single Fully Qualified Domain Name (FQDN).

**The Data Flow:**
`[Local Git Commit]` -> `[GitHub Pages Origin]` -> `[Cloudflare Edge Cache]` -> `[End User]`

* **Origin:** GitHub Pages (`main` branch -> `/root`)
* **Proxy:** Cloudflare (Orange Cloud / Full Strict SSL)
* **Edge TTL:** Forced to 1-Month via Cloudflare Page Rules to guarantee a >99% cache hit ratio and near-zero load on the origin server.

## Directory Structure

To maintain strict organization, assets must be committed to their respective operational directories:

```text
/
├── CNAME                  # Defines custom domain routing (DO NOT DELETE)
├── images/
│   ├── hardware/          # Schematics, PCB layouts, wiring diagrams
│   ├── security/          # Threat intel graphs, subversion PoCs
│   ├── blog/              # General post imagery
│   └── avatars/           # ActivityPub / Fediverse profile assets
├── css/
│   ├── terminal.css       # Core minimalist theme styling
│   └── overrides.css      # Monolithic CMS specific UI overrides
├── js/                    # (Reserved for strictly necessary client-side logic)
└── fonts/                 # Self-hosted woff2 files (no Google Fonts)
