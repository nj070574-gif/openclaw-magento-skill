# magento-admin — OpenClaw Skill v5

The definitive Magento 2 administration skill for OpenClaw agents. Connect your AI agent directly to any Magento 2 store via SSH, REST API, GraphQL, and direct database access.

## What This Skill Can Do

### Store Operations
- Health check — full system status in one command
- Cache — flush, clean, enable/disable any cache type
- Indexing — reindex all or specific indexers, fix broken search
- Cron — run, monitor, clear backlog, fix stuck jobs

### Orders and Revenue
- Order management — view, cancel, invoice, ship, refund via REST API
- Revenue reports — today, week, month, custom period
- Abandoned carts — identify and report
- Sales analytics — day-by-day breakdown

### Products and Inventory
- Product admin — stock updates, price changes, enable/disable via REST
- Stock management — out-of-stock reports, bulk updates
- Categories and attributes — list, query, manage
- Import/Export — CSV via Magento CLI

### Customers
- Customer lookup — by email, with full order history
- LTV calculation — lifetime value per customer
- Top customers — ranked by revenue
- Password reset — via REST API

### Configuration and Extensions
- Config management — read/write any config path, raw DB access
- Module management — enable/disable, status check
- Composer — install, remove, update extensions
- Full deployment — upgrade, compile, static deploy, cache flush

### Security
- 2FA management — status check, reset per user
- Admin users — list, create, unlock
- Failed logins — monitor and report

### Infrastructure
- Database — arbitrary SQL, table repair, optimize, size analysis
- Logs — exception, system, cron, Apache
- OpenSearch — health, indices, rebuild
- Redis — status, flush by database
- GraphQL — store config, product search, category tree

### Email and Marketing
- SMTP — config check, send test email
- Coupons — usage stats, generate via REST API
- Price rules — cart and catalog rules

## Installation

Via ClawHub:
```
clawhub install magento-admin
```

Manual: copy skill/SKILL.md to ~/.openclaw/workspace/skills/magento-admin/SKILL.md

## Requirements

- OpenClaw agent with exec tool enabled
- sshpass installed on the agent server
- SSH access to your Magento server
- Magento 2.4.x (tested on 2.4.9)
- PHP 8.1+ on the Magento server

## License

MIT
READMEEND && echo README_OK
