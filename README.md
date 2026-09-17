# backing up data to the cloud: a plain-English guide to costs, storage types, and plans that won't surprise you on the invoice

Almost everyone agrees backups matter right up until the moment they'd actually have to set one up. Then it becomes a "next weekend" item on the to-do list. The usual reasons are predictable: too many options, unclear pricing, and the nagging worry that you'll pick something and get burned by a bill you didn't understand.

This guide takes a different approach than most. Instead of cheerleading for cloud backup in the abstract, it covers the things you actually need to decide: which storage type fits your data, what the real costs look like (including the egress fees that sneak into hyperscaler invoices), how the 3-2-1 rule shapes a sane setup, and which specific plans — from **Sharktech**, a hosting provider that runs its own data centers in Los Angeles, Las Vegas, Denver, Chicago, and Amsterdam — fit which kind of backup job.

## Why "off-site" is the part people get wrong

Hardware failure gets the headlines, but the scenarios that actually destroy data are broader: ransomware encrypting everything on the network, an accidental `rm -rf` at 2 a.m., a fire or flood, or a stolen laptop. What these have in common is that they don't just hit one device — they hit every copy stored in the same building, on the same network, or attached to the same account.

That's the reasoning behind the 3-2-1 backup rule, which the backup industry has repeated for years because it holds up: keep **three copies** of your data, on **two different types of media**, with **at least one copy off-site**. The cloud is how most people and small businesses satisfy that last part without renting a safe deposit box at a bank across town.

One important distinction before choosing anything: a sync service is not a backup. If a file gets deleted, corrupted, or encrypted by ransomware on your machine, a sync tool will often faithfully replicate that damage to the cloud copy. A real backup keeps point-in-time versions so you can roll back to yesterday, last week, or last month. When you evaluate "backing up data to the cloud," version history and retention policy matter more than the provider's logo.

## The three ways data actually gets to the cloud

There's no single mechanism called "cloud backup." Depending on what you're protecting, you'll end up in one of three patterns.

**Agent-based backup software.** You install a client (Acronis, Veeam Agent, restic, rclone, whatever your shop standardizes on), point it at cloud storage, set a schedule, and it handles encryption, compression, deduplication, and versioning. This is the right pattern for laptops, desktops, and small servers — the data lives on machines you use daily, and the software handles the plumbing.

**S3-compatible object storage as the target.** Object storage is the standard backend for backup tooling. It's cheap per terabyte, scales without pre-planning capacity, and because the S3 API is a de facto industry standard, nearly every backup tool on the market speaks it natively. This is the right pattern when you have a terabyte-scale archive, database dumps, media libraries, or CI artifacts — data that's written once and rarely changed.

**Machine images and snapshots.** If you run servers (VPS, cloud instances, bare metal), a "backup" often means a full disk image rather than individual files. Restore a whole machine, OS and all, in one shot. Not every hosting provider lets you download those images and take them elsewhere — some quietly hold them hostage — so for infrastructure people, "can I export my own backup images?" is a legitimate make-or-break question.

The rest of this guide maps these three patterns onto actual products and prices.

## The egress fee problem, and why flat pricing matters

Here's the trap most people discover on their second or third hyperscaler invoice: storage itself is cheap, but **getting your data back out** is not. The big public clouds charge for outgoing bandwidth ("egress"), and if you ever need to do a full restore — the one moment you really, truly need your backup — pulling 5 TB back down can cost more than a year of storing it. Restores are also exactly when you're stressed and in a hurry, which is a terrible time to be doing arithmetic about per-GB rates.

When you compare backup providers, look at the whole invoice, not just the headline storage price. Sharktech is one of the providers that built its pricing around this complaint. Its S3 object storage page lists exactly two line items — storage and bandwidth — with storage at **$4.90 per TB per month** and the bandwidth line shown at **$0.00** in their example invoice. There's no minimum commitment, no tiered rate card that unlocks only at scale, and custom plans are available if your archive outgrows the standard offering. For context on what that means for a backup archive: 5 TB of cold backup data runs about $24.50/month at that rate.

If you want to see the current numbers yourself, 👉 check Sharktech's flat-rate S3 object storage pricing.

## A practical setup: pick your data, pick your tool, test the restore

Enough theory. Here's the sequence that works for most home-lab and small-business situations.

1. **Decide what actually needs off-site protection.** Your operating system and applications are re-installable; your documents, databases, photos, and config files are not. Don't pay to back up things you can rebuild from a download link.
2. **Choose the storage pattern from the previous section** — agent software for end-user machines, S3-compatible storage for archives and server backups, disk images for whole-machine recovery.
3. **Encrypt before data leaves your machine.** The standard advice from essentially every backup guide is the same: encrypt in transit *and* at rest, and hold the keys yourself. Most backup agents support client-side encryption; check that it's on before your first job runs.
4. **Automate the schedule.** A backup that depends on someone remembering to run it is a backup that stops happening in about three weeks. Daily for actively-changing data, weekly or monthly for archives.
5. **Test a restore every few months.** This is the step everyone skips and the only one that proves anything. Pick a random file, restore it to a different location, confirm it opens. A backup you've never restored from is a hope, not a plan.

For step 2, the tooling decision is easier than it sounds because of the S3 standard. Sharktech's S3 storage is API-compatible with anything that speaks S3 — their docs name Jenkins, GitLab, and Terraform as examples, and the same applies to backup tools — so you're not locked into one vendor's client. You can switch from rclone to restic to Duplicacy without migrating a single byte.

## Managed option: when you want someone else to run the backup

Not everyone wants to manage backup tooling at all. For laptops, desktops, and small office machines, a managed agent-based service is the low-effort path, and Sharktech runs one built on Acronis backup software: it backs up computers, devices, and phones to cloud storage, includes encryption and compression, and restores anything from a single file to an entire system through a web interface or mobile app.

The pricing has a quirk worth knowing before you click checkout. There are **four billing cycles**, and the per-GB overage rate changes with each:

| Cycle | Included storage | Base price | Extra GB |
| --- | --- | --- | --- |
| Monthly | 200 GB | $4.00 | $0.02 |
| Quarterly | 200 GB | $8.00 | $0.04 |
| Semi-annual | 200 GB | $12.00 | $0.06 |
| Annual | 200 GB | $24.00 | $0.12 |

Counterintuitively, the monthly plan is the best value: it's the cheapest entry point and has the lowest overage rate, which matters as soon as you pass 200 GB. A 400 GB workload costs $8/month on the monthly cycle versus roughly $26.40 on the annual one. There's also an optional file sync-and-sharing add-on at $0.03 per GB monthly if you want Dropbox-style sharing on top of pure backup.

If that hands-off approach sounds like your speed, 👉 get started with Acronis cloud backup through Sharktech.

## Full plan comparison: every currently listed option

The table below covers every backup-relevant plan currently displayed across Sharktech's storage, backup, and cloud pages, so you can see the whole menu in one place. Public Cloud tiers are included because they're the standard answer for server workloads where you back up at the machine-image level; Dedicated Cloud tiers are the prepaid, fixed-monthly version of the same infrastructure. The Public Cloud plans include unlimited incoming bandwidth and 5,000 GB outgoing, with additional egress at $0.002/GB; extra IPv4 addresses are $1.50/month beyond the first free one.

| Plan | Core spec | Price (USD) | Billing | Get started |
| --- | --- | --- | --- | --- |
| S3 Object Storage | S3 API, triple redundancy, DDoS-protected network | $4.90/TB | Monthly, flat | [order S3 storage](https://bit.ly/SharKTech) |
| Acronis Cloud Backup — Monthly | 200 GB, +$0.02/GB, encryption + compression | $4.00 | Monthly | [order backup](https://bit.ly/SharKTech) |
| Acronis Cloud Backup — Quarterly | 200 GB, +$0.04/GB | $8.00 | Every 3 months | [order backup](https://bit.ly/SharKTech) |
| Acronis Cloud Backup — Semi-annual | 200 GB, +$0.06/GB | $12.00 | Every 6 months | [order backup](https://bit.ly/SharKTech) |
| Acronis Cloud Backup — Annual | 200 GB, +$0.12/GB | $24.00 | Yearly | [order backup](https://bit.ly/SharKTech) |
| Public Cloud — Small | 4–16 vCPU, 8–32 GB RAM, 300–2,400 GB SSD, up to 1,200 GB NVMe / 4,800 GB HDD | From $39.00 | Monthly (+ hourly overage) | [deploy Small tier](https://bit.ly/SharKTech) |
| Public Cloud — Medium | 8–32 vCPU, 16–64 GB RAM, 800–6,400 GB SSD, up to 3,200 GB NVMe / 12,800 GB HDD | From $79.00 | Monthly (+ hourly overage) | [deploy Medium tier](https://bit.ly/SharKTech) |
| Public Cloud — Large | 32–128 vCPU, 64–256 GB RAM, 1,500–12,000 GB SSD, up to 6,000 GB NVMe / 24,000 GB HDD | From $249.00 | Monthly (+ hourly overage) | [deploy Large tier](https://bit.ly/SharKTech) |
| Public Cloud — Enterprise | 64+ vCPU, 128 GB+ RAM, 5,000 GB+ SSD, uncapped NVMe/HDD | From $499.00 | Monthly (+ hourly overage) | [deploy Enterprise tier](https://bit.ly/SharKTech) |
| Dedicated Cloud — Tiny through Colossal (7 tiers) | Fixed prepaid resource pools, same OpenStack infrastructure | Custom quote | Fixed monthly | [request a Dedicated Cloud quote](https://bit.ly/SharKTech) |

A few notes on reading this table:

- The **Public Cloud** tiers aren't resource presets in the usual sense — you get a pool (say, 8 vCPUs and 8 GB RAM) and can spread it across as many virtual machines as you like, which is handy when one of those VMs is a dedicated backup target.
- **Dedicated Cloud** tiers (Tiny, Small, Medium, Large, Huge, Giant, Colossal) use the same infrastructure but bill a fixed monthly amount for exactly the resources you order, instead of metering overage hourly. Pricing is quoted per configuration rather than published flat, so start a conversation rather than guess.
- Every plan above runs on Sharktech's OpenStack-based platform in their own data centers. One point that matters specifically for backup strategy: you can download your full disk images and backups at any time, so your off-site copy never becomes a hostage if you switch providers later. The company advertises 99.999% uptime and 24/7 phone and email support with real engineers — that's their claim, but it's the kind of thing you can test with a pre-sales question before committing.

To see the current configurations and order links for any tier, 👉 browse the full Sharktech cloud and storage lineup.

## Matching the plan to the job

The decision comes down to two questions: how much data, and how much do you want to manage yourself?

- **Laptop and desktop files, under ~200 GB, minimal setup:** Acronis backup at $4/month. Install the agent, set the schedule, forget about it. The $0.02/GB overage stays sane as you grow.
- **Archives, media libraries, database dumps, anything terabyte-scale:** S3 object storage at $4.90/TB. Point your existing S3-compatible tool at it, keep the versioning on your side, and budget by simple multiplication.
- **Servers and infrastructure:** a Public Cloud instance as your backup target (Small tier from $39/month is plenty for many setups), with exported images as your own off-site layer. If your workload is predictable and you'd rather have a fixed bill than hourly metering, that's exactly what the Dedicated Cloud tiers exist for.
- **Compliance or large custom builds:** skip the guesswork entirely — 👉 ask Sharktech for a custom backup plan, since custom storage plans and dedicated infrastructure are both available on quote.

Whichever route you take, the two habits that separate people who recover from disasters from people who don't are boringly consistent: automate the schedule, and test the restore. Do those two things, and the off-site copy you're uploading today will actually be there when you need it — which is, after all, the entire point.
