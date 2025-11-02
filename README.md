# interwebshack.com → Cloudflare R2 (Fully Automated)

This repo deploys **interwebshack.com** (and **www.interwebshack.com**) from a **Cloudflare R2** bucket via GitHub Actions.  
We serve the site over Cloudflare’s CDN using an **R2 Custom Domain** and **Origin Rules** to route apex + www.

## What gets automated

- R2 **bucket** creation (idempotent) :contentReference[oaicite:7]{index=7}
- R2 **Custom Domain** attachment (public access via Cloudflare CDN) :contentReference[oaicite:8]{index=8}
- **DNS**: proxied apex + `www` records (so Cloudflare handles traffic) :contentReference[oaicite:9]{index=9}
- **Origin Rules**: route `interwebshack.com` and `www.interwebshack.com` to the R2 custom domain and set `Host` header accordingly (API via **Rulesets**) :contentReference[oaicite:10]{index=10}
- **Upload** to R2 using S3-compatible API (`aws s3 sync`) :contentReference[oaicite:11]{index=11}
- **Validation**: DNS resolves, 200 on `/index.html`, SHA-256 match, cache headers

## Repo layout

```bash
site/ # built static files (must include index.html)
.github/workflows/deploy-r2.yml
docs/DEPLOYMENT-R2.md
```

## One-time setup

1. **Cloudflare zone**: Add `interwebshack.com` in Cloudflare and update nameservers (once).  
2. **Create credentials**  
   - **R2 Access Key Pair** (for S3 uploads) + **S3 endpoint**: `https://<ACCOUNT_ID>.r2.cloudflarestorage.com` :contentReference[oaicite:12]{index=12}  
   - **API Token** with permissions:  
     - *Workers R2 Storage: Write* (create bucket, attach custom domain)  
     - *DNS: Write* (create records)  
     - *Zone Transform/Origin Rules: Write* (Rulesets API) :contentReference[oaicite:13]{index=13}

3. **GitHub → Settings → Secrets and variables → Actions**  
   - `CLOUDFLARE_ACCOUNT_ID`  
   - `CLOUDFLARE_API_TOKEN`  
   - `R2_ACCESS_KEY_ID`  
   - `R2_SECRET_ACCESS_KEY`  
   - `R2_BUCKET` (e.g., `site-interwebshack-com`)  
   - `R2_CUSTOM_DOMAIN` (e.g., `static.interwebshack.com`)

4. **Commit** your site to `./site` and push to `main`. The workflow will provision, upload, and validate.

See **docs/DEPLOYMENT-R2.md** for deeper details and troubleshooting.
