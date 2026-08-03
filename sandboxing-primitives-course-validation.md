# Sandboxing Primitives Course Validation

**Validated:** August 3, 2026

On August 3, 2026, all 19 executable exercises passed on one Amazon Linux 2023 AArch64 host. The pass also checked 73 external links, 23 internal navigation targets, and Chrome rendering at desktop and mobile widths. It does not validate accessibility or the macOS and Windows technical claims.

## Test Environment

| Property | Value |
|---|---|
| Operating system | Amazon Linux 2023 |
| Kernel | Linux 6.12.94, AArch64 |
| Control groups | cgroup v2 |
| Privileges | Passwordless sudo available |
| Installed tools | Docker, `unshare`, iproute2, `systemd-run`, Python 3, curl |
| Extracted test tools | bubblewrap 0.10.0 and `strace` 6.12 from Amazon Linux RPMs |

## Validation Summary

| Check | Acceptance Criterion | Result |
|---|---|---|
| Executable exercises | Every command reaches its documented expected result | 19 of 19 passed |
| Manual design exercise | A learner produces and reviews a boundary diagram | Not executed; requires a learner-owned resource |
| External links | Final HTTP response is 2xx or 3xx | 73 of 73 passed |
| Internal navigation | Every fragment link resolves to one unique ID | 23 of 23 passed |
| Browser rendering | Chrome renders the page at 1440 by 1000 and 390 by 844 without overlap | Passed |

## Execution Results

| Module | Exercise | Result |
|---|---|---|
| 0 | OS surface inventory | Passed |
| 1 | Process inheritance and descriptor leaks | Passed |
| 2 | Permission and identity inspection | Passed |
| 3 | Syscalls with `strace` | Passed |
| 4 | Filesystem isolation with bubblewrap | Passed |
| 5 | PID namespace and process visibility | Passed |
| 6 | Network namespace with no egress | Passed |
| 6 | veth gateway | Passed, including cleanup |
| 7 | Seccomp profile with Docker | Passed after correcting the profile |
| 8 | Cgroup resource limits | Passed; memory workload ended with `oom-kill` |
| 9 | Container controls | Passed |
| 10 | Shell command injection compared with argv | Passed |
| 11 | Data parsing compared with code execution | Passed |
| 12 | Descriptor authority | Passed |
| 13 | Secret exposure inventory | Passed with secret values redacted |
| 14 | Brokered access design review | Not executed; requires a learner-owned resource |
| 14 | Broker-only gateway | Passed, including direct-egress denial and broker allow/deny behavior |
| 15 | Namespace observability | Passed with the module 6 namespace fixture |
| 16 | Inherited-descriptor failure mode | Passed |
| 17 | Composed sandbox | Passed, including cgroup attachment, filesystem restrictions, and no direct egress |

## Defects Corrected

1. The seccomp profile returned errno value 0 and used BusyBox `uname`, which can print invalid output while exiting successfully. The revised profile denies `mkdir` and `mkdirat` with `errnoRet: 1`.
2. The capstone ran `env -i` before `systemd-run --user`. This removed user-bus variables and produced `Failed to connect to bus: No medium found`. The revised command starts the scope before sanitizing the sandbox environment.
3. The Chromium Linux sandboxing link returned HTTP 404. The course now links to the current sandbox README.
4. The credential module repeated its bearer-versus-brokered explanation.
5. Descriptor labs left file descriptor 3 open after completion.
6. The secret-inventory lab printed credential values. It now prints variable names only.
7. The interpreter exercise lacked reproducible commands. It now compares JSON parsing with Python compilation and execution.
8. The seccomp discussion omitted `io_uring_setup`, which can expose operations that syscall-specific filters do not observe.

## Portability Limits

This pass proves the Linux commands on one Amazon Linux 2023 AArch64 host. It does not prove portability across Linux distributions, x86-64, macOS, or Windows.

Before external publication, the course maintainer must run the Linux labs on an x86-64 Ubuntu host. macOS and Windows sandbox specialists must review their platform sections.
