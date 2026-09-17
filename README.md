# online server backup: build a setup that actually restores, from $4 a month for 200GB of cloud backup

Most people don't think about online server backup until the day they need it — and that's exactly the problem. A backup you've never restored from is more of a hope than a plan. This guide walks through what actually works for backing up servers to remote storage, the mistakes that quietly ruin otherwise reasonable setups, and one concrete option worth knowing about: Sharktech, a hosting provider that resells an Acronis-powered cloud backup service starting at $4 a month for 200GB. 👉 [See Sharktech's backup and hosting plans](https://bit.ly/SharKTech)

## What online server backup means (and what it doesn't)

An online server backup is a copy of your server's data — files, databases, configurations, or full disk images — stored somewhere off the server itself, usually in a provider's cloud storage. The "online" part matters: the copy lives on different hardware, often in a different building, so a dead drive, a ransomware infection, or a fat-fingered `rm -rf` doesn't take the copy down with it.

It's worth separating backup from two things it gets confused with:

- **Sync** (Dropbox-style, or file sync add-ons) mirrors your files across machines. Delete a file on one end, and the deletion propagates. Sync is great for collaboration and terrible as your only backup.
- **Replication** copies data in near real time to a second server. It protects against hardware failure, but if the data itself gets corrupted or encrypted by malware, replication faithfully copies the damage too.

A proper backup keeps point-in-time versions, so you can roll back to last Tuesday, before the corruption happened.

The shorthand most of the industry uses is the **3-2-1 rule**: three copies of your data, on two different types of media, with at least one copy off-site. If you run servers — whether a single VPS or a rack of bare metal — the off-site copy is the one that saves you when the first two fail together, which happens more often than anyone would like.

## The four ways backups fail (all of them preventable)

Before shopping for a service, it helps to know what you're defending against. The failure modes are consistent across companies of every size:

1. **Untested restores.** The backup runs every night, the logs look green, and then the restore fails because an agent silently stopped working six months ago. If you never test a restore, you don't have a backup — you have a scheduled job that makes you feel good.
2. **Backups sitting next to the source.** A second drive in the same chassis, or a backup server in the same rack, shares the same failure domain. Fire, flood, power spike, or a compromised admin account takes both out at once.
3. **Ransomware that finds the backups.** Modern attacks explicitly hunt for backup storage and mounted network shares before detonating. Ransomware can't easily touch a cloud copy that isn't permanently mounted as a drive.
4. **Egress fees at the worst moment.** Some cheap storage tiers make restoring your own data an expensive exercise. AWS Glacier-style archive classes, for instance, charge for retrieval speed. A disaster is a bad time to discover your restore costs hundreds in exit fees.

Any online server backup setup you build should be measured against that list, not against marketing pages.

## Your main options for off-site server backup

There's no single "correct" tool, but in practice, setups fall into a few recognizable buckets. Which one fits depends on how much data you have, what your recovery time needs to be, and how much administration you're willing to do.

**Host-based backup software with cloud storage.** An agent (or script) runs on your server, grabs changed data on a schedule, encrypts it, and ships it to remote storage. Commercial tools in this space include Veeam, Acronis Cyber Protect, and IDrive; on the DIY side, restic, borg, and rclone paired with object storage like Backblaze B2 or Wasabi are popular on forums for a reason — per-GB pricing is transparent and the tooling is scriptable.

**Image-level backups and snapshots.** Instead of picking files, you back up whole disk images. Restores are slower but complete: OS, configs, applications, everything. Many hosting providers offer snapshot features, but check whether snapshots are stored separately from the host machine — a snapshot on the same storage array doesn't satisfy 3-2-1 on its own.

**Self-managed copies on rented infrastructure.** Rent a cheap VPS or object storage, push your backups there with your own scripts, and verify periodically. This is the cheapest route in dollars and the most expensive in your time. It works well until the person who wrote the script leaves.

**Managed backup as an add-on from your host.** Several hosting providers sell backup bolt-ons alongside their servers. The advantage is one vendor, one bill, and support staff who know both your server and the backup system — you're not explaining your stack to two different companies at 2 a.m.

## Where Sharktech fits in

Sharktech is a US hosting company founded in 2003, running data centers in Los Angeles, Las Vegas, Denver, Chicago, and Amsterdam. They're best known for DDoS-protected hosting, but they also resell a cloud backup service built on Acronis, positioned at roughly half the cost of buying comparable protection elsewhere. It covers the standard bases: agent-based backups of servers and workstations, configurable schedules (daily down to hourly), encryption of the stored data, and file-level or whole-system recovery options.

The pricing is published plainly, which is more than can be said for a lot of the industry:

- **$4 a month** for 200GB of cloud backup storage, then **$0.02 per additional GB**
- **$8 every 3 months** for 200GB, then **$0.04 per additional GB** per cycle
- **$12 every 6 months** for 200GB, then **$0.06 per additional GB**
- **$24 a year** for 200GB, then **$0.12 per additional GB**

Longer billing cycles drop the effective monthly cost — the annual plan works out to $2 a month for the base 200GB. There's also an optional file sync & share add-on if you want Dropbox-style sharing on top of backup, billed separately ($0.03 per GB monthly, with the same doubling pattern per longer cycle).

What does this mean in practice for a typical small setup? Say you have a web server with about 1TB to protect. On the monthly plan, you'd pay $4 for the first 200GB plus 800 × $0.02 for the rest — roughly **$20 a month** for a full terabyte of encrypted off-site backup, computed straight from their published rates. Compared with rolling your own on object storage once you factor in your time — or with enterprise Veeam licensing — that's a defensible price point for small and mid-sized workloads.

Two more details worth knowing if you're already a Sharktech customer or considering their cloud hosting. First, independent review site HostAdvice, in its hands-on review of Sharktech's public cloud, found the backup offer surfaces right in checkout — a pop-up suggesting an Acronis Cloud Backup plan at an additional $4.00 a month before you finalize an order. Second, Sharktech's OpenStack-based cloud explicitly lets you download your VM disk images whenever you want, which makes their infrastructure a viable *destination* for image-level off-site backups, and means you're never locked in if you decide to move.

Their support angle is also unusual for the price bracket: 24/7 phone and ticket support with real humans, and HostAdvice's testing measured ticket replies in under 40 minutes at 1 a.m. That matters specifically for backups, because the moment you actually need support is usually the moment your business is bleeding.

👉 [Compare Sharktech's cloud and backup options](https://bit.ly/SharKTech)

## Full pricing table: Sharktech cloud backup plans

The table below covers every backup billing cycle Sharktech currently publishes. All plans include 200GB of Acronis-powered cloud backup storage with encryption; the difference is how you're billed and the per-GB rate for extra capacity.

| Plan (billing cycle) | Price | Included storage | Extra storage rate | Effective monthly base | Buy |
| --- | --- | --- | --- | --- | --- |
| Monthly | $4.00 / month | 200 GB | $0.02 / GB | $4.00 | [Get monthly backup](https://bit.ly/SharKTech) |
| Quarterly | $8.00 / 3 months | 200 GB | $0.04 / GB per cycle | ~$2.67 | [Get quarterly backup](https://bit.ly/SharKTech) |
| Semi-annual | $12.00 / 6 months | 200 GB | $0.06 / GB per cycle | $2.00 | [Get semi-annual backup](https://bit.ly/SharKTech) |
| Annual | $24.00 / year | 200 GB | $0.12 / GB per year | $2.00 | [Get annual backup](https://bit.ly/SharKTech) |

**Optional add-on — file sync & share:** $0.03 per GB monthly (or $0.06 quarterly, $0.12 semi-annually, $24-relevant annual rates per the published schedule), added on top of backup storage if you also want file sharing across devices. 👉 [View add-on details and current pricing](https://bit.ly/SharKTech)

A couple of honest observations from the numbers. If your dataset stays under 200GB, the annual plan at $24 a year is hard to argue with. If you're pushing multiple terabytes, the per-GB overage rates mean monthly costs scale linearly — fine and predictable, but at that scale it's worth pricing against raw object storage and a script, because your per-GB cost there can be lower at the cost of doing the engineering yourself. And unlike hyperscaler archive tiers, there are no published retrieval fees here; restoring your own data costs bandwidth, not ransom.

## How Sharktech compares to the usual alternatives

For context, the general backup market splits into consumer-grade services (Backblaze Personal, Carbonite — priced for laptops, not servers), prosumer/business tools (IDrive, which PCMag's 2026 picks highlight as a top backup service; Backblaze B2 for raw per-GB storage), and enterprise platforms (Veeam, Acronis Cyber Protect direct, Druva). Pricing philosophies differ: consumer plans are flat-rate and unlimited-ish but licensed per device; per-GB services scale cleanly but punish uncontrolled data growth; enterprise platforms bundle anti-ransomware and compliance features at a price that assumes an IT budget.

Sharktech's offering sits in a specific niche: it's essentially **Acronis technology resold at hosting-provider prices**, aimed at people who already run infrastructure (or want to run it with the same provider). You give up the polish of buying direct from Acronis's own portal — you're going through a smaller company's billing system — and in exchange the entry price is a fraction of what direct Acronis licensing typically costs. For a solo admin or an SMB with a handful of servers and less than a terabyte of critical data, that trade is reasonable. If you need compliance certifications, immutable WORM storage guarantees, or petabyte-scale dedupe, you're outside this product's design range and should be shopping in the enterprise tier.

## What real users say (the good and the ugly)

A provider's backup service deserves extra scrutiny on reputation, because you only find out how good it is on your worst day. The honest picture of Sharktech, from reviews that actually exist:

Sharktech sits around **3.4 out of 5 on Trustpilot across a small sample of 13 reviews** — low volume, so treat it as directional, not definitive. The recent positives are specific: users praise a roughly $8/month VPS as the cheapest reliable option they've found, report a year of uptime without incidents, and call the yearly VPS deals among the best around. HostAdvice's expert review rates Sharktech's public cloud 9.4/10 overall, with support responsiveness (9.5) and features (9.6) as standout scores.

The negatives are equally worth reading. A 2022 reviewer claims Sharktech lost their files and couldn't restore from a backup they'd paid for — the single most damning complaint a backup service can receive, even allowing that it's one side of the story. A 2025 reviewer describes billing disputes around canceled services and PayPal subscriptions, resolved via chargebacks. A March 2026 reviewer complained about a KYC identity-verification process that suspended their server a day after activation. None of these are backup-specific patterns, but the 2022 restore failure in particular is why "trust but verify" applies here more than with most products.

The practical takeaway: Sharktech's pricing and support are genuinely competitive, and the review trajectory over the last two years trends positive. But given that one documented restore failure — and the general principle that untested backups are prayers — whatever provider you choose, schedule your first test restore the same week you sign up. If a small provider's support team handles a test-restore ticket quickly and well, that tells you more than any review page.

## A checklist to run before you commit to any backup plan

Whether you end up with Sharktech, Backblaze B2, IDrive, or a DIY setup, run through this before money changes hands:

1. **Do the math on total data, not just the base tier.** Extra-GB rates dominate your bill once you pass the included allowance. The $4 headline price is only real if your data fits in 200GB.
2. **Confirm the restore path.** File-level restore for "I deleted one file" and image-level restore for "the whole server is gone" are different operations with different time costs. Know how both work, and roughly how long a full restore takes at your data size.
3. **Check the encryption model.** Data should be encrypted in transit and at rest. If you can bring your own key, better — that's what keeps a provider insider or a provider breach from becoming your breach.
4. **Test the first restore immediately.** Not next quarter. Immediately.
5. **Set retention that beats ransomware.** Keep enough point-in-time versions that an infection which sat dormant for two weeks doesn't burn all your recovery points. Daily versions with 30-day retention is a reasonable floor.
6. **Know the exit cost.** Restoring your own data — or downloading everything to move to a competitor — should be free or nearly free. Egress-heavy archive tiers fail this test; Sharktech's published model, with no listed retrieval fees, passes it on paper.
7. **Match the support model to your team.** If you're a one-person IT department, 24/7 phone support isn't a luxury during a restore emergency; it's the difference between a bad night and a lost week.

## The short version

Online server backup rewards boring competence: a scheduled encrypted copy, stored off-site, with tested restores and predictable pricing. Sharktech's Acronis-based service is one of the more sensible entry points on price — $4 a month for 200GB, scaling at $0.02 per GB, with the annual plan effectively cutting the base cost in half — and it's especially logical if you already run your servers with them, since a single vendor supports both the workload and its safety net. The small but real history of complaint reviews means you should verify with a test restore early rather than trust the logo on the portal.

If your needs fit the profile — a few servers, under a couple of terabytes, a preference for humans over ticket queues — it's worth a look. 👉 [Check current Sharktech backup plans and pricing](https://bit.ly/SharKTech)
