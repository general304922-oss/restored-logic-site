# Pointing restoredlogic.com at GitHub Pages

Registrar: **Namecheap** (nameservers `dns1/dns2.registrar-servers.com`).

Go to **Domain List → restoredlogic.com → Manage → Advanced DNS**.

## 1. Delete what Namecheap put there by default

A new domain ships with placeholder records that will fight yours:

- `CNAME Record` · Host `www` · Value `parkingpage.namecheap.com.`
- `URL Redirect Record` · Host `@`

Remove both. Leaving them is the most common reason this setup fails.

## 2. Add four A records (the apex domain)

| Type | Host | Value | TTL |
|---|---|---|---|
| A Record | `@` | `185.199.108.153` | Automatic |
| A Record | `@` | `185.199.109.153` | Automatic |
| A Record | `@` | `185.199.110.153` | Automatic |
| A Record | `@` | `185.199.111.153` | Automatic |

All four. They are GitHub's published Pages addresses; the redundancy is the point.

## 3. Add one CNAME (the www subdomain)

| Type | Host | Value | TTL |
|---|---|---|---|
| CNAME Record | `www` | `general304922-oss.github.io.` | Automatic |

Keep the trailing dot.

## 4. Wait, then verify

Namecheap usually propagates in 30 minutes; allow a few hours. Check with:

```bash
dig +short restoredlogic.com          # expect the four 185.199.x.153 addresses
curl -sI https://restoredlogic.com | head -1
```

## 5. Turn on HTTPS

Once DNS resolves, GitHub issues a Let's Encrypt certificate automatically —
this can take up to 24 hours. Then, in the repo's **Settings → Pages**, tick
**Enforce HTTPS**.

Do not apply for the eBay developer account until `https://restoredlogic.com`
loads with a valid certificate. A domain serving a browser warning is worse
than one that does not resolve at all.

## Troubleshooting

- **404 at the domain** — the `CNAME` file in this repo must contain exactly
  `restoredlogic.com`. It does. Don't delete it; Pages rewrites it on redeploy.
- **Certificate stuck** — remove and re-add the custom domain in Settings → Pages
  to retrigger issuance.
- **www doesn't work but apex does** — the CNAME's trailing dot is missing.
