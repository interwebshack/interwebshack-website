# Deployment details — Cloudflare R2 + Origin Rules

This environment serves **interwebshack.com** and **www.interwebshack.com** from a public **R2** bucket via a **Custom Domain**.

## Flow

1) **R2 bucket** hosts objects.  
2) **Custom Domain** for R2 exposes the bucket over Cloudflare’s CDN. (This is how public access is enabled.) :contentReference[oaicite:14]{index=14}  
3) **DNS** has proxied records for apex and `www`, so Cloudflare receives traffic. :contentReference[oaicite:15]{index=15}  
4) **Origin Rules** (Rulesets API) rewrite the **origin** and **Host** header to the R2 custom domain for both apex and `www`. :contentReference[oaicite:16]{index=16}  
5) **Uploads** happen via R2’s **S3-compatible** endpoint: `https://<ACCOUNT_ID>.r2.cloudflarestorage.com`. :contentReference[oaicite:17]{index=17}

## Why Origin Rules (instead of CNAME to the bucket)

- A simple URL rewrite cannot change the **hostname**; Origin Rules can. :contentReference[oaicite:18]{index=18}  
- The recommended pattern for pointing a hostname to an R2 custom domain is via an **Origin Rule**, optionally with a path rewrite if needed. :contentReference[oaicite:19]{index=19}

## Validation performed in CI

- **DNS**: `dig interwebshack.com` and `dig www.interwebshack.com`.  
- **HEAD 200**: `https://interwebshack.com/index.html`.  
- **Integrity**: SHA-256 of local `index.html` equals remote body.  
- **Cache-Control** header present (HTML short cache; assets long cache).

## Caching policy

- HTML: `Cache-Control: public, max-age=300` (tune as you like).  
- Assets (CSS/JS/images): uploaded via `aws s3 sync` with long cache by default; for immutable assets, embed a hash in filenames and set `max-age=31536000, immutable`.

## CORS (optional)

If your site needs cross-origin requests, configure **Bucket → Settings → CORS** in R2. :contentReference[oaicite:20]{index=20}

## Troubleshooting

- **403/404 via apex**: confirm the **Origin Rule** exists in the `http_request_origin` phase and that DNS records are **proxied**. :contentReference[oaicite:21]{index=21}  
- **Uploads fail**: verify you’re using the **S3 endpoint** and R2 Access Key Pair (not the API token). :contentReference[oaicite:22]{index=22}  
- **Public access**: ensure Custom Domain is attached and **Active** (R2 → Bucket → Settings → Public access). :contentReference[oaicite:23]{index=23}
