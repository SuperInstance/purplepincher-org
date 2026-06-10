# PurplePincher

> Hermit crab care tracker.

**[purplepincher.org](https://purplepincher.org)**

Caribbean hermit crabs (*Coenobita clypeatus*) live 15–30 years in captivity when cared for properly. Most die in the first year from improper humidity, bad substrate, or painted shells. PurplePincher tracks everything so yours isn't one of them.

## What It Tracks

- **Molting cycles** — Dates, duration, surface vs underground, exoskeleton consumption, post-molt behavior. Predicts next molt window
- **Shell inventory** — Available shells by size, type, opening shape. Know when to order the next size up
- **Feeding log** — What you offered, what they ate. Hermit crabs need 50+ varieties over time
- **Temperature & humidity** — Log readings, alerts if humidity drops below 70% or temp goes outside 72–82°F
- **Behavior notes** — Digging, climbing, shell-switching, aggression, lethargy. Patterns emerge when you track them
- **Care reminders** — Water changes, salt water refreshes, deep clean schedule, shell shop orders

## Tech Stack

- Cloudflare Workers (edge deployment)
- Single-file HTML response
- Custom domain via Cloudflare

## Deployment

```bash
npx wrangler deploy
```

## Part of [SuperInstance](https://superinstance.ai)
