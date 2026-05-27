# Mail Server Quick Reference
## mail.eastgateresidences.com.au

**Status:** ✅ WORKING | **Updated:** February 8, 2026

---

## 🔐 Admin Access

### Admin Panel
```
URL: https://mail.eastgateresidences.com.au/admin
Username: admin@eastgateresidences.com.au
Password: AdminPass123!
```

### Webmail (Roundcube)
```
URL: https://mail.eastgateresidences.com.au/webmail
Login: Use any email address you create + password
```

---

## 📧 Email Client Settings

### IMAP (Receiving Mail)
```
Server: mail.eastgateresidences.com.au
Port: 993
Security: SSL/TLS
Username: full@email.address
Password: user's password
```

### SMTP (Sending Mail)
```
Server: mail.eastgateresidences.com.au
Port: 587 (STARTTLS) or 465 (SSL)
Security: STARTTLS or SSL/TLS
Authentication: Yes
Username: full@email.address
Password: user's password
```

---

## 🎯 Quick Actions

### Create New Email Account
1. Login to admin panel
2. Click **"User list"** → **"Add user"**
3. Fill in: email, password, display name
4. Set quota (1GB default)
5. Enable IMAP/POP3 as needed

### Reset Admin Password
```bash
sudo docker exec mailu-admin-1 flask mailu admin admin eastgateresidences.com.au 'NEW_PASSWORD' -m update
```

### Check Mail Server Status
```bash
# Check containers
sudo docker ps | grep mailu

# Check tunnel
sudo systemctl status cloudflared

# Check SSL certificate
echo | openssl s_client -connect mail.eastgateresidences.com.au:443 2>/dev/null | openssl x509 -noout -dates
```

### View Logs
```bash
# Cloudflare tunnel
sudo journalctl -u cloudflared -f

# Mail containers
sudo docker logs -f mailu-admin-1
sudo docker logs -f mailu-smtp-1
sudo docker logs -f mailu-imap-1
```

### Restart Services
```bash
# Restart tunnel
sudo systemctl restart cloudflared

# Restart mail server
cd /mailu && sudo docker-compose restart
```

---

## 🌐 Supported Domains

- **Primary:** eastgateresidences.com.au
- **Additional:**
  - hameesattire.com
  - skillsapien.com
  - silverfoxtechnologies.com.au

---

## 📋 SSL Certificate

```
Certificate: /etc/letsencrypt/live/mail.eastgateresidences.com.au/fullchain.pem
Private Key: /etc/letsencrypt/live/mail.eastgateresidences.com.au/privkey.pem
Expires: May 9, 2026
Auto-Renewal: ✅ Enabled (runs twice daily)
```

### Force Renewal Test
```bash
sudo certbot renew --dry-run
```

---

## 🔧 Troubleshooting

### 522 Error
**Problem:** Cloudflare can't reach server
**Check:**
1. DNS record is CNAME (not A record)
2. Cloudflare tunnel is running
3. Port 8888 is accessible: `curl http://localhost:8888`

### Can't Login
**Solution:** Reset password
```bash
sudo docker exec mailu-admin-1 flask mailu admin admin eastgateresidences.com.au 'NewPassword' -m update
```

### Email Not Sending
**Check SMTP logs:**
```bash
sudo docker logs mailu-smtp-1 | tail -50
```

### Tunnel Not Working
**Restart tunnel:**
```bash
sudo systemctl restart cloudflared
sudo journalctl -u cloudflared -f
```

---

## 📞 Important Files

- **Mailu Config:** `/mailu/mailu.env`
- **Tunnel Config:** `/etc/cloudflared/config.yml`
- **Cloudflare API Token:** `/root/.secrets/cloudflare.ini`
- **SSL Certificates:** `/etc/letsencrypt/live/mail.eastgateresidences.com.au/`

---

## 📚 Full Documentation

Complete setup guide: `/home/gagneet/gagneet/SSL_CERTIFICATE_INSTALLATION_FINAL.md`

---

**IMPORTANT:** Change the default admin password after first login!
```bash
sudo docker exec mailu-admin-1 flask mailu admin admin eastgateresidences.com.au 'YOUR-SECURE-PASSWORD' -m update
```
