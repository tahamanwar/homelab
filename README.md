# homelab

Raspberry Pi 4 – Homelab Setup Documentation  
Author: Taha Anwar  
Document Version: v1.0  
Last Updated: 1 September 2025  

CanaKit Raspberry Pi 4 4GB Starter PRO Kit - 4GB RAM
amazon.com/dp/B07V5JTMV9?ref=ppx_yo2ov_dt_b_fed_asin_title&th=1

Project: Raspberry Pi Homelab Setup

Plan + Pre-Setup
Documented: 1 September 2025

Hardware:
CanaKit Raspberry Pi 4 (4GB Starter PRO Kit)
Includes: Pi board, heatsinks, cooling fan, case, 32GB Samsung EVO microSD card, power supply with inline switch, HDMI cable, USB microSD card reader.

OS Choice:
Raspberry Pi OS Lite (64-bit, Debian Bookworm) → chosen for:
Lightweight, terminal-based (no desktop overhead).
Ideal for sysadmin practice, Pi-hole, and forensics tools.
Easier headless use (remote via SSH).

Setup Decisions:
Flash OS using Raspberry Pi Imager (with advanced options).
Username: taha
SSH enabled (for headless remote access).
Wi-Fi preconfigured
Locale & timezone set
Boot headless (no monitor/keyboard required).
Enable unattended-upgrades for automated security updates.


Goal of Raspberry Pi 4
Documented: 1 September 2025

Primary Purpose: Create a homelab environment for hands-on learning in system administration and digital forensics.

Planned Uses:
Pi-hole deployment for DNS-based ad/malware blocking and network administration practice.
Digital forensics experiments (file system analysis, log review, forensic imaging).
General Linux sysadmin practice (service hosting, monitoring, scripting).

Career Relevance:
Builds demonstrable skills in Linux, networking, and cybersecurity.
Strengthens resume for future sysadmin and digital forensics roles.
Forms a base environment for portfolio projects and documentation.



Initial Set-up
Documented: 1 September 2025

Hardware assembly complete
Installed heatsinks, cooling fan, case, and CanaKit power supply.
Inserted 32GB Samsung EVO microSD card.

Operating system installed
Flashed Raspberry Pi OS Lite (64-bit, Debian Bookworm) via Raspberry Pi Imager.
Applied customizations:
Username: taha
SSH (Secure Shell) enabled for remote management
Wi-Fi configured (Fios-1XW5H-5G)
Locale/timezone set (US/EST)

First boot successful
Booted headless (no monitor/keyboard).
Logged in via SSH from laptop (taha@raspberrypi.local).

System fully updated & secured
Ran sudo apt update && sudo apt upgrade -y.
Enabled automated security patching (unattended-upgrades).
Confirmed latest kernel, firmware, and packages installed.

Verification checks passed
Disk space confirmed (~24GB free).
Network confirmed (active on Wi-Fi at 192.168.1.172).
Hostname: taha
Current IP: [xxx]

Outcome
Pi is now fully functional, secured, and project-ready
Baseline environment established for homelab sysadmin practice, Pi-hole deployment, and future digital forensics tools.


Security Notes
Documented: 1 September 2025

Default credentials avoided (custom username/password configured).
SSH enabled for headless access; password-based login active.
Automatic security patching enabled (unattended-upgrades).
System running on home Wi-Fi network behind router firewall.
Future improvement: switch to SSH key authentication for stronger security.






Future Projects + Roadmap
Documented: 1 September 2025

Networking / Sysadmin
Deploy Pi-hole for DNS sinkhole and network-wide ad blocking.
Configure log monitoring and system metrics (ex: htop, iftop).

Digital Forensics
Practice forensic imaging with dd and file carving tools.
Explore open-source forensic suites (Autopsy, Sleuth Kit).

Hardening / Advanced
Move from password SSH to key-based SSH authentication.
Implement firewall rules (UFW/iptables) for tighter security.
Document incident response drills using the Pi.

Useful Commands

sudo apt update && sudo apt upgrade -y
updates the pi’s package lists and upgrades any outdated software
htop
to monitor system health and resource usage
(use ‘q’ to exit the interface and return back to terminal)



Project 1: Pi-hole								    
Documented: 13 July 2026

What is Pi-hole?
Pi-hole is a Linux-based, network-wide ad blocker that acts as an internal DNS sinkhole. Instead of relying on traditional browser extensions, Pi-hole intercepts domain name requests at the network level, blocking ads, trackers, and telemetry data before they can even download onto a device.


Steps
To begin, I navigated to taha@taha.local in my terminal
Ran the command: curl -sSL https://install.pi-hole.net | bash
The Pi-hole installer downloaded its dependencies and led to a wizard inside the terminal
Went through the steps to fit my preferences and desired usage
After all steps are done, I documented my Admin Webpage login password displayed in the terminal wizard
I used my server’s private network location and accessed the website using my admin webpage login password to monitor my Pi-hole dashboard
http://xxx.xxx.x.xxx/admin
To test the environment safely without impacting other network users, I manually pointed my local Windows machine to the Pi-hole DNS server
I opened the Network Connections menu (win + r → ncpa.cpl → enter) and accessed my active Wi-Fi adapter's properties
I modified the TCP/IPv4 settings to use the Pi-hole’s static IP address as the Preferred DNS server
The Alternate DNS server was left blank to ensure Windows routed all traffic through the Pi-hole
Result: Successfully confirmed an immediate 58% query block rate on the dashboard, proving that telemetry, ad domains, and tracking trackers were actively intercepted and blocked

Deployment Testing & Engineering Decisions
Documented: July 16, 2026

Local Sandbox Validation
Before evaluating a network-wide rollout, the Pi-hole environment was isolated and tested purely on a local client workstation:
Configured the active Windows network adapter to route DNS queries exclusively to the Pi-hole static IP address (192.168.1.235)
Flushed local DNS caches (ipconfig /flushdns) to ensure immediate traffic redirection
Successfully validated ad-blocking, upstream forwarding, and real-time query logging on the Pi-hole dashboard
Network Risk Assessment & High-Availability Decision
To evaluate scaling the deployment to the entire household, a test deployment phase was done on the upstream Verizon gateway router (192.168.1.1):
The Core Risk: Router-level integration requires distributing the Pi-hole IP as the primary DNS. If the Raspberry Pi is powered down, undergoes maintenance, or fails, the entire household immediately loses internet resolution.
The Secondary DNS Limitation: Standard consumer routers do not treat a secondary DNS (such as Google’s 8.8.8.8 or Cloudflare's 1.1.1.1) as a strict second option. Instead, routers split DNS queries across both fields concurrently. Populating a secondary public DNS to prevent downtime allows up to 50% of client traffic to bypass the Pi-hole, rendering ad-blocking highly inconsistent.
Final Deployment Strategy
To guarantee 100% network uptime and reliability for household members, the following decisions were made:
Production Status: The upstream router DHCP/DNS settings were left at their default automatic configuration to ensure maximum network stability.
Local Sandbox Preservation: Ad-blocking and system administration practice remain strictly mapped to the primary workstation. This configuration allows for continuous, high-performance local testing and safe homelab experimentation without disrupting secondary network users.
