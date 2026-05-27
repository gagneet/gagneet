# SSL Certificate Installation Documentation
## mail.eastgateresidences.com.au

**Date:** February 8, 2026
**Domain:** mail.eastgateresidences.com.au
**Server:** Ubuntu 24.04 (Noble)
**Method:** Let's Encrypt with DNS-01 Challenge (Cloudflare)

---

## Problem Statement

The domain `mail.eastgateresidences.com.au` was behind a Cloudflare tunnel with reverse nginx proxy. When attempting to obtain an SSL certificate using the standard HTTP-01 challenge method, the following error occurred:

```
Certbot failed to authenticate some domains (authenticator: nginx).
The Certificate Authority reported these problems:
  Domain: mail.eastgateresidences.com.au
  Type:   connection
  Detail: Fetching http://mail.eastgateresidences.com.au/.well-known/acme-challenge/...
          Timeout during connect (likely firewall problem)
```

**Root Cause:** Let's Encrypt couldn't reach the server directly because traffic was routed through Cloudflare tunnel, preventing HTTP-01 challenge validation.

---

## Solution Chosen

**DNS-01 Challenge with Cloudflare Plugin**

This method validates domain ownership by creating DNS TXT records, which works even when the server is behind a proxy or firewall.

**Advantages:**
- ✅ Works with Cloudflare tunnel/proxy enabled
- ✅ Supports automatic certificate renewal
- ✅ No need to expose ports or disable Cloudflare proxy
- ✅ Can generate wildcard certificates (if needed)

---

## Step-by-Step Implementation

### Step 1: Update System and Install Certbot Plugin

```bash
sudo apt update
sudo apt install -y python3-certbot-dns-cloudflare
```

**Packages Installed:**
- python3-certbot-dns-cloudflare
- python3-cloudflare
- python3-bs4
- python3-lxml
- python3-html5lib
- python3-cssselect
- python3-soupsieve
- python3-webencodings

**Verification:**
```bash
sudo certbot plugins | grep cloudflare
```

Expected output:
```
* dns-cloudflare
Description: Obtain certificates using a DNS TXT record...
```

---

### Step 2: Create Cloudflare API Token

**Instructions:**
1. Go to: https://dash.cloudflare.com/profile/api-tokens
2. Click **"Create Token"**
3. Use the **"Edit zone DNS"** template
4. Set permissions:
   - **Zone → DNS → Edit**
   - **Zone → Zone → Read**
5. Under **Zone Resources**:
   - **Include → Specific zone → eastgateresidences.com.au**
6. Click **"Continue to summary"** → **"Create Token"**
7. Copy the token (shown only once)

**Token Created:** `xdnYeYdiiXKHmEAVumptCH2EuyiBIFwBM6WRJ3NU`

---

### Step 3: Create Credentials File

```bash
# Create secrets directory
sudo mkdir -p /root/.secrets

# Create credentials file
cat > /tmp/cloudflare.ini << 'EOF'
# Cloudflare API token
dns_cloudflare_api_token = xdnYeYdiiXKHmEAVumptCH2EuyiBIFwBM6WRJ3NU
EOF

sudo mv /tmp/cloudflare.ini /root/.secrets/cloudflare.ini

# Set secure permissions (read-only for root)
sudo chmod 600 /root/.secrets/cloudflare.ini
```

**File Location:** `/root/.secrets/cloudflare.ini`
**Permissions:** `600` (owner read/write only)

---

### Step 4: Obtain SSL Certificate

```bash
sudo certbot certonly \
  --dns-cloudflare \
  --dns-cloudflare-credentials /root/.secrets/cloudflare.ini \
  -d mail.eastgateresidences.com.au \
  --non-interactive \
  --agree-tos \
  --email gagneet@example.com
```

**Output:**
```
Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/mail.eastgateresidences.com.au/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/mail.eastgateresidences.com.au/privkey.pem
This certificate expires on 2026-05-09.
Certbot has set up a scheduled task to automatically renew this certificate in the background.
```

**Certificate Files:**
- **Certificate:** `/etc/letsencrypt/live/mail.eastgateresidences.com.au/fullchain.pem`
- **Private Key:** `/etc/letsencrypt/live/mail.eastgateresidences.com.au/privkey.pem`
- **Chain:** `/etc/letsencrypt/live/mail.eastgateresidences.com.au/chain.pem`
- **Cert:** `/etc/letsencrypt/live/mail.eastgateresidences.com.au/cert.pem`

**Certificate Details:**
- **Issuer:** Let's Encrypt (E7)
- **Valid From:** February 8, 2026 06:57:18 GMT
- **Expires:** May 9, 2026 06:57:17 GMT (90 days)
- **Subject:** CN=mail.eastgateresidences.com.au
- **Algorithm:** ECDSA (id-ecPublicKey)

---

### Step 5: Configure Nginx with SSL

**Original Configuration:** `/etc/nginx/sites-available/mail.conf`

```nginx
server {
    listen 80;
    server_name mail.eastgateresidences.com.au;

    location / {
        proxy_pass http://127.0.0.1:8888;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

**Updated Configuration:**

```nginx
# HTTP server - redirect to HTTPS
server {
    listen 80;
    server_name mail.eastgateresidences.com.au;

    # Redirect all HTTP traffic to HTTPS
    return 301 https://$server_name$request_uri;
}

# HTTPS server
server {
    listen 443 ssl;
    server_name mail.eastgateresidences.com.au;

    # SSL certificate paths
    ssl_certificate /etc/letsencrypt/live/mail.eastgateresidences.com.au/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/mail.eastgateresidences.com.au/privkey.pem;

    # SSL configuration
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    location / {
        proxy_pass http://127.0.0.1:8888;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

---

### Step 6: Test and Reload Nginx

```bash
# Test nginx configuration
sudo nginx -t

# Reload nginx to apply changes
sudo systemctl reload nginx

# Verify nginx is running
sudo systemctl status nginx
```

**Test Output:**
```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

**Nginx Status:**
```
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded
     Active: active (running)
```

---

### Step 7: Verify SSL Configuration

```bash
# Check nginx is listening on ports 80 and 443
sudo ss -tlnp | grep nginx | grep -E ':80|:443'

# Test HTTPS locally
curl -Ik --resolve mail.eastgateresidences.com.au:443:127.0.0.1 https://mail.eastgateresidences.com.au

# View certificate details
sudo openssl x509 -in /etc/letsencrypt/live/mail.eastgateresidences.com.au/fullchain.pem -noout -text
```

**Verification Results:**
- ✅ Nginx listening on ports 80 and 443 (IPv4 and IPv6)
- ✅ HTTPS responding with HTTP/2 308 redirect
- ✅ Certificate valid and correctly configured
- ✅ SSL protocols: TLSv1.2, TLSv1.3

---

## Automatic Renewal

Certbot automatically sets up a systemd timer for certificate renewal.

**Check Renewal Timer:**
```bash
sudo systemctl list-timers | grep certbot
```

**Test Renewal (Dry Run):**
```bash
sudo certbot renew --dry-run
```

**Renewal Configuration:**
- Certificates are automatically renewed when they have 30 days or less remaining
- Renewal uses the same DNS-01 challenge method
- No need to disable Cloudflare proxy for renewals
- Renewal runs twice daily via systemd timer

---

## Final Configuration Summary

### Files Created/Modified

1. **Cloudflare Credentials:**
   - Path: `/root/.secrets/cloudflare.ini`
   - Permissions: `600`
   - Contains: Cloudflare API token

2. **Nginx Configuration:**
   - Path: `/etc/nginx/sites-available/mail.conf`
   - Symlink: `/etc/nginx/sites-enabled/mail.conf`
   - Changes: Added HTTPS server block and HTTP→HTTPS redirect

3. **SSL Certificates:**
   - Directory: `/etc/letsencrypt/live/mail.eastgateresidences.com.au/`
   - Auto-managed by certbot

### Ports in Use

- **Port 80 (HTTP):** Redirects to HTTPS
- **Port 443 (HTTPS):** SSL-enabled, proxies to localhost:8888
- **Port 8888:** Backend application (unchanged)

---

## Post-Installation Steps

### 1. Re-enable Cloudflare Proxy

Since you temporarily disabled the Cloudflare proxy (grey cloud) to obtain the certificate:

1. Go to Cloudflare Dashboard → DNS settings
2. Find `mail.eastgateresidences.com.au` A/AAAA record
3. Click the **grey cloud** icon to turn it **orange** (Proxied)

### 2. Test SSL Certificate

After re-enabling Cloudflare proxy, test the site:

```bash
# Test from command line
curl -I https://mail.eastgateresidences.com.au

# Or visit in browser
https://mail.eastgateresidences.com.au
```

**Expected Results:**
- ✅ Site loads over HTTPS
- ✅ Valid SSL certificate (green padlock in browser)
- ✅ HTTP automatically redirects to HTTPS
- ✅ No certificate warnings or errors

---

## Troubleshooting

### Certificate Renewal Fails

If automatic renewal fails, check:

```bash
# View certbot logs
sudo tail -50 /var/log/letsencrypt/letsencrypt.log

# Test renewal manually
sudo certbot renew --dry-run --dns-cloudflare --dns-cloudflare-credentials /root/.secrets/cloudflare.ini

# Check Cloudflare API token is still valid
# Token must have Zone:DNS:Edit and Zone:Zone:Read permissions
```

### Nginx Not Starting

```bash
# Check nginx configuration
sudo nginx -t

# View nginx error logs
sudo tail -50 /var/log/nginx/error.log

# Check certificate files exist and are readable
sudo ls -la /etc/letsencrypt/live/mail.eastgateresidences.com.au/
```

### SSL Certificate Errors in Browser

- Verify Cloudflare proxy is enabled (orange cloud)
- Check certificate hasn't expired: `sudo certbot certificates`
- Ensure nginx is using correct certificate paths
- Clear browser cache and try incognito mode

---

## Security Considerations

### API Token Security

**Permissions:** Cloudflare API token has minimal required permissions:
- Zone:DNS:Edit (for creating DNS TXT records)
- Zone:Zone:Read (for reading zone information)
- Scoped to: eastgateresidences.com.au only

**Storage:** Token stored in `/root/.secrets/cloudflare.ini` with `600` permissions (root-only access)

**Best Practices:**
- Never commit credentials file to version control
- Rotate API tokens periodically
- Monitor Cloudflare audit logs for unexpected API usage
- Consider using separate tokens for each domain/server

### SSL Configuration

**Current Settings:**
- Protocols: TLSv1.2, TLSv1.3 (modern and secure)
- Ciphers: HIGH:!aNULL:!MD5 (excludes weak ciphers)
- Server preference: Enabled

**Potential Enhancements:**
```nginx
# Add these to the HTTPS server block for enhanced security:

# HSTS (HTTP Strict Transport Security)
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

# OCSP Stapling
ssl_stapling on;
ssl_stapling_verify on;
ssl_trusted_certificate /etc/letsencrypt/live/mail.eastgateresidences.com.au/chain.pem;

# SSL Session Cache
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 10m;
```

---

## Reference Commands

### Certificate Management

```bash
# List all certificates
sudo certbot certificates

# View certificate details
sudo certbot show_account

# Renew certificates manually
sudo certbot renew

# Revoke a certificate
sudo certbot revoke --cert-path /etc/letsencrypt/live/mail.eastgateresidences.com.au/cert.pem

# Delete a certificate
sudo certbot delete --cert-name mail.eastgateresidences.com.au
```

### Nginx Management

```bash
# Test configuration
sudo nginx -t

# Reload configuration (no downtime)
sudo systemctl reload nginx

# Restart nginx (brief downtime)
sudo systemctl restart nginx

# View error logs
sudo tail -f /var/log/nginx/error.log

# View access logs
sudo tail -f /var/log/nginx/access.log
```

### SSL Testing

```bash
# Test SSL locally
openssl s_client -connect localhost:443 -servername mail.eastgateresidences.com.au

# Check certificate expiry
echo | openssl s_client -connect mail.eastgateresidences.com.au:443 2>/dev/null | openssl x509 -noout -dates

# Test SSL configuration online
# Visit: https://www.ssllabs.com/ssltest/analyze.html?d=mail.eastgateresidences.com.au
```

---

## Resources

- **Let's Encrypt Documentation:** https://letsencrypt.org/docs/
- **Certbot Documentation:** https://eff-certbot.readthedocs.io/
- **Certbot Cloudflare Plugin:** https://certbot-dns-cloudflare.readthedocs.io/
- **Nginx SSL Configuration:** https://nginx.org/en/docs/http/configuring_https_servers.html
- **Cloudflare API Docs:** https://developers.cloudflare.com/api/
- **SSL Labs Test:** https://www.ssllabs.com/ssltest/

---

## Support Information

**Server Details:**
- Hostname: (current server)
- OS: Ubuntu 24.04 LTS (Noble)
- Nginx Version: 1.24.0
- Certbot Version: Latest (from Ubuntu repository)

**Contact:**
- Cloudflare Account: (your account)
- Domain Registrar: (check Cloudflare dashboard)
- Let's Encrypt Support: https://community.letsencrypt.org/

---

## Changelog

**2026-02-08 - Initial Installation**
- Installed python3-certbot-dns-cloudflare
- Created Cloudflare API token with DNS edit permissions
- Obtained SSL certificate using DNS-01 challenge
- Configured nginx with SSL and HTTP→HTTPS redirect
- Verified SSL configuration working locally
- Documented all steps and configurations

---

## Notes

- The Cloudflare tunnel setup remains unchanged
- Backend application on port 8888 continues running as before
- DNS-01 challenge method allows certificate renewal without disabling Cloudflare proxy
- Certificate automatically renews 30 days before expiration
- No changes required to firewall rules or port forwarding

**Success Indicators:**
✅ Certificate obtained and installed
✅ Nginx configured with SSL
✅ Auto-renewal enabled
✅ HTTP→HTTPS redirect working
✅ Local SSL tests passing

**Next Action:** Re-enable Cloudflare proxy and test in browser

---

*Document generated on February 8, 2026*
