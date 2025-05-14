# Homelab Firewall + VPN Raspberry Pi Project 

**Start Date:** May 7, 2025  
**Total Time Invested:** `070000` (7 Days, 0 Hours, 0 Minutes)

## 🔹 Project Summary
Configured a Raspberry Pi to act as a secure firewall and VPN server. It uses Tailscale for remote access from anywhere in the world and WireGuard for homelab LAN traffic. SSH access is secured with hardware keys, biometric authentication, and IP-based firewall rules.

---

## 🔧 Tools & Technologies Used
- Raspberry Pi OS  
- Blink Shell (iPhone)  
- SSH (ed25519, FIDO2, and no-passphrase keys)  
- Tailscale  
- WireGuard  
- UFW (Uncomplicated Firewall)  
- Fail2Ban  
- SSH keygen & config

---

## 🧱 Steps Taken

1. Created SSH keys (no passphrase) on Mac and added to Pi.
2. Configured Blink Shell (iPhone) to use Face ID-secured SSH key.
3. Manually added public keys to `~/.ssh/authorized_keys`.
4. Verified proper SSH key access from both devices.
5. Installed and configured `fail2ban` to monitor and block brute-force SSH attempts.
6. Installed and configured `ufw` to deny incoming traffic except trusted IPs and ports.
7. Installed and configured Tailscale for remote device-level VPN access.
8. Installed and configured WireGuard for LAN-level homelab traffic.

---

## ⚠️ Challenges & Fixes

- Blink Shell doesn’t allow importing custom keys  
  → Solved by copying Blink’s public key into Pi’s `authorized_keys`  
- `ssh-copy-id` failed due to `SCRATCH_DIR` bug  
  → Solved by manually appending key to Pi  
- SSH key on Mac had passphrase  
  → Regenerated new key without passphrase  

---

## 🔒 Security Summary

- SSH passwords fully disabled (`PasswordAuthentication no`)
- Fail2Ban bans IPs after 3 failed login attempts
- UFW blocks all incoming traffic by default, only whitelisted IPs allowed
- Tailscale enables secure access from any global location
- WireGuard tunnels LAN traffic between internal systems

---
