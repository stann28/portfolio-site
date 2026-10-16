# Security

## Reporting

This is a personal static site. If you find a security issue, contact me via LinkedIn: https://www.linkedin.com/in/seantanner1

## Vulnerability triage log

Images are scanned by Amazon ECR on push (basic scanning). Each finding is triaged: what it is, whether it's reachable in this deployment, whether a fix exists, and the decision.

### CVE-2026-85091: zlib heap buffer overflow (HIGH)

| Field | Detail |
|---|---|
| Found | 2026-10-15, ECR scan of `portfolio-site:latest` |
| Package | `zlib` 1.3.2-r0 (Alpine base image) |
| Severity | HIGH, CVSS 3.1 7.4 (`AV:N/AC:H/PR:N/UI:N/S:U/C:N/I:H/A:H`) |
| Known exploited | No |

**What it is.** A heap buffer overflow in zlib's internal `gz_vacate()` function, reached when `gzprintf()`/`gzvprintf()` is called after a write stall in a non-blocking `gzwrite()` operation.

**Reachability: not reachable in this deployment.**
- The bug is in zlib's `gzFile` file API (`gzwrite`/`gzprintf`), used by programs that write `.gz` files.
- NGINX uses zlib only through the streaming `deflate()` API for HTTP response compression, and never calls `gzwrite()` or `gzprintf()`.
- gzip compression is not enabled in this site's configuration; the site serves static files only.

**Fix available:** No. As of mid-September 2026, no upstream patch had been released, and distributions (e.g. Ubuntu) list the issue as unfixed/deferred.

**Decision: risk accepted, monitored.**
- Compensating controls: non-root container with all Linux capabilities dropped; no user input, uploads or server-side code; strict CSP; container reachable only from the load balancer's security group.
- Re-review: monthly, or as soon as Alpine publishes a patched zlib. Then rebuild, push, and confirm the finding clears in ECR.

References:
- https://ubuntu.com/security/CVE-2026-85091
- https://security-tracker.debian.org/tracker/CVE-2026-85091
