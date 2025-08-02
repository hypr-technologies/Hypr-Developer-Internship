## 🧩 1. Nginx Reverse Proxy Wildcard Configuration

Use a wildcard SSL and DNS setup to automatically route any subdomain under `*.hypr.tech` to the correct container or internal service.

```nginx
# /etc/nginx/sites-enabled/proxy-hypr.conf
server {
  listen 80;
  server_name *.hypr.tech;
  return 301 https://$host$request_uri;
}

server {
  listen 443 ssl http2;
  server_name *.hypr.tech;

  ssl_certificate     /etc/letsencrypt/live/hypr.tech/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/hypr.tech/privkey.pem;
  include             /etc/letsencrypt/options-ssl-nginx.conf;
  ssl_dhparam         /etc/letsencrypt/ssl-dhparams.pem;

  # Dynamic proxy to upstream based on subdomain
  location / {
    proxy_pass http://templates-upstream;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }
}

upstream templates-upstream {
  # Example: map launchpad.hypr.tech -> localhost:3001
  server 127.0.0.1:3001;
  # Additional backends will be mapped dynamically
}
```

✔️ This configuration uses a wildcard certificate and routes subdomains to internal services by inspecting `$host` ([LinkedIn][1], [Medium][2], [Server Fault][3], [Stack Overflow][4]).

---

## 📦 2. Cloudflare DNS Zone File Template

Use Cloudflare to manage DNS for `hypr.tech`:

```
; Zone file for hypr.tech
$ORIGIN hypr.tech.
@    3600 IN A     YOUR.SERVER.IP
*    3600 IN A     YOUR.SERVER.IP
www  3600 IN CNAME @
```

* `* hypr.tech` wildcard A record points all subdomains to your reverse proxy IP.
* Use Cloudflare's **Full DNS setup** to ensure SSL proxies correctly and DNS integrity across subdomains ([Server Fault][5]).

👉 Import via Cloudflare’s dashboard or API using their zone file import tools ([Cloudflare Docs][6]).

---

## 🧭 3. Notion Domain & Subdomain Diagram

Create a clean map in Notion or your internal wiki to visualize routing:

```
hypr.tech
├── www.hypr.tech → public homepage
├── blog.hypr.tech → content hub
├── careers.hypr.tech → job portal
└── templates.hypr.tech
    ├── launchpad.hypr.tech → Pod A templates
    ├── gridlock.hypr.tech → Pod B templates
    ├── loopline.hypr.tech → Pod C templates
    ├── fetchops.hypr.tech → Pod D templates
    └── upcycle.hypr.tech → Pod E templates

Internal / Private:
├── staging.hypr.tech → central preview area
├── dev.hypr.tech → documentation, internal tools
├── ci.hypr.tech → build/CI status UI
├── dash.hypr.tech → Grafana dashboards
└── logs.hypr.tech → internal log viewer & monitoring
```

Embed this as a page: use headings, callouts, and color-coded pods for clarity.

---

## ✅ Summary Table

| Layer            | Configuration            | Purpose                               |
| ---------------- | ------------------------ | ------------------------------------- |
| DNS              | `*.hypr.tech → A record` | Direct wildcard subdomain routing     |
| SSL              | Let’s Encrypt wildcard   | Single certificate for all subdomains |
| Reverse Proxy    | Nginx wildcard server    | Dispatch to individual services       |
| Internal Routing | Upstream mapping         | Map service names to ports/containers |

---

## 💡 Pro Tips

* **Wildcard SSL**: Use DNS‑01 validation via Certbot + Cloudflare to generate `*.hypr.tech` SSL automatically ([Cloudflare Docs][7], [LinkedIn][1], [Medium][2]).
* **Cloudflare Proxy Enabled**: Toggle the cloud icon to enable HTTP proxying for easier certificate automation and DDoS protection ([ClickFunnels Classic][8]).
* **Subdomain delegation**: Only needed if third-party teams manage specific subdomains; otherwise one global zone is sufficient ([Cloudflare Docs][9], [Server Fault][5]).

---

[1]: https://www.linkedin.com/pulse/setting-up-wildcard-subdomains-https-using-nginx-lets-nagidi-3olmc?utm_source=chatgpt.com "Setting Up Wildcard Subdomains with HTTPS Using ..."
[2]: https://medium.com/%40euricopaes/configure-nginx-with-a-wildcard-ssl-certificate-let-s-encrypt-65f951e0faa7?utm_source=chatgpt.com "Configure Nginx with a Wildcard SSL Certificate (Let´s ..."
[3]: https://serverfault.com/questions/1089759/how-to-configure-wildcard-subdomains-together-with-some-fixed-names-in-nginx?utm_source=chatgpt.com "How to configure wildcard subdomains together with some ..."
[4]: https://stackoverflow.com/questions/12950572/nginx-wildcard-proxy-pass-subdomain-to-the-server-upstream-proxy?utm_source=chatgpt.com "Nginx wildcard proxy, pass subdomain to the server ..."
[5]: https://serverfault.com/questions/934700/delegating-only-a-third-level-zone-a-b-example-to-cloudflare?utm_source=chatgpt.com "Delegating only a third level zone (abexample) to CloudFlare"
[6]: https://developers.cloudflare.com/dns/manage-dns-records/how-to/import-and-export/?utm_source=chatgpt.com "Import and export records - DNS"
[7]: https://developers.cloudflare.com/dns/zone-setups/?utm_source=chatgpt.com "DNS setups"
[8]: https://support.clickfunnels.com/support/solutions/articles/150000152836-adding-a-cloudflare-subdomain?utm_source=chatgpt.com "Adding A Cloudflare Subdomain"
[9]: https://developers.cloudflare.com/dns/zone-setups/subdomain-setup/?utm_source=chatgpt.com "Subdomain setup - DNS"
