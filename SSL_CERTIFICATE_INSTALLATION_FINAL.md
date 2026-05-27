# SSL Certificate Installation & Mail Server Setup
## mail.eastgateresidences.com.au

**Date:** February 8, 2026
**Domain:** mail.eastgateresidences.com.au
**Server:** Ubuntu 24.04 (Noble)
**Method:** Let's Encrypt with DNS-01 Challenge (Cloudflare)
**Status:** ✅ **WORKING**

---

## Mail Server Access

### Admin Interface
- **URL:** https://mail.eastgateresidences.com.au/admin
- **Username:** admin@eastgateresidences.com.au
- **Password:** AdminPass123!

### Webmail (Roundcube)
- **URL:** https://mail.eastgateresidences.com.au/webmail
- **Login:** Use any email address you create + its password

### Server Details
- **Domain:** eastgateresidences.com.au
- **Mail Hostname:** mail.eastgateresidences.com.au
- **Postmaster:** postmaster@eastgateresidences.com.au
- **Additional Domains Supported:**
  - hameesattire.com
  - skillsapien.com
  - silverfoxtechnologies.com.au

---

## Problem Statement

The domain `mail.eastgateresidences.com.au` was behind a Cloudflare tunnel with reverse nginx proxy. Multiple issues occurred:

1. **HTTP-01 Challenge Failed:**
```
Certbot failed to authenticate some domains (authenticator: nginx).
The Certificate Authority reported these problems:
  Domain: mail.eastgateresidences.com.au
  Type:   connection
  Detail: Fetching http://mail.eastgateresidences.com.au/.well-known/acme-challenge/...
          Timeout during connect (likely firewall problem)
```

2. **522 Connection Timeout:** After obtaining SSL certificate, accessing the site resulted in Cloudflare 522 errors because:
   - A DNS record pointed to unreachable public IP (163.47.70.127)
   - Server is behind ISP router with no port forwarding
   - Cloudflare couldn't reach origin server

**Root Causes:**
- Let's Encrypt couldn't reach the server directly (needed DNS-01 challenge)
- Cloudflare tried connecting to unreachable public IP instead of using tunnel
- DNS record needed to be CNAME pointing to Cloudflare tunnel

---

## Complete Solution

### Part 1: SSL Certificate (DNS-01 Challenge)
Use DNS-01 challenge with Cloudflare plugin to validate domain ownership via DNS TXT records.

### Part 2: Cloudflare Tunnel Configuration
Configure Cloudflare tunnel to route traffic to local mail server on port 8888.

### Part 3: DNS Configuration
Use CNAME record pointing to Cloudflare tunnel instead of A record to public IP.

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

### Step 5: Configure Cloudflare Tunnel

**Edit tunnel configuration:**

```bash
sudo nano /etc/cloudflared/config.yml
```

**Add this entry in the ingress section (before the catch-all rule):**

```yaml
  - hostname: mail.eastgateresidences.com.au
    service: http://localhost:8888
```

**Complete configuration:**

```yaml
tunnel: c66da87e-6ce4-4242-afea-8a8b3162b235
credentials-file: /home/gagneet/.cloudflared/c66da87e-6ce4-4242-afea-8a8b3162b235.json

ingress:
  # NEW DOMAIN - East Gate Residences
  - hostname: eastgateresidences.com.au
    service: http://localhost:80
  - hostname: www.eastgateresidences.com.au
    service: http://localhost:80
  - hostname: mail.eastgateresidences.com.au
    service: http://localhost:8888

  # OLD DOMAIN - Keep for transition period
  - hostname: eastgate.gagneet.com
    service: http://localhost:80

  # Other domains
  - hostname: healthapp.gagneet.com
    service: http://localhost:80
  - hostname: retirement.gagneet.com
    service: http://localhost:80
  - hostname: hamees.gagneet.com
    service: http://localhost:80

  # SkillSapien domain
  - hostname: skillsapien.com
    service: http://localhost:80
  - hostname: www.skillsapien.com
    service: http://localhost:80

  # Catch-all rule (important: must be last)
  - service: http_status:404
```

**Restart the tunnel:**

```bash
sudo systemctl restart cloudflared
sudo systemctl status cloudflared
```

---

### Step 6: Configure Cloudflare DNS (CRITICAL!)

This was the **key step** that made everything work!

**Go to Cloudflare Dashboard → DNS → Records**

1. **Delete the A record** for `mail.eastgateresidences.com.au` (if it exists)
   - The old A record pointed to: `163.47.70.127` (unreachable public IP)

2. **Create a CNAME record:**
   - **Type:** CNAME
   - **Name:** mail
   - **Target:** `c66da87e-6ce4-4242-afea-8a8b3162b235.cfargotunnel.com`
   - **Proxy status:** Proxied (orange cloud)
   - **TTL:** Auto

**Why this works:**
- CNAME points to the Cloudflare tunnel hostname
- Cloudflare automatically routes traffic through the tunnel
- No need for publicly accessible IP or open ports
- Traffic flows: User → Cloudflare → Tunnel → localhost:8888

**Alternative Method (via Zero Trust Dashboard):**
1. Go to: **Cloudflare Zero Trust → Networks → Tunnels**
2. Click on your tunnel → **Public Hostnames** tab
3. Add public hostname:
   - **Subdomain:** mail
   - **Domain:** eastgateresidences.com.au
   - **Service:** http://localhost:8888
4. This will automatically create/update the DNS record

---

### Step 7: Configure Nginx with SSL (Optional)

**Note:** In our final setup, nginx SSL configuration was **not needed** because:
- Cloudflare handles SSL at the edge (user → Cloudflare = HTTPS)
- Tunnel to server is encrypted by Cloudflare tunnel
- Mailu on port 8888 receives HTTP traffic from tunnel

However, if you want nginx to handle SSL locally:

**Configuration:** `/etc/nginx/sites-available/mail.conf`

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

Then test and reload:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

---

## Mail Server Usage

### Creating Email Accounts

1. **Login to Admin Interface:**
   - Go to: https://mail.eastgateresidences.com.au/admin
   - Username: admin@eastgateresidences.com.au
   - Password: AdminPass123!

2. **Create a User:**
   - Click **"User list"** in the sidebar
   - Click **"Add user"** button
   - Fill in the form:
     - **Email:** username@eastgateresidences.com.au
     - **Password:** (set a strong password)
     - **Display name:** User's full name
     - **Enable POP3:** ✓ (if needed)
     - **Enable IMAP:** ✓ (recommended)
     - **Quota:** 1GB (or as needed)
   - Click **"Submit"**

3. **User can now login:**
   - **Webmail:** https://mail.eastgateresidences.com.au/webmail
   - **Email client:** Use IMAP/SMTP settings below

### Email Client Configuration

**IMAP Settings (Receiving):**
- **Server:** mail.eastgateresidences.com.au
- **Port:** 993
- **Security:** SSL/TLS
- **Username:** full email address (e.g., user@eastgateresidences.com.au)
- **Password:** user's password

**SMTP Settings (Sending):**
- **Server:** mail.eastgateresidences.com.au
- **Port:** 587 (or 465 for SSL)
- **Security:** STARTTLS (or SSL/TLS for port 465)
- **Authentication:** Yes
- **Username:** full email address
- **Password:** user's password

### Managing Additional Domains

Your Mailu instance supports multiple domains:
- eastgateresidences.com.au (primary)
- hameesattire.com
- skillsapien.com
- silverfoxtechnologies.com.au

To create users on other domains:
1. Go to admin interface
2. Navigate to **"Mail domains"**
3. Verify the additional domains are listed
4. When creating users, select the domain from the dropdown

---

## Verification and Testing

### Test SSL Certificate

```bash
# Check certificate details
openssl s_client -connect mail.eastgateresidences.com.au:443 -servername mail.eastgateresidences.com.au < /dev/null 2>/dev/null | openssl x509 -noout -text

# Check certificate expiry
echo | openssl s_client -connect mail.eastgateresidences.com.au:443 2>/dev/null | openssl x509 -noout -dates
```

### Test DNS Resolution

```bash
# Check DNS record
dig mail.eastgateresidences.com.au

# Should show Cloudflare IPs:
# 104.21.2.132
# 172.67.129.62
```

### Test Mail Server

```bash
# Test locally
curl -I http://localhost:8888

# Test via tunnel (from server)
curl -I https://mail.eastgateresidences.com.au

# Test SMTP port
telnet mail.eastgateresidences.com.au 587

# Test IMAP port
telnet mail.eastgateresidences.com.au 993
```

### Test Cloudflare Tunnel

```bash
# Check tunnel status
sudo systemctl status cloudflared

# View tunnel logs
sudo journalctl -u cloudflared -f

# Test tunnel connectivity
sudo cloudflared tunnel info c66da87e-6ce4-4242-afea-8a8b3162b235
```

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

## Architecture Overview

### Traffic Flow

```
User Browser (HTTPS)
    ↓
Cloudflare Edge (SSL Termination)
    ↓
Cloudflare Tunnel (Encrypted)
    ↓
cloudflared service (localhost)
    ↓
Mailu on port 8888 (HTTP)
    ↓
Docker containers:
  - mailu-front-1 (nginx)
  - mailu-admin-1 (admin interface)
  - mailu-webmail-1 (Roundcube)
  - mailu-smtp-1 (Postfix)
  - mailu-imap-1 (Dovecot)
  - mailu-rspamd-1 (spam filter)
  - mailu-redis-1 (cache)
  - mailu-resolver-1 (DNS)
```

### Network Configuration

- **Public Domain:** mail.eastgateresidences.com.au
- **DNS:** CNAME → tunnel hostname (Cloudflare proxied)
- **Server LAN IP:** 192.168.4.27
- **Public IP:** 163.47.70.127 (behind router, not directly accessible)
- **Tunnel Connection:** Server → Cloudflare via outbound HTTPS
- **Mail Server:** Docker containers on port 8888

### Security Layers

1. **Cloudflare WAF:** DDoS protection, firewall rules
2. **Cloudflare Tunnel:** Encrypted connection, no open inbound ports
3. **SSL/TLS:** Let's Encrypt certificate (renewed automatically)
4. **Mailu Security:** Authentication, rate limiting, spam filtering
5. **Docker Isolation:** Containers run in isolated network

---

## Troubleshooting

### 522 Connection Timeout Error

**Symptoms:** Cloudflare shows "Connection timed out" error

**Causes:**
- DNS A record points to unreachable IP
- Cloudflare tunnel not configured correctly
- Tunnel service not running
- Wrong service port in tunnel config

**Solutions:**
1. **Check DNS record:**
   ```bash
   dig mail.eastgateresidences.com.au
   ```
   - Should be CNAME to tunnel, NOT A record to public IP

2. **Check tunnel status:**
   ```bash
   sudo systemctl status cloudflared
   sudo journalctl -u cloudflared -f
   ```

3. **Verify tunnel config:**
   ```bash
   sudo cat /etc/cloudflared/config.yml
   ```
   - Ensure mail.eastgateresidences.com.au → http://localhost:8888

4. **Test local service:**
   ```bash
   curl -I http://localhost:8888
   ```
   - Should return HTTP 200 or 308 redirect

5. **Fix: Change A record to CNAME**
   - Go to Cloudflare DNS settings
   - Delete A record for mail subdomain
   - Add CNAME pointing to tunnel hostname

### Certificate Renewal Fails

```bash
# View certbot logs
sudo tail -50 /var/log/letsencrypt/letsencrypt.log

# Test renewal manually
sudo certbot renew --dry-run

# Check Cloudflare API token is still valid
# Token must have Zone:DNS:Edit and Zone:Zone:Read permissions
```

### Tunnel Not Connecting

```bash
# Restart tunnel
sudo systemctl restart cloudflared

# Check tunnel registration
sudo cloudflared tunnel list

# Check tunnel connectivity
sudo cloudflared tunnel info c66da87e-6ce4-4242-afea-8a8b3162b235

# Verify credentials file exists
ls -la /home/gagneet/.cloudflared/*.json
```

### Mail Server Not Accessible

```bash
# Check Docker containers
sudo docker ps | grep mailu

# Check container logs
sudo docker logs mailu-admin-1
sudo docker logs mailu-front-1

# Restart mail containers
cd /mailu
sudo docker-compose restart

# Test port 8888
curl -v http://localhost:8888
```

### Can't Login to Admin

```bash
# Reset admin password
sudo docker exec mailu-admin-1 flask mailu admin admin eastgateresidences.com.au 'NewPassword123!' -m update

# Check admin user exists
sudo docker exec mailu-admin-1 flask mailu user-list eastgateresidences.com.au
```

### Email Not Sending/Receiving

```bash
# Check SMTP logs
sudo docker logs mailu-smtp-1 | tail -50

# Check IMAP logs
sudo docker logs mailu-imap-1 | tail -50

# Check spam filter
sudo docker logs mailu-rspamd-1 | tail -50

# Test SMTP port
telnet mail.eastgateresidences.com.au 587

# Check DNS MX records
dig MX eastgateresidences.com.au
```

---

## Important Files and Locations

### SSL Certificates
- `/etc/letsencrypt/live/mail.eastgateresidences.com.au/fullchain.pem`
- `/etc/letsencrypt/live/mail.eastgateresidences.com.au/privkey.pem`
- `/etc/letsencrypt/live/mail.eastgateresidences.com.au/chain.pem`
- `/etc/letsencrypt/live/mail.eastgateresidences.com.au/cert.pem`

### Cloudflare Configuration
- `/etc/cloudflared/config.yml` - Tunnel configuration
- `/home/gagneet/.cloudflared/c66da87e-6ce4-4242-afea-8a8b3162b235.json` - Tunnel credentials
- `/root/.secrets/cloudflare.ini` - Certbot Cloudflare API token

### Mail Server Configuration
- `/mailu/mailu.env` - Mailu environment variables
- `/mailu/docker-compose.yml` - Docker container configuration
- `/mailu/data/` - Mail data directory
- `/mailu/config/` - Mailu configuration files

### Nginx Configuration (if used)
- `/etc/nginx/sites-available/mail.conf` - nginx virtual host
- `/etc/nginx/sites-enabled/mail.conf` - symlink to above

### Logs
- `/var/log/letsencrypt/letsencrypt.log` - Certbot logs
- `/var/log/nginx/error.log` - Nginx error logs
- `/var/log/nginx/access.log` - Nginx access logs
- `sudo journalctl -u cloudflared` - Cloudflare tunnel logs
- `sudo docker logs mailu-admin-1` - Mailu admin logs

---

## Security Best Practices

### API Token Security

**Cloudflare API Token:**
- Stored in `/root/.secrets/cloudflare.ini` with `600` permissions
- Has minimal required permissions (Zone:DNS:Edit, Zone:Zone:Read)
- Scoped to single zone (eastgateresidences.com.au)
- **Action:** Rotate token every 6 months

**Recommendations:**
- Never commit credentials to version control
- Monitor Cloudflare audit logs for unexpected API usage
- Use separate tokens for each server/domain

### Mail Server Security

**Change Default Password:**
```bash
# Change admin password from default
sudo docker exec mailu-admin-1 flask mailu admin admin eastgateresidences.com.au 'YOUR-SECURE-PASSWORD-HERE' -m update
```

**Enable 2FA:**
1. Login to admin interface
2. Go to Settings → Security
3. Enable two-factor authentication

**Configure SPF/DKIM/DMARC:**
1. Go to admin interface
2. Navigate to **Mail domains → eastgateresidences.com.au**
3. Copy the DNS records shown
4. Add them to Cloudflare DNS:
   - SPF: TXT record for domain
   - DKIM: TXT record for selector._domainkey
   - DMARC: TXT record for _dmarc

**Rate Limiting:**
- Already configured in `/mailu/mailu.env`: `AUTH_RATELIMIT=10/minute`
- Adjust if needed for your use case

### Firewall Configuration

**Required Open Ports:**
- Port 25 (SMTP) - For receiving email from other servers
- Port 587 (SMTP submission) - For sending email (authenticated)
- Port 465 (SMTPS) - For sending email (SSL, authenticated)
- Port 993 (IMAPS) - For reading email (SSL)

**No Inbound Ports Needed for Web Interface:**
- All HTTPS traffic goes through Cloudflare tunnel
- No need to open ports 80/443 on router

### SSL/TLS Configuration

**Current Settings (if using nginx):**
- Protocols: TLSv1.2, TLSv1.3
- Ciphers: HIGH:!aNULL:!MD5
- Server preference: Enabled

**Enhanced Security (optional):**

Add to nginx HTTPS server block:

```nginx
# HSTS (HTTP Strict Transport Security)
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

# OCSP Stapling
ssl_stapling on;
ssl_stapling_verify on;
ssl_trusted_certificate /etc/letsencrypt/live/mail.eastgateresidences.com.au/chain.pem;

# SSL Session Cache
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 10m;

# Additional security headers
add_header X-Frame-Options "SAMEORIGIN" always;
add_header X-Content-Type-Options "nosniff" always;
add_header X-XSS-Protection "1; mode=block" always;
```

---

## Maintenance Tasks

### Weekly
- Check mail server logs for errors or spam
- Monitor disk space usage
- Review failed login attempts

### Monthly
- Update Docker containers:
  ```bash
  cd /mailu
  docker-compose pull
  docker-compose up -d
  ```
- Review and clean up old emails if needed
- Check SSL certificate renewal status

### Quarterly
- Review and update firewall rules
- Audit user accounts (remove inactive users)
- Update server packages:
  ```bash
  sudo apt update && sudo apt upgrade
  ```
- Rotate Cloudflare API token

### Annually
- Review backup and disaster recovery procedures
- Update documentation with any changes
- Review security settings and best practices

---

## Backup and Recovery

### Backup Mail Data

```bash
# Backup mail data directory
sudo tar -czf /backup/mailu-data-$(date +%Y%m%d).tar.gz /mailu/data/

# Backup configuration
sudo tar -czf /backup/mailu-config-$(date +%Y%m%d).tar.gz /mailu/mailu.env /mailu/docker-compose.yml /etc/cloudflared/
```

### Restore Mail Data

```bash
# Stop containers
cd /mailu
sudo docker-compose down

# Restore data
sudo tar -xzf /backup/mailu-data-YYYYMMDD.tar.gz -C /

# Start containers
sudo docker-compose up -d
```

### Backup SSL Certificates

```bash
# Backup certificates
sudo tar -czf /backup/letsencrypt-$(date +%Y%m%d).tar.gz /etc/letsencrypt/
```

**Note:** Certificates can be easily renewed, so backups are optional.

---

## Resources and Documentation

### Official Documentation
- **Let's Encrypt:** https://letsencrypt.org/docs/
- **Certbot:** https://eff-certbot.readthedocs.io/
- **Certbot Cloudflare Plugin:** https://certbot-dns-cloudflare.readthedocs.io/
- **Cloudflare Tunnels:** https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/
- **Mailu:** https://mailu.io/2.0/
- **Nginx SSL:** https://nginx.org/en/docs/http/configuring_https_servers.html

### Testing Tools
- **SSL Labs:** https://www.ssllabs.com/ssltest/
- **MX Toolbox:** https://mxtoolbox.com/
- **Mail Tester:** https://www.mail-tester.com/

### Support
- **Cloudflare Community:** https://community.cloudflare.com/
- **Let's Encrypt Community:** https://community.letsencrypt.org/
- **Mailu Community:** https://mailu.io/master/community.html

---

## Changelog

**2026-02-08 - Complete Implementation**
- ✅ Installed python3-certbot-dns-cloudflare
- ✅ Created Cloudflare API token with DNS edit permissions
- ✅ Obtained SSL certificate using DNS-01 challenge
- ✅ Configured Cloudflare tunnel with mail subdomain route
- ✅ Changed DNS from A record to CNAME pointing to tunnel
- ✅ Verified SSL configuration working
- ✅ Created mail server admin account
- ✅ Documented complete solution with credentials
- ✅ Tested and confirmed working: https://mail.eastgateresidences.com.au

---

## Summary

**What Was Done:**
1. ✅ Installed certbot with Cloudflare DNS plugin
2. ✅ Created Cloudflare API token for DNS validation
3. ✅ Obtained Let's Encrypt SSL certificate via DNS-01 challenge
4. ✅ Configured Cloudflare tunnel to route traffic to port 8888
5. ✅ Changed DNS from A record to CNAME (critical fix!)
6. ✅ Created admin account for mail server
7. ✅ Verified everything is working

**Key Success Factors:**
- DNS-01 challenge worked despite server being behind firewall
- Cloudflare tunnel enabled access without port forwarding
- CNAME record (not A record) was essential for tunnel routing
- Direct routing to port 8888 avoided unnecessary nginx complexity

**Current Status:**
- 🟢 **Mail Server:** https://mail.eastgateresidences.com.au - ✅ WORKING
- 🟢 **Admin Panel:** https://mail.eastgateresidences.com.au/admin - ✅ WORKING
- 🟢 **Webmail:** https://mail.eastgateresidences.com.au/webmail - ✅ WORKING
- 🟢 **SSL Certificate:** Valid until May 9, 2026 - ✅ AUTO-RENEWAL ENABLED
- 🟢 **Cloudflare Tunnel:** 4 connections active - ✅ HEALTHY

**Next Steps:**
1. Change admin password from default
2. Create email accounts for users
3. Configure SPF/DKIM/DMARC records
4. Set up regular backups
5. Configure email clients (IMAP/SMTP)

---

*Documentation generated and updated on February 8, 2026*
*Status: COMPLETE AND WORKING ✅*
