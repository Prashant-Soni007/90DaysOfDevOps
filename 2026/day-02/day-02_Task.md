# Linux Architecture, Processes, and systemd

## Core Components of Linux
- **Kernel:** The core layer that talks directly to hardware (CPU, memory, disk, network) and manages resources.
- **User Space:** Where applications, shells, and utilities run — isolated from the kernel for stability and security.
- **Init/systemd:** The first process (PID 1) started by the kernel at boot; it starts, stops, and manages all other services.

## How Processes Are Created and Managed
- Every process is created by an existing process using `fork()`, then often replaced with a new program via `exec()`.
- This creates a **parent-child** relationship, forming a process tree rooted at PID 1.
- The kernel scheduler decides which process gets CPU time and for how long.
- If a parent process dies before its child, the child is "adopted" by PID 1 (or systemd) to avoid becoming orphaned.

## Process States
- **Running (R):** Actively executing or ready to run on the CPU.
- **Sleeping (S):** Waiting for an event or resource (e.g., I/O) — most idle processes are here.
- **Uninterruptible Sleep (D):** Waiting on I/O that cannot be interrupted (e.g., disk operations).
- **Stopped (T):** Paused, usually via a signal (e.g., `Ctrl+Z`).
- **Zombie (Z):** Finished execution but still has an entry in the process table because the parent hasn't read its exit status.

## What systemd Does and Why It Matters
- systemd is the modern init system that manages the entire lifecycle of services (start, stop, restart, enable at boot).
- It starts services in parallel (faster boot) and tracks dependencies between them.
- It automatically restarts failed services, which keeps production systems self-healing.
- It centralizes logs via `journald`, making troubleshooting easier.

## 5 Commands I'd Use Daily
1. `systemctl status <service>` — check if a service is running and see recent logs.
2. `ps aux` — list all running processes with resource usage.
3. `top` / `htop` — monitor live CPU and memory usage.
4. `journalctl -u <service>` — view logs for a specific systemd service.
5. `kill -9 <pid>` — force-terminate a stuck or misbehaving process.

## Why This Matters
Understanding processes and systemd means faster debugging during incidents — knowing whether a service crashed, is stuck, or was never started, instead of guessing.