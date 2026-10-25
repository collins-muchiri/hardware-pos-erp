# Hardware POS, ERP & Inventory Management System

![category](https://img.shields.io/badge/category-erp-pos-F59E0B?style=flat-square) ![language](https://img.shields.io/badge/language-TypeScript-2563EB?style=flat-square) ![maintenance](https://img.shields.io/badge/maintenance-active-16A34A?style=flat-square) ![repo](https://img.shields.io/badge/repo-hardware--pos--erp-111827?style=flat-square)

---

*Hardware-store point-of-sale, ERP and inventory management built on Next.js 16, Prisma and shadcn/ui.*

## Overview

Hardware retail has inventory shape that generic POS products handle badly:
serial-numbered stock, supplier purchases that arrive partial, stock
adjustments that must be explained, and expenses that belong in the same ledger
as sales. This system is built for that shape. It models the full operational
loop — catalog, purchasing, sales, payments, adjustments and expenses — in a
single Prisma schema, and presents it through a fast, keyboard-friendly
dashboard with a collapsible sidebar and auto-refreshing views. The data model
below is the honest feature list: everything it can do is visible in the
schema it persists.

## Features

- **Sales & payments** — `Sale`/`SaleItem`/`Payment` records with per-item lines and settled payments.
- **Purchasing** — `Purchase`/`PurchaseItem` supplier orders feeding stock on receipt.
- **Inventory control** — `Product`/`Category` catalog with `StockAdjustment` audit trails and `SerialNumber` unit tracking.
- **Customers & suppliers** — `Customer` and `Supplier` masters shared across sales and purchasing.
- **Expenses** — `Expense` entries kept in the same ledger as sales for a single financial picture.
- **Configuration & safety** — `StoreSettings` for store-level configuration and `ErrorLog` for structured failure records.

## Architecture

The application is a single Next.js 16 App Router project: React server
components and route handlers on the server, shadcn/ui components on the
client, and Prisma as the only data-access layer against a SQLite database
(`DATABASE_URL=file:./dev.db` by default). There is no separate backend to
deploy — the Next.js server is the backend — which keeps a store deployment to
one process and one file-backed database. The trade-off is deliberate: this
targets single-store installations where operational simplicity outweighs
horizontal scale.

## Technology stack

| Component | Technology | Why |
| --- | --- | --- |
| App framework | Next.js 16 (App Router) + React + TypeScript | One deployable process for UI and API. |
| ORM / database | Prisma 6 + SQLite | Typed schema, zero-ops file database for single-store use. |
| UI | Tailwind CSS + shadcn/ui | Consistent, accessible dashboard components without a custom design system. |
| Runtime | Bun | Fast install and script execution (`bun run …`). |

## Getting started

Prerequisites: [Bun](https://bun.sh) (and Node.js 18+ for tooling compatibility).

```bash
git clone https://github.com/collins-muchiri/hardware-pos-erp.git
cd hardware-pos-erp
bun install
bun run db:push        # create/sync the SQLite database from prisma/schema.prisma
bun run dev            # http://localhost:3000
```

Database utilities (from `package.json`): `bun run db:generate`,
`bun run db:migrate`, `bun run db:reset`. Production build:
`bun run build` then `bun run start` (serves the standalone Next.js server).

## Usage

A typical counter session: receive stock against a supplier purchase, ring up a
customer sale with per-line quantities, take payment, and adjust any damaged
units with a `StockAdjustment` so the variance is explained in the ledger.
Store identity and receipt behavior live in `StoreSettings`; every failed
operation lands in `ErrorLog` instead of disappearing into a console.

## Status and roadmap

Working today: the complete data model above, the dashboard shell with
collapsible sidebar and auto-refresh views, and the dev/build/start script
chain. Next: deepening receipt printing and multi-store hardening. Explicitly
out of scope by design: cloud-native horizontal scaling — this system targets
single-store deployments on one process.

## Contribution and license

Issues and pull requests are welcome — please open an issue describing the
proposed change before submitting substantive work. No license file is present; the work remains under the author's default copyright. Contact the owner for reuse terms.
