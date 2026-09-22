# Self-Hosted ERPNext on Rocky Linux / KVM

A hands-on lab built to learn ERP concepts (MRP, BOM costing, general ledger
mechanics) by verifying them against a real system instead of taking a
textbook's worked examples at face value.

## What this is

- A Rocky Linux 9 KVM guest, provisioned via cloud-init, running ERPNext
  (via `frappe_docker`) on a home lab node.
- A set of exercises rebuilding standard ERP/MRP textbook examples
  (production planning, materials requirements planning, inventory costing)
  and checking the results directly against the ERPNext database.

## Why

Textbook ERP examples (bills of material, MRP records, standard costing)
are usually presented as SAP screenshots with numbers that are simply
asserted. This project re-derives those numbers from scratch in an actual
ERP system, to confirm the underlying mechanism rather than the presentation.

## Infra.

- **Host**: Rocky Linux 9, KVM/libvirt
- **Guest provisioning**: cloud-init (`infra/cloud-init/`), scripted with
  `virt-install` (`infra/virt-install.sh`)
- **ERP**: ERPNext, deployed via `frappe_docker`'s `pwd.yml`
- Notes on issues hit along the way (libvirtd not running, missing seed
  ISO, KVM acceleration falling back to software emulation, etc.) are in
  `infra/notes.md`

## 1. Sys Arch. Topology

```text
[ Remote Operator Client ]
        │
        │  (Encrypted Transport Layer: SSH Tunnel)
        ▼
[ Hypervisor Host Node ]
  ├── Virtualization Engine: KVM / QEMU / libvirt
  ├── Network Bridge: Isolated Virtual Switch (NAT/Private Subnet)
  └── [ Virtual Machine Guest ]
        ├── Compute Resources: Configurable vRAM / vCPU / Dynamic vDisk
        └── [ Container Engine: Docker & Compose ]
              ├── Edge Ingress: Reverse Proxy (Nginx)
              ├── Application Core: Frappe / ERPNext
              ├── Relational Backend: MariaDB
              ├── Key-Value Stores: Redis (Cache & Queue Brokers)
              ├── Real-time Engine: WebSocket Daemon
              └── Asynchronous Workers: Distributed Job Queues


## Verification exercises

- `verification/mrp-record.md` — hand-derived MRP record (gross/net
  requirements, planned orders) compared against ERPNext's Production Plan
  and Stock Projected Qty outputs
- `verification/bom-cost-check.md` — BOM-rolled-up cost verified against a
  worked textbook example
- `verification/gl_queries.sql` — SQL queries against `tabGL Entry` and
  `tabStock Ledger Entry` confirming that a Purchase Receipt posts to
  inventory and the general ledger simultaneously

## Scope / non-goals

This is a learning lab, not a production deployment — no public hosting,
no real business data. Config here is meant to be readable and adaptable,
not hardened.



