# PurplePincher

**PurplePincher** (*Coenobita clypeatus*) is a Cloudflare Worker application for tracking Caribbean hermit crab care — molting cycles, shell inventory, feeding logs, habitat conditions (temperature, humidity, substrate), and behavioral observations. It deploys as a single-file Worker that serves a responsive care-tracking dashboard.

## Why It Matters

Caribbean hermit crabs live 15–30 years in captivity but most die within the first year due to improper humidity, inadequate substrate depth, poor nutrition, or toxic painted shells. The problem isn't lack of information — care guides abound — but lack of *consistent tracking*. PurplePincher solves this by logging the data that matters: daily temperature/humidity readings, molting dates and duration, feeding variety (crabs need 50+ food types over time), and shell availability. When something goes wrong, the history reveals why. This is a real application serving real pet owners, not a demo — it runs as a production Cloudflare Worker at the edge.

## How It Works

### Worker Architecture

The application is a single Cloudflare Worker (`worker.ts`) that returns a complete HTML dashboard. No backend database is required for the MVP — all state is client-side via localStorage, making the worker stateless and globally cacheable:

```
Client (browser)
  ├── localStorage: crab data, habitat readings, molt logs
  └── fetch(worker_url) → HTML dashboard (cached at edge)
```

### Data Model

The tracking model captures:

- **Crab profile**: name, species, shell size, current shell type, estimated weight, behavior notes
- **Molting log**: date, duration, surface vs. underground, exoskeleton consumption, post-molt behavior
- **Habitat readings**: temperature (°F), humidity (%), substrate depth, warm/cool side temps, water types
- **Feeding log**: food items offered vs. consumed, rotation tracking

### Critical Care Thresholds

The dashboard applies alert logic based on established care guidelines:

```
if humidity < 70% → WARN (respiration difficulty)
if humidity < 60% → CRITICAL (gill damage risk)
if temp < 72°F → WARN (lethargy, molting failure)
if temp > 85°F → CRITICAL (heat stress)
if substrate_depth < 6" → WARN (inadequate molting depth)
```

### Molting Prediction

Based on historical molting intervals, the app predicts the next molt window:

```
predicted_next = last_molt_date + mean_interval ± std_dev
```

This alerts the owner to prepare deeper substrate and isolate the crab before molting begins.

## Quick Start

```bash
# Deploy with Wrangler
npx wrangler deploy

# Local development
npx wrangler dev
```

The worker serves HTML at the root path. Visit `http://localhost:8787` in development or the deployed worker URL in production.

## API

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/` | GET | Returns the full dashboard HTML |

*Data persistence is client-side (localStorage). Server-side sync is planned.*

## Architecture Notes

PurplePincher is part of the SuperInstance ecosystem's consumer-facing applications. It demonstrates the γ + η = C principle in animal care: γ (gamma) is the proactive tracking and feeding, η (eta) is the avoidance of care mistakes (wrong humidity, painted shells, inadequate substrate). The alert thresholds represent η — they fire when conditions enter the "danger zone" that experienced crab keepers have learned to avoid. See [ARCHITECTURE.md](https://github.com/SuperInstance/SuperInstance/blob/main/ARCHITECTURE.md).

## References

1. de Vries, S. (2015). *Proper Care of Hermit Crabs*. The Crab Street Journal. — Establishes the 70%+ humidity and 72–82°F thresholds.
2. Wilde, J. E. (2002). "Land hermit crab (Coenobita) care." *Journal of Exotic Pet Medicine*, 11(3), 131–137.
3. Cloudflare. (2024). *Workers Documentation: Edge Runtime*. — Deploy model for stateless edge applications.

### Worker Deployment Model

The worker is a single file (`worker.ts`) deployed to Cloudflare's edge network. This means:
- **Zero cold starts**: Workers initialize in <5ms
- **Global distribution**: Served from 300+ edge locations
- **No server management**: Scale to millions of requests automatically
- **Free tier**: 100K requests/day at no cost

The HTML dashboard is generated programmatically (template literal in TypeScript), keeping the deployment artifact to a single file. This eliminates build steps and asset pipelines.

## License

MIT
