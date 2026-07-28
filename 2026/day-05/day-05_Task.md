# Linux Troubleshooting Drill: CPU, Memory, and Logs

**Target process:** `process_api` (PID 1, the container's init process — this sandbox has no systemd/ssh/cron, so it's the only long-running process available to inspect)

## Environment Basics
**`uname -a`**
```
Linux vm 6.18.5 #1 SMP PREEMPT_DYNAMIC @0 x86_64 x86_64 x86_64 GNU/Linux
```
**`cat /etc/os-release`**
```
PRETTY_NAME="Ubuntu 24.04.4 LTS"
```
*Observation:* Ubuntu 24.04 on a 6.18 kernel — good to confirm before assuming any OS-specific behavior.

## Filesystem Sanity
**`mkdir /tmp/runbook-demo && cp /etc/hosts /tmp/runbook-demo/hosts-copy && ls -l /tmp/runbook-demo`**
```
total 4
-rw-r--r-- 1 root root 65 Jul 28 21:25 hosts-copy
```
*Observation:* Write access confirmed — disk isn't read-only or full, so basic file operations aren't the issue here.

## Snapshot: CPU & Memory
**`ps -o pid,pcpu,pmem,comm -p 1`**
```
  PID %CPU %MEM COMMAND
    1  6.6  0.1 process_api
```
*Observation:* Low CPU (6.6%) and memory (0.1%) — process is idle/healthy, not under load.

**`free -h`**
```
               total        used        free      shared  buff/cache   available
Mem:           3.9Gi       205Mi       3.8Gi       4.2Mi        73Mi       3.7Gi
Swap:             0B          0B          0B
```
*Observation:* 3.7Gi available out of 3.9Gi total, zero swap in use — no memory pressure at all.

## Snapshot: Disk & IO
**`df -h`**
```
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda        252G  8.6G   10G  47% /
```
*Observation:* Root filesystem at 47% use — plenty of headroom, not a disk-space risk.

**`du -sh /var/log`**
```
988K    /var/log
```
*Observation:* Logs are tiny (988K) — no runaway log growth eating disk space.

## Snapshot: Network
**`curl -I https://pypi.org`** *(used instead of `ping`/`ss`, which aren't installed in this sandbox)*
```
HTTP/2 200
content-type: text/html; charset=UTF-8
server: gunicorn
```
*Observation:* Outbound HTTPS works and returns a clean 200 — external connectivity is healthy.

## Logs Reviewed
**`tail -n 5 /var/log/dpkg.log`**
```
2026-04-18 18:13:02 status installed ca-certificates-java:all 20240118
```
*Observation:* Last log entries are routine package installs from setup — no errors.

**`journalctl -u ssh -n 5`**
```
No journal files were found.
-- No entries --
```
*Observation:* No systemd journal exists in this container (no systemd as PID 1) — expected here, but on a real server this command would show recent ssh activity/errors directly.

## Quick Findings
- The process is idle and healthy: near-zero CPU/memory usage, no swap in use.
- Disk and log volume are both well within safe limits.
- Network egress works fine (200 OK on an HTTPS request).
- The only real "issue" is environmental: no systemd/journalctl in this sandbox, so service-level checks had to be substituted with process-level ones.

## If This Worsens (Next Steps)
1. **CPU/memory climbing:** Re-run `ps`/`free` on a short interval (e.g., every 30s) to see if it's a spike or a steady leak; if steady, plan a controlled restart.
2. **Disk filling up:** Check `du -sh /var/log/*` per file to find which log is growing, then rotate/truncate it before it fills the volume.
3. **No response / connection issues:** Escalate to `curl -v` for full request tracing, and if still unclear, use `strace -p <pid>` to see exactly what syscalls the process is stuck on.