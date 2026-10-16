# portfolio-site

Personal portfolio site for Sean Tanner — static HTML/CSS served by an unprivileged NGINX container, deployed to AWS behind HTTPS on a custom domain.

## Stack

| Layer | Choice | Why |
|---|---|---|
| Content | Static HTML + CSS, no JavaScript | Smallest attack surface; strict CSP possible |
| Web server | `nginx-unprivileged` (Alpine) | Runs as non-root on port 8080 |
| Container | Docker | Same artifact locally and in AWS |
| Hosting | _TBD: Lightsail container service or ECS Fargate_ | See "Hosting decision" |
| DNS / TLS | _TBD_ | |

## Run locally

```bash
docker build -t portfolio-site .
docker run --rm -p 8080:8080 portfolio-site
# open http://localhost:8080
```

Check the security headers:

```bash
curl -sI http://localhost:8080 | grep -iE "content-security|x-frame|x-content|referrer|permissions"
```

## Security choices

- **Non-root container** on an unprivileged port (8080).
- **No server version** disclosure (`server_tokens off`).
- **Headers:** CSP (no scripts, self-only), `X-Frame-Options: DENY`, `nosniff`, Referrer-Policy, Permissions-Policy, HSTS.
- **No secrets in the repo.** `.gitignore` blocks common key/credential files; GitHub secret scanning + push protection enabled.
- **Health check** at `/healthz` for the container platform.

## Architecture

_Diagram goes here once deployed: browser → DNS → HTTPS endpoint → container._

## Hosting decision

_Record which AWS service was chosen, the alternatives considered, and why._

## Request path

_Trace one browser request: DNS lookup → TLS handshake → load balancer / service endpoint → container → NGINX → file._

## Permissions

_IAM users/roles used, what each can do, and why that's least privilege._

## Logging

_Where access logs go (e.g. CloudWatch) and how to view them._

## Cost

_Monthly estimate from the AWS Pricing Calculator; billing alarm threshold._

## Teardown

_Exact steps to delete every resource so nothing keeps billing._

## Lessons learned

_One deployment problem hit and how it was solved._
