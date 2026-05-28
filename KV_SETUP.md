# Vercel KV Cache Setup (Upstash Redis — Free Tier)

Follow these steps once after deploying to Vercel.

---

## 1. Create a free Upstash Redis database

1. Go to https://upstash.com and sign up (free).
2. Click **Create Database**.
3. Name it anything (e.g. `stock-tracker-cache`).
4. Choose the region **closest to your Vercel deployment** (usually `ap-south-1` for India / Singapore).
5. Leave the plan as **Free**.
6. Click **Create**.

---

## 2. Copy the credentials

On the Upstash database page, under **REST API**, copy:
- `UPSTASH_REDIS_REST_URL`  → this maps to `KV_REST_API_URL`
- `UPSTASH_REDIS_REST_TOKEN` → this maps to `KV_REST_API_TOKEN`

---

## 3. Add env vars to your Vercel project

1. Open your project in the **Vercel dashboard**.
2. Go to **Settings → Environment Variables**.
3. Add two variables (all environments: Production, Preview, Development):

| Name                  | Value                          |
|-----------------------|--------------------------------|
| `KV_REST_API_URL`     | paste from Upstash             |
| `KV_REST_API_TOKEN`   | paste from Upstash             |

4. Optionally add:

| Name            | Value  | Effect                            |
|-----------------|--------|-----------------------------------|
| `YF_CACHE_TTL`  | `300`  | Cache lifetime in seconds (default 300 = 5 min) |

5. Click **Save**, then **Redeploy** your project.

---

## 4. Verify it's working

Hit the health endpoint after deployment:

```
https://your-app.vercel.app/api/health
```

You should see:
```json
{
  "cache": "connected",
  "cache_ttl": 300,
  ...
}
```

If `"cache"` says `"not configured (local cache only)"`, double-check
that the env vars are set and you've redeployed.

---

## Free tier limits (Upstash, May 2026)

| Limit              | Value        |
|--------------------|--------------|
| Commands / day     | 10,000       |
| Storage            | 256 MB       |
| Databases          | 1            |
| Max data size/key  | 1 MB         |

Each API route consumes **at most 2 commands** (1 GET + 1 SET on a cache miss).
At 300 s TTL, a tracker is re-fetched at most **288 times/day**, well within limits.

---

## How the cache works

```
Request arrives
     │
     ▼
KV GET cache_key ──► hit  ──► return JSON immediately (~50 ms)
     │
    miss
     │
     ▼
yfinance fetch (5–20 s)
     │
     ▼
KV SET cache_key  ex=300
     │
     ▼
return JSON
```

All Vercel function instances share the same KV store, so a cold start
on one instance benefits from data a warm instance cached earlier.
