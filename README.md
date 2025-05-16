# Homelab Firewall + VPN Raspberry Pi Project

**Start Date:** May 7, 2025  
**Total Time Invested:** `070000` (7 Days, 0 Hours, 0 Minutes)

---

## 🔹 Project Summary

Configured a Raspberry Pi into a zero-trust **homelab security hub**, combining a strict firewall, secure VPN gateways, and advanced intrusion prevention. This system uses **Tailscale** for secure remote access and **WireGuard** for fast internal LAN traffic. **SSH access is hardened** with hardware keys, biometric protection, and IP-based firewall rules. A custom Fail2Ban + msmtp integration sends instant email alerts when IPs are banned.

---

## 🔧 Tools & Technologies Used

- Raspberry Pi OS (Debian 11)
- Blink Shell (iPhone w/ Face ID)
- SSH (ed25519 + FIDO2 hardware keys)
- Tailscale (Zero-config VPN)
- WireGuard (LAN mesh VPN)
- UFW (Uncomplicated Firewall)
- Fail2Ban (Brute-force protection)
- msmtp (Lightweight mail client)
- Lynis (Security audit scanner)
- Nmap (Port scanner)
- Sysctl hardening

---

## 🧱 Steps Taken

1. **Generated secure ed25519 + hardware SSH keys** on Mac and iPhone.
2. **Configured Blink Shell on iPhone** to use Face ID-protected key.
3. **Disabled SSH passwords completely** (`PasswordAuthentication no`).
4. Installed and configured `fail2ban` to ban IPs after repeated SSH or WireGuard intrusion attempts.
5. Set up `ufw` to **block all ports** except for SSH (5****, Tailscale, and WireGuard.
6. Installed Tailscale for **remote device-level VPN** access from anywhere.
7. Configured WireGuard to route **internal LAN traffic securely** between homelab nodes.
8. Hardened system using custom `sysctl.conf` and UFW kernel tweaks.
9. **Installed msmtp** and integrated it with **Fail2Ban** to send **real-time email alerts** on IP bans.
10. Ran a full **Lynis security audit** and patched high-priority items.
11. Verified external visibility using `nmap` and confirmed port stealth.

---

## ⚔️ Simulated WireGuard Attacks

To test the system’s resilience, a custom script (`simulate_wireguard_attack.sh`) was built to simulate repeated unauthorized WireGuard handshakes — mimicking brute-force VPN attack patterns.

### Purpose:
- Test whether **Fail2Ban** could accurately detect and block WireGuard intrusion attempts.
- Ensure an **email alert is only triggered after the 3rd failed handshake**.
- Confirm the system doesn't overreact (false positives) or underreact (missed bans).

### Key Challenges:
| Issue | Fix |
|-------|-----|
| Fail2Ban wasn’t detecting attacks | Added custom `wireguard` filter targeting log pattern: `Invalid handshake from <IP>` |
| Each simulation used a new IP | Set a static attacker IP (`203.0.113.250`) to simulate multiple attempts |
| Bans triggered after just 1 try | Tuned `maxretry = 3` and `findtime` to simulate time-based attacks properly |
| Email alerts didn’t send | Fixed broken `<destemail>` in Fail2Ban action script by hardcoding address |
| Emails silently failed | Created `/etc/fail2ban/send-msmtp.sh` wrapper to **log every mail event** |
| Emails still failed as root | Resolved by copying `.msmtprc` into `/root/` and using `sudo` in action script |
| Fail2Ban socket crash | Fixed by removing stale socket `/var/run/fail2ban/fail2ban.sock` manually |

### Final Result:
- IP is banned **only after 3 failed handshakes**.
- Email alert is sent with log and timestamp.
- Custom logs (`/tmp/f2b-email-call.log`) record all email attempts and errors.
- All tests passed — system now mimics a **mini VPN intrusion detection and response engine**.

---

## ✉️ Custom Email Alert System (Fail2Ban + msmtp)

Custom integration was built to send alerts via Gmail when an IP is banned. Includes:

- `send-msmtp.sh`: Custom mail wrapper with logging
- `/etc/fail2ban/action.d/multi-msmtp-wireguard.conf`: Custom Fail2Ban action for WireGuard jail
- `/tmp/f2b-email-call.log`: Debug log for every email attempt

> This turns the Pi into a **basic intrusion detection system (IDS)** with email alerting.

---

## 🔒 Security Summary

| Area | Hardening |
|------|-----------|
| **SSH** | Password login disabled, hardware key-only, Face ID-secured access |
| **Firewall** | Default deny-all, only key ports (WireGuard/Tailscale/SSH) allowed |
| **Fail2Ban** | Auto-ban after 3 failed attempts with real-time email alerts |
| **VPN** | Tailscale for remote access, WireGuard for LAN mesh tunneling |
| **Logging** | Email alerts + persistent logs of bans & email attempts |
| **Hardening** | Kernel & network hardening via `sysctl`, UFW tuning, and Lynis |

---

## ✅ Security Audit (Lynis)

- **Hardening Index:** 64/100
- ✅ No rootkits or major flaws detected
- Suggestions provided for:
  - SSH hardening (TCP forwarding, session limits)
  - Kernel sysctl tweaks
  - File integrity monitoring (rkhunter, AIDE)
  - AppArmor / auditd (optional)
  - Custom legal login banners (optional)

---

## 📌 Final Thoughts

This project turned a $35 Raspberry Pi into a **miniature firewall, VPN server, and intrusion detection system**. Every access attempt is tightly controlled and monitored. Email alerts notify you of suspicious activity instantly — all built without a GUI or expensive software.

---

## ▶️ Next Steps

- Add `rkhunter` or `chkrootkit` for malware scans
- Install `auditd` for full audit logging
- Configure AppArmor or SELinux for MAC (Mandatory Access Control)
- Deploy the same stack to other edge nodes
- Back up `.msmtprc`, `ufw`, and `fail2ban` configs
- Create automatic script backups of `/etc`

---