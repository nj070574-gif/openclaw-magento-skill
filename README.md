# magento-admin — OpenClaw Skill

The definitive Magento 2 administration skill for OpenClaw agents. Connects
your AI agent directly to your own Magento 2 store via SSH key auth, REST API,
GraphQL, and direct database access.

---

## ⚠️ About the Security Scanner Rating

Security scanners (including VirusTotal and the OpenClaw built-in scanner) rate
this skill as **high-risk**. This is **expected and correct** — it is not a
sign of malware or malicious intent.

Here is what the scanner is actually telling you:

> *"The skill's capabilities are aligned with the stated purpose... the broad
> scope of authority granted to the agent over production infrastructure is
> inherently high-risk."*

That is an accurate description of what a full store administration tool does.
Any software that can restart services, modify a database, and manage SSH access
to a server will receive a high-risk rating from security scanners — the same
way a server management panel, a deployment tool, or a database GUI would.

**The scanner is not saying this skill is malicious.** It is saying that you
should understand what you are installing before you install it. That is good
advice, and this README exists to make sure you do.

### What the scanner checks and what it found

| Check | Result | Notes |
|---|---|---|
| Purpose matches capabilities | ✅ Pass | SSH + DB + REST is proportionate to full Magento admin |
| Install mechanism | ✅ Pass | Instruction-only, no code downloaded, no install scripts |
| Credential handling | ✅ Pass | All credentials user-supplied, nothing hardcoded |
| Persistence | ✅ Pass | Not always-enabled, no system-wide changes |
| Passwords on command lines | ✅ Pass | DB password via MYSQL_PWD env var, REST via HTTPS body |
| TLS verification | ✅ Pass | `--cacert` used, not `-k` |
| SSH credential method | ✅ Pass | SSH key auth only, no sshpass |
| Inherent privilege scope | ⚠️ High-risk (expected) | Full admin = high privilege by design |

The ⚠️ is not a problem to fix — it is the scanner correctly identifying that
this tool is powerful. **All technical security checks pass.**

---

## What This Skill Does

Gives an OpenClaw AI agent the ability to fully administer a Magento 2 store.
You talk to your agent in natural language; the agent runs the appropriate
commands on your server and reports back.

### Store Operations
- Health check — full system status in one command
- Cache — flush, clean, enable/disable any type
- Indexing — reindex all or specific indexers, fix broken search
- Cron — run, monitor, clear backlog, fix stuck jobs
- Maintenance mode — enable/disable

### Orders and Revenue
- Order management — view, cancel, invoice, ship, refund via REST API
- Revenue reports — today, week, month, 30-day breakdown
- Abandoned carts — identify today's active abandoned carts
- Last N orders — with customer, total, and status

### Products and Inventory
- Stock updates — qty and in-stock status via REST API
- Price changes — update via REST API
- Out-of-stock report — list all depleted products
- Product count by type

### Customers
- Lookup by email with full order history
- Lifetime value calculation
- Top 10 customers by revenue
- Password reset via REST API

### Configuration and Extensions
- Read/write any Magento config path
- Enable/disable modules
- Install/remove extensions via Composer
- Full deployment pipeline: upgrade → compile → static deploy → cache flush

### Security
- 2FA status and reset per admin user
- Admin user list, create, unlock
- Failed login monitoring

### Infrastructure
- Database queries, table repair, optimise, size analysis
- Log files — exception, system, cron
- OpenSearch — health, indices, rebuild
- Redis — status, flush by database
- GraphQL — store config, product search, categories
- Backup and restore — DB dump/restore with maintenance mode

### Email and Marketing
- SMTP config check and test send
- Cart and catalog price rules
- Coupon usage stats and generation via REST API

---

## Security Model

This skill uses the following security patterns. Each was chosen to avoid
common scanner flags while remaining fully functional.

**Authentication:**
- SSH key authentication only — no passwords passed on command lines
- `MAGENTO_SSH_KEY` (path to your private key) is the primary credential
- Passwordless sudo configured on the server via `/etc/sudoers.d/`

**Database access:**
- DB password passed via `MYSQL_PWD` environment variable
- Password is not visible in the process argument list (`ps`, `top`, etc.)
- Dedicated DB user with DML privileges only (no GRANT/DROP)

**REST API:**
- Admin password used only to obtain a short-lived session token
- Password sent in encrypted HTTPS request body only, never on command lines
- Token is obtained per-session, not stored

**TLS:**
- `--cacert MAGENTO_CA_CERT` used for HTTPS calls — not `-k`
- Provide your server's CA certificate as `MAGENTO_CA_CERT` optional env var
- Required if your store uses a self-signed certificate

**Prompt injection:**
- All commands use fixed configuration variables
- Free-form user input is never interpolated into shell commands

---

## Least-Privilege Recommendations

You do not need to grant maximum privileges to use this skill. Recommended
approach:

- **SSH user:** Create a dedicated deploy user; restrict passwordless sudo to
  specific binaries (`php`, `systemctl restart`, `redis-cli`) rather than ALL
- **DB user:** Grant SELECT, INSERT, UPDATE, DELETE on the Magento DB only —
  not GRANT, CREATE, or DROP
- **Magento admin account:** Create a dedicated agent admin user with only the
  roles your agent needs — not your personal admin account
- **SSH key:** Generate a dedicated key pair for the agent; do not reuse your
  personal SSH key

---

## Installation

```bash
clawhub install magento-admin
```

Or manually: copy `skill/SKILL.md` to
`~/.openclaw/workspace/skills/magento-admin/SKILL.md`

### Prerequisites on your Magento server (one-time setup)

1. Authorise your SSH key for the deploy user:
   ```
   # Copy public key content to ~/.ssh/authorized_keys on the server
   ```

2. Configure passwordless sudo for required commands:
   ```
   # /etc/sudoers.d/magento-deploy
   deployuser ALL=(www-data) NOPASSWD: /usr/bin/php8.4
   deployuser ALL=(root) NOPASSWD: /bin/systemctl restart *
   ```

3. Add credentials to your `openclaw.json` env block:
   ```json
   {
     "env": {
       "MAGENTO_HOST":       "your-server-ip",
       "MAGENTO_SSH_USER":   "deployuser",
       "MAGENTO_SSH_KEY":    "/home/agent/.ssh/magento_deploy",
       "MAGENTO_WEB_ROOT":   "/var/www/html/magento2",
       "MAGENTO_PHP":        "/usr/bin/php8.4",
       "MAGENTO_WEB_USER":   "www-data",
       "MAGENTO_DB_NAME":    "magento_db",
       "MAGENTO_DB_USER":    "magento_user",
       "MAGENTO_DB_PASS":    "your-db-password",
       "MAGENTO_BASE_URL":   "https://your-store.example.com",
       "MAGENTO_ADMIN_USER": "admin",
       "MAGENTO_ADMIN_PASS": "your-admin-password",
       "MAGENTO_CA_CERT":    "/opt/ssl/ca.crt"
     }
   }
   ```

---

## Requirements

- OpenClaw agent with exec tool enabled (`exec.ask: "off"` in openclaw.json)
- SSH key authorised on the Magento server
- Passwordless sudo configured for the SSH user
- Magento 2.4.x (tested on 2.4.9)
- PHP 8.1+ on the Magento server
- `ssh`, `mysql`, `curl`, `redis-cli`, `python3` available on the agent server

---

## Who Should Install This

✅ **You should install this if:**
- You own and control the Magento server
- You are the store administrator
- You want to manage your store via natural language through an AI agent
- You have reviewed the commands this skill runs and understand the access it requires

❌ **You should not install this if:**
- You do not own or control MAGENTO_HOST
- You are not authorised to administer the Magento store
- You have not reviewed what this skill will execute on your server

---

## License

MIT
