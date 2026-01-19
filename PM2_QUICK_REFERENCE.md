# PM2 Auto-Startup - Quick Reference Guide

## Quick Status Check

```bash
# Check all services
systemctl status pm2-gagneet pm2-root

# Check all PM2 processes
pm2 list                    # Root processes (hamees)
sudo -u gagneet pm2 list    # Gagneet processes (healthapp)

# Check ports
sudo netstat -tlnp | grep -E ':(3002|3009)'

# Test URLs
curl -I https://healthapp.gagneet.com
curl -I https://hamees.gagneet.com
```

## Common Commands

### Restart Applications

```bash
# Restart healthapp
pm2 restart healthapp-nextjs
# or
sudo -u gagneet pm2 restart healthapp-nextjs

# Restart hamees
pm2 restart hamees-inventory
```

### View Logs

```bash
# Healthapp logs
pm2 logs healthapp-nextjs

# Hamees logs
pm2 logs hamees-inventory

# All logs
pm2 logs
```

### After Making Changes

```bash
# ALWAYS save after starting/stopping processes
pm2 save                    # For root processes
sudo -u gagneet pm2 save    # For gagneet processes
```

## Applications on This Server

| Application | Port | User    | PM2 Name          | URL                           |
|-------------|------|---------|-------------------|-------------------------------|
| HealthApp   | 3002 | gagneet | healthapp-nextjs  | https://healthapp.gagneet.com |
| Hamees      | 3009 | root    | hamees-inventory  | https://hamees.gagneet.com    |

## Systemd Services

| Service          | Manages          | Status Command                  |
|------------------|------------------|---------------------------------|
| pm2-gagneet      | HealthApp        | `systemctl status pm2-gagneet`  |
| pm2-root         | Hamees           | `systemctl status pm2-root`     |

## After Server Reboot

Both applications will start automatically. Verify with:

```bash
# 1. Wait 2-3 minutes after boot
# 2. Check services are running
systemctl status pm2-gagneet pm2-root

# 3. Check applications are online
pm2 list
sudo -u gagneet pm2 list

# 4. Test URLs
curl -I https://healthapp.gagneet.com
curl -I https://hamees.gagneet.com
```

## Troubleshooting

### Service not running after reboot

```bash
# Check service status
sudo systemctl status pm2-gagneet
sudo systemctl status pm2-root

# Check logs
sudo journalctl -u pm2-gagneet -n 50
sudo journalctl -u pm2-root -n 50

# Manually start
sudo systemctl start pm2-gagneet
sudo systemctl start pm2-root
```

### Process not showing in PM2

```bash
# Check saved processes
cat /home/gagneet/.pm2/dump.pm2
cat /root/.pm2/dump.pm2

# Manually resurrect
pm2 resurrect
sudo -u gagneet pm2 resurrect
```

### Application crashed

```bash
# Check PM2 status
pm2 list

# View error logs
pm2 logs <name> --err --lines 50

# Restart application
pm2 restart <name>
```

## Important Reminders

✅ **ALWAYS** run `pm2 save` after changing processes
✅ Check that services are **enabled**: `systemctl list-unit-files | grep pm2`
✅ Both services should show: `enabled`
✅ Test auto-startup after any configuration changes

## Full Documentation

- HealthApp: `/home/gagneet/healthapp-nextjs/PM2_AUTO_STARTUP.md`
- Hamees: `/home/gagneet/hamees/PM2_AUTO_STARTUP.md`

## Configuration Date

**Setup Date**: January 19, 2026
**Services**: pm2-gagneet.service, pm2-root.service
**Applications**: healthapp-nextjs (port 3002), hamees-inventory (port 3009)
