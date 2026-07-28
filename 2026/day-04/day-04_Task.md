# Linux Practice: Processes and Services

> Note: This practice was run on a container-based Linux environment without systemd as PID 1.
> Process and log commands below show real output. The `systemctl` commands are included with
> their exact syntax/purpose since they need a systemd-based machine (most servers/VMs) to run.

## Process Checks

**1. `ps aux | head -10`** — list running processes
```
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1 21.3  0.1  12076  4532 ?        Sl   21:21   0:00 /process_api ...
root         2  0.0  0.0      0     0 ?        S    21:21   0:00 [kthreadd]
root         3  0.0  0.0      0     0 ?        S    21:21   0:00 [pool_workqueue_release]
```
PID 1 is the container's init process; the rest are kernel worker threads.

**2. `pgrep -a process_api`** — find a process by name
```
1 /process_api --firecracker-init --addr 0.0.0.0:2024 ...
```
Confirms the main init process is running and shows its full startup command.

## Service Checks (reference syntax — requires systemd)

**3. `systemctl status ssh`** — check if the SSH service is active
```
● ssh.service - OpenBSD Secure Shell server
   Loaded: loaded (/lib/systemd/system/ssh.service; enabled)
   Active: active (running) since ...
```

**4. `systemctl list-units --type=service --state=running`** — list all currently running services
```
UNIT                LOAD   ACTIVE SUB     DESCRIPTION
ssh.service         loaded active running OpenBSD Secure Shell server
cron.service        loaded active running Regular background program processing daemon
```

## Log Checks

**5. `tail -n 5 /var/log/dpkg.log`** — view the last 5 lines of a real log file
```
2026-04-18 18:13:02 status triggers-pending ca-certificates-java:all 20240118
2026-04-18 18:13:02 startup packages triggers-only
2026-04-18 18:13:02 trigproc ca-certificates-java:all 20240118 <none>
2026-04-18 18:13:02 status half-configured ca-certificates-java:all 20240118
2026-04-18 18:13:02 status installed ca-certificates-java:all 20240118
```

**6. `journalctl -u ssh` (reference)** — view logs for the ssh service on a systemd machine
```
Jul 29 10:02:11 host sshd[1234]: Server listening on 0.0.0.0 port 22.
Jul 29 10:05:44 host sshd[1250]: Accepted publickey for user from 192.168.1.5
```

## Mini Troubleshooting Flow
1. `ps aux` — spot if the process is even running.
2. `pgrep -a <name>` — confirm it's the right process and check its startup args.
3. `systemctl status <service>` — check if it's active, failed, or restarting in a loop.
4. `journalctl -u <service> -n 50` — read the last logs to find the actual error.
5. `tail -f <app-log>` — watch live logs while reproducing the issue.

## What I Learned
Even without systemd in this environment, `ps` and `pgrep` were enough to confirm the init process was alive. On a real server, `systemctl` + `journalctl` would be the next step to check service health and pull the exact error before restarting anything blindly.