# The Shopping List

*Everything needed to stop theorizing and start operating.*

---

## The Math

| Category | Cost |
|----------|------|
| Nodes (6x refurb) | £450 |
| RAM upgrades | £90 |
| SSDs (if the eBay gods are unkind) | £0-84 |
| Networking | £95 |
| Power | £20 |
| Crash cart & accessories | £75 |
| VESA mounts | £40 |
| **Gross** | **£770-855** |
| **Alienware sale** | **-£400** |
| **Net** | **£370-455** |

Under £500 for a 6-node cluster. The M3 Max it replaced cost £3,400 and taught nothing about distributed systems.

---

## Nodes — eBay UK

**Target: 6× HP ProDesk 400 G4 Mini**

| Spec | What to Look For |
|------|------------------|
| CPU | Intel i5-8500T (6-core, good enough) |
| RAM | 8GB DDR4 (you'll upgrade to 16GB) |
| Storage | 256GB SSD (verify — avoid HDD units) |
| Price | £70-85 each |

### Where to Hunt

**eBay UK:**
- [HP ProDesk 400 G4 Mini Category](https://www.ebay.co.uk/b/bn_7117042486)
- Search: `HP ProDesk 400 G4 Mini i5`
- Filter: UK sellers, Buy It Now, sorted by price

**Specific Listings (verify availability):**
- [Item 156355588586](https://www.ebay.co.uk/itm/156355588586) — Scunthorpe seller
- [Item 376628953786](https://www.ebay.co.uk/itm/376628953786) — i5-8500T, Win 11
- [Item 115632328391](https://www.ebay.co.uk/itm/115632328391) — VESA mount included

**UK Refurb Specialists:**
- [Laptops Direct](https://www.laptopsdirect.co.uk/refurbished-hp-prodesk-400-g4-core-i5-8th-gen-16gb-256gb-windows-11-profess-t1-400g4i516gb256gbw11pm/version.asp) — BSI certified, 1-year warranty. Costs more, worries less.
- [Bargain Hardware](https://www.bargainhardware.co.uk/refurbished-desktop-pcs/hp/hp-prodesk-400-g5) — Business-grade refurbs
- [Stone Refurb](https://www.stonerefurb.co.uk/hp-prodesk-400-g5-desktop-mini-intel-core-i3-9100t-8gb-ram-500gb-hdd) — 1-year warranty

**Alternative Models (if ProDesk stock is thin):**
- Lenovo ThinkCentre M920q — [eBay UK](https://www.ebay.co.uk/itm/286350696705)
- Dell OptiPlex 3060 Micro
- HP EliteDesk 800 G4 Mini

*6 × £75 = £450. Watch for bulk deals from the same seller.*

---

## RAM — Amazon UK

Each node ships with 8GB. That's enough for kubelet and disappointment. Upgrade to 16GB.

| Item | Link | Qty | Unit | Total |
|------|------|-----|------|-------|
| Crucial 8GB DDR4-2666 SODIMM | [CT8G4SFS8266](https://www.amazon.co.uk/Crucial-CT8G4SFS8266-PC4-21300-Single-260-Pin/dp/B071KNKBQ1) | 6 | ~£15 | ~£90 |

Alternative: [Crucial CT8G4SFRA266](https://www.amazon.co.uk/Crucial-CT8G4SFRA266-PC4-21300-SODIMM-260-Pin/dp/B08C56KXQJ)

---

## SSDs — Amazon UK (If Needed)

Most eBay refurbs include SSDs. Verify before ordering. If you get unlucky and receive units with HDDs or missing storage:

| Item | Link | Qty | Unit | Total |
|------|------|-----|------|-------|
| Kingston KC600 256GB 2.5" SATA | [SKC600/256G](https://www.amazon.co.uk/Kingston-SKC600-256G-Internal-Encryption/dp/B07ZDBZDR9) | 0-3 | ~£28 | £0-84 |

*A node with an HDD is a node that teaches you patience. Replace it.*

---

## Networking — Amazon UK

| Item | Link | Price |
|------|------|-------|
| TP-Link TL-SG108-M2 8-Port 2.5GbE | [Amazon UK](https://www.amazon.co.uk/TP-LINK-TL-SG108-M2-8-Port-Unmanaged-Desktop/dp/B08SWNG4NN) | £70 |
| Cat6a 0.5m cables | [Amazon UK Search](https://www.amazon.co.uk/cat6-ethernet-cable-0-5m/s?k=cat6+ethernet+cable+0.5m) | ~£12 |
| Cable Matters 5-Pack 1.5m Cat6 | [Amazon UK](https://www.amazon.co.uk/Cable-Matters-5-Pack-Snagless-Ethernet/dp/B00C2DL96W) | ~£12 |

2.5GbE is overkill for k8s learning. Buy it anyway. Future-proofing is cheap; re-cabling is not.

---

## Power — Amazon UK

| Item | Link | Price |
|------|------|-------|
| 8-Way Surge Protected Strip | [Amazon UK Search](https://www.amazon.co.uk/8-way-power-strip-surge-protected/s?k=8+way+power+strip+surge+protected) | ~£20 |

Individual switches are useful. Kill node 3 with a flick to simulate sudden failure. The cluster should handle it. If it doesn't, you'll learn why.

---

## Crash Cart — Amazon UK

For when SSH isn't an option and something has gone properly wrong.

| Item | Link | Price |
|------|------|-------|
| WHOLEV 10.1" HDMI Monitor | [Amazon UK](https://www.amazon.co.uk/Monitors-Display-Speaker-Raspberry-Security/dp/B073P2DV7Y) | ~£50 |
| USB Keyboard (cheap) | [Amazon UK Search](https://www.amazon.co.uk/usb-keyboard/s?k=usb+keyboard) | ~£8 |
| USB Flash Drives 32GB 4-pack | [Amazon UK Search](https://www.amazon.co.uk/usb-drives-32gb/s?k=usb+drives+32gb) | ~£12 |
| Label Tape | Amazon UK | ~£5 |

Alternative monitor: [ELECROW 10.1" Touchscreen](https://www.amazon.co.uk/ELECROW-Touchscreen-Capacitive-1920%C3%971080p-Raspberry/dp/B0823P93SX) (~£90) — nicer, not necessary.

---

## VESA Mounts — Amazon UK

| Item | Link | Price |
|------|------|-------|
| StarTech Thin Client VESA Mount | [Amazon UK](https://www.amazon.co.uk/StarTech-com-Thin-Client-Mount-Computer/dp/B076N6B6GQ) | ~£20 |
| HumanCentric Mini PC Mount | [Amazon UK](https://www.amazon.co.uk/HumanCentric-Bracket-Computer-Monitor-Surface/dp/B07KY3LHVG) | ~£20 |

Buy 2-3. Stack nodes behind a monitor or on a shelf. A tidy cluster is a cluster you'll actually maintain.

---

## The Actual Order

### eBay UK

```
□ Search: "HP ProDesk 400 G4 Mini i5"
□ Filter: UK sellers, Buy It Now
□ Target: 6 units with 8GB RAM + SSD
□ Verify: SSD included (no HDDs)
□ Watch: Bulk deals from same seller
□ Order: 6 units @ £70-85 each
```

### Amazon UK

```
□ TP-Link TL-SG108-M2 Switch              £70
□ 6× Crucial 8GB DDR4 SODIMM              £90
□ Cable Matters Cat6 5-pack               £12
□ 8-way surge protected strip             £20
□ WHOLEV 10.1" HDMI monitor               £50
□ USB keyboard                            £8
□ 32GB USB drives 4-pack                  £12
□ 2× VESA mount brackets                  £40
□ (If needed) Kingston SSDs               £0-84
```

---

## Funding

| Source | Amount |
|--------|--------|
| Alienware R9 sale | +£400 |
| Out of pocket | £0-455 |

The Alienware was 8C/8T from 2019, a GPU you weren't using, and an HDD that made everything painful. It was furniture. Now it's funding.

---

## Timeline

| Item | Expected |
|------|----------|
| Amazon UK | 1-2 days (Prime) |
| eBay UK refurbs | 3-5 days |
| Total setup ready | ~1 week |

Order at the start of Week 3. Hardware arrives during LFS build. By Week 4, you're racking nodes.

---

*Don't optimize this list. Don't spend another day comparing N95 vs 4300U benchmarks. The hardware matters less than you think. The learning matters more than you know.*

*Order it. Start building.*
