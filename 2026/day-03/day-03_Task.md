# Linux Commands Cheat Sheet

## Process Management
1. `ps aux` — list all running processes with resource usage.
2. `top` — view live CPU/memory usage per process.
3. `htop` — interactive, easier-to-read version of `top`.
4. `kill -9 <pid>` — force-terminate a process.
5. `systemctl status <service>` — check status of a systemd service.
6. `systemctl restart <service>` — restart a service.
7. `journalctl -u <service>` — view logs for a specific service.
8. `nice -n 10 <command>` — start a process with lower priority.

## File System
9. `ls -lh` — list files with human-readable sizes and permissions.
10. `df -h` — check disk space usage per mounted volume.
11. `du -sh <dir>` — check size of a specific directory.
12. `find / -name "<file>"` — search for a file by name.
13. `chmod 755 <file>` — change file permissions.
14. `chown user:group <file>` — change file ownership.
15. `tail -f <file>` — live-follow a log file for new entries.
16. `grep -i "<text>" <file>` — search for text in a file, case-insensitive.
17. `df -i` — check inode usage (useful when disk shows space but writes fail).

## Networking Troubleshooting
18. `ping <host>` — check basic network connectivity to a host.
19. `ip addr` — view network interfaces and assigned IP addresses.
20. `curl -I <url>` — check HTTP response headers/status from a service.
21. `dig <domain>` — check DNS resolution for a domain.
22. `netstat -tulnp` — list open ports and the processes using them.
23. `traceroute <host>` — trace the network path to a host.

## Why This Matters
These commands are the daily toolkit for spotting stuck processes, full disks, and connectivity issues fast — before they turn into longer outages.