# CTF Write-Up: [Have a Break](https://tryhackme.com/room/haveabreak)

> **Platform:** TryHackMe | **Difficulty:** Medium

---

## Flags

<details>
<summary>Q1 — Which VPN service was used to send the anonymous email?</summary>

`Mullvad`
</details>

<details>
<summary>Q2 — What is the full street address of the petrol station where the missing vehicle was last seen?</summary>

`Kroměřížská 1281, 768 24 Hulín, Czechia`
</details>

<details>
<summary>Q3 — At what time did the suspicious action take place in the route planning system on 25 March 2026?</summary>

`22:14:09`
</details>

<details>
<summary>Q4 — What is the employee ID of the person who sent the anonymous email?</summary>

`BR-0312`
</details>

<details>
<summary>Q5 — What is the employee ID of the employee responsible for leaking the shipment details?</summary>

`BR-0291`
</details>

<details>
<summary>Q6 — What is the full name of the culprit?</summary>

`Radovan Blšťák`
</details>

---

## Evidence Files

| File | Description |
|------|-------------|
| `ecta_memo.html.pdf` | ECTA briefing document — start here |
| `exhibit_a.eml` | Anonymous tip email |
| `exhibit_b.png` | Dashcam image from D1 corridor |
| `transeuro_data/employees.csv` | Employee records |
| `transeuro_data/access_log.csv` | IT access logs |
| `transeuro_data/comms_export.txt` | Internal communications |

---

## Task 01 — VPN Service Used for Anonymous Email

**Question:** Which VPN service was used to send the anonymous email from the `.eml` file?

**Evidence:** `exhibit_a.eml`

Extract the email headers to find the originating IP:

```bash
grep "Received" exhibit_a.eml
```

Output:
```bash
Received: by 2002:a05:640c:2247:b0:261:9202:4f12 with SMTP id h7csp418823eiw;
X-Received: by 2002:a05:6402:4112:b0:55d:741:a88c with SMTP id
Received: from mail-sor-f41.google.com (mail-sor-f41.google.com. [209.85.220.41])
Received-SPF: pass (google.com: domain of notmyname2847@gmail.com designates
X-Received: by 2002:a05:6402:4892:b0:66b:c5e3:77f2 with SMTP id
Received: from [193.32.249.132] ([193.32.249.132])
```

The last `Received` header is the key one — it shows the IP the email originated from before hitting Google's servers: `193.32.249.132`. Everything above that is just Google's internal routing.

Search `"193.32.249.132"` on **[Google](https://www.google.com/)**:

![Google Search](/Images/Screenshots/1.png)

Top result leads to **[Spur.us](https://spur.us/context/193.32.249.132)**:

![Spur VPN Detection](/Images/Screenshots/2.png)

Spur flags it as a **Mullvad VPN** exit node based in Amsterdam — the sender deliberately anonymised their traffic before sending the tip.

> 🚩 `Mullvad`

---

## Task 02 — Full Street Address of Petrol Station

**Question:** What is the full street address of the petrol station where the missing vehicle was last seen?

**Evidence:** `exhibit_b.png` — Dashcam image timestamped 26 Mar 2026 at 22:31 CET

![Dashcam Image](/Images/exhibit_b.png)

From the ECTA memo (`ecta_memo.html.pdf`), the relevant passage reads:

> A dashcam SD card was recovered from a vehicle stopped for an unrelated traffic matter near **Hulín** on the **D1 corridor**. The image shows a large HGV at truck parking, consistent with the missing vehicle. Timestamp: 26.03.2026 — 22:31.

So the vehicle departed Central Italy on the morning of 26 March, and was captured on dashcam at 22:31 that night — an overnight transit window consistent with the Italy→Poland route via Czech Republic.

**Method — Geolocation via Google Maps:**

1. Set directions **Olomouc → Brno** along the D1 corridor
2. Add **Hulín** as a stop (it's the dashcam recovery location, so the station must be near there)
3. Search for **ORLEN** service stations along that segment

![ORLEN Station on Google Maps](/Images/Screenshots/3.png)

There's one ORLEN station that fits — near Hulín, with truck parking, open 24/7, right on the D1.

> 🚩 `Kroměřížská 1281, 768 24 Hulín, Czechia`

---

## Task 03 — Time of Suspicious Route Planning Action

**Question:** At what time did the suspicious action take place in the route planning system on 25 March 2026? (Format: HH:MM:SS)

**Evidence:** `transeuro_data/access_log.csv`

Filter the log for 25 March:

```bash
grep "2026-03-25" access_log.csv
```

Output:
```bash
2026-03-25,08:55:19,PR-0114,CAPACITY_MARCH_2026.xlsx,VIEW
2026-03-25,09:05:41,BR-0334,ROUTE_DE_PL_Q1_2026.pdf,VIEW
2026-03-25,09:20:33,BR-0204,ROUTE_CZ_SK_Q1_2026.pdf,VIEW
2026-03-25,09:21:14,BR-0204,ROUTE_AT_HU_Q1_2026.pdf,VIEW
2026-03-25,09:44:07,BR-0291,DRIVER_SCHEDULE_WK13.xlsx,VIEW
2026-03-25,10:05:22,BR-0291,ROUTE_IT_PL_Q1_2026.pdf,VIEW
2026-03-25,10:19:08,PR-0122,ROUTE_IT_PL_Q1_2026.pdf,VIEW
2026-03-25,11:30:44,PR-0098,DRIVER_SCHEDULE_WK12.xlsx,VIEW
2026-03-25,12:02:17,BR-0334,CAPACITY_MARCH_2026.xlsx,VIEW
2026-03-25,13:17:33,BR-0188,INSURANCE_Q1_TEMPLATE.docx,EDIT
2026-03-25,14:02:18,PR-0114,CAPACITY_MARCH_2026.xlsx,EDIT
2026-03-25,14:44:55,BR-0312,DRIVER_SCHEDULE_WK13.xlsx,VIEW
2026-03-25,16:44:08,BR-0204,DRIVER_SCHEDULE_WK13.xlsx,VIEW
2026-03-25,19:55:02,PR-0122,ROUTE_IT_PL_Q1_2026.pdf,VIEW
2026-03-25,22:14:09,BR-0291,ROUTE_IT_PL_Q1_2026.pdf,EXPORT   ← suspicious
2026-03-25,23:41:17,BR-0312,DRIVER_SCHEDULE_WK13.xlsx,EDIT
```

One line stands out immediately:

```bash
2026-03-25,22:14:09,BR-0291,ROUTE_IT_PL_Q1_2026.pdf,EXPORT
```

Three red flags at once:
- **After hours** — 22:14 is well outside any normal working day
- **EXPORT, not VIEW** — this isn't someone reading a file, it's someone taking it
- **The exact file** — `ROUTE_IT_PL_Q1_2026.pdf` is the Italy-Poland route. The truck goes missing the next day.

> 🚩 `22:14:09`

---

## Task 04 — Employee ID of Whistleblower

**Question:** What is the employee ID of the person who sent the anonymous email?

**Evidence:** `transeuro_data/comms_export.txt`

A staff broadcast from IT Systems (BR-0255) on 24 March stands out:

```bash
[2026-03-24 09:11] br0255@transeuro-log.cz
Reminder to all staff — personal email addresses must not be
used for accessing or sharing company files. This morning a
request was received from an external address
(kraliknovak09@gmail.com) to access files in the route
planning shared folder. The request was blocked. Please use
your company account for all work-related activity.
```

Someone inside the company read that message, watched the theft unfold, and tipped off a journalist anonymously rather than going through official channels. The anonymous email itself says:

> *"I saw unusual activity in our internal system the night before departure. Access to a file that had no reason to be touched at that hour."*

This points to someone who was active late at night and would recognise an after-hours EXPORT as abnormal. Cross-referencing the comms log:

```bash
[2026-03-24 23:41] br0312@transeuro-log.cz
WK13 driver assignments updated — three changes from the
draft, all confirmed. Notes in the schedule file.
```

BR-0312 (Dispatch Operator) was working late on 24 March, visible in both the comms log and access records. As someone who coordinates directly with drivers, they would immediately recognise that exporting a live route file after-hours was deeply abnormal.

Two further details from the `.eml` file corroborate this:
- The email was sent from **K-9 Mail for Android** — a mobile email client, consistent with someone acting outside of work hours
- It was addressed to **`redakce@novinybrno.cz`** — a local Brno newspaper. BR-0312 is based in **Brno**

The evidence is circumstantial but coherent across comms, access logs, and the email itself.

> 🚩 `BR-0312`

---

## Task 05 — Employee ID Responsible for Leaking Shipment Details

**Question:** Which employee leaked the route data?

**Evidence:** `transeuro_data/access_log.csv` + `transeuro_data/employees.csv`

The EXPORT entry from Task 03 names them directly:

```bash
2026-03-25,22:14:09,BR-0291,ROUTE_IT_PL_Q1_2026.pdf,EXPORT
```

Employee database lookup:

```bash
BR-0291, Route Planner, Brno, Králice nad Oslavou, br0291@transeuro-log.cz
```

And their full access trail tells a clear story:

```bash
2026-03-24,07:11:03,BR-0291,ROUTE_IT_PL_Q1_2026.pdf,AUTH_FAILED    ← first attempt, failed
2026-03-24,09:51:07,BR-0291,ROUTE_IT_PL_Q1_2026.pdf,VIEW           ← got in, started looking
2026-03-25,10:05:22,BR-0291,ROUTE_IT_PL_Q1_2026.pdf,VIEW           ← back again
2026-03-25,22:14:09,BR-0291,ROUTE_IT_PL_Q1_2026.pdf,EXPORT         ← takes the file
2026-03-27,09:12:07,BR-0291,ROUTE_IT_PL_Q1_2026.pdf,ACCESS_DENIED  ← locked out post-breach
```

On top of that, BR-0291 also tried to access the route planning folder via an external personal email (`kraliknovak09@gmail.com`) on 24 March — the same address IT flagged in the staff broadcast. That's not a coincidence.

> 🚩 `BR-0291`

---

## Task 06 — Full Name of Culprit

**Question:** What is the full name of the person behind `kraliknovak09@gmail.com`?

**Evidence:** External email address from `transeuro_data/comms_export.txt`

**Method — Email OSINT using [EPIEOS](https://epieos.com):**

Search `kraliknovak09@gmail.com`:

![EPIEOS Email Search](/Images/Screenshots/4.png)

EPIEOS links the address to Google Account ID `103790956576446810107`, which has an active Google Maps contributor profile. Visit the profile URL and the display name is right there:

![Google Maps Profile](/Images/Screenshots/5.png)

**Radovan Blšťák** — Route Planner, Brno, hometown Králice nad Oslavou. That's BR-0291.

> 🚩 `Radovan Blšťák`

---

## Investigation Timeline

```
24 Mar 07:11  → BR-0291 fails first authentication attempt on ROUTE_IT_PL_Q1_2026.pdf
24 Mar 09:11  → kraliknovak09@gmail.com blocked from route planning folder; IT notifies all staff
24 Mar 09:51  → BR-0291 views ROUTE_IT_PL_Q1_2026.pdf (reconnaissance)
25 Mar 10:05  → BR-0291 views file again (target confirmation)
25 Mar 22:14  → BR-0291 EXPORTS ROUTE_IT_PL_Q1_2026.pdf (data exfiltration)
26 Mar (morn) → HGV departs Central Italy with 400,000+ KitKat units
26 Mar 22:31  → Vehicle last sighted at ORLEN station, Hulín
26 Mar (eve)  → BR-0312 sends anonymous tip via Mullvad VPN (193.32.249.132)
27 Mar 09:00+ → File access revoked; investigation begins
```

---

## Techniques Summary

| Task | Objective | Technique | Tool |
|------|-----------|-----------|------|
| Q1 | Identify VPN from anonymous email | Email header extraction + IP reputation lookup | [Google](https://www.google.com/) + [Spur.us](https://spur.us) |
| Q2 | Locate service station from dashcam | Geolocation via route analysis | [Google Maps](https://www.google.com/maps/) |
| Q3 | Find suspicious data exfiltration time | Access log analysis + anomaly detection | `grep` |
| Q4 | Identify whistleblower | Internal comms + access pattern analysis | Manual log review |
| Q5 | Match employee to data leak | Log correlation + employee database lookup | `access_log.csv` + `employees.csv` |
| Q6 | Unmask culprit's real identity | Email OSINT + Google Account enumeration | [EPIEOS](https://epieos.com) + [Google Maps](https://www.google.com/maps/) |

---

## Key Findings

**Culprit**
- **Name:** Radovan Blšťák
- **Employee ID:** BR-0291 (Route Planner, Brno)
- **Personal email alias:** kraliknovak09@gmail.com (Google ID: 103790956576446810107)
- **Hometown:** Králice nad Oslavou
- **Role in heist:** Leaked the Italy-Poland shipment route to an external theft network

**Whistleblower**
- **Employee ID:** BR-0312 (Dispatch Operator, Brno)
- **Method:** Anonymous email via Mullvad VPN exit node (193.32.249.132, Amsterdam)
- **Motivation:** Aware of the breach, chose to report externally rather than trust internal channels

**Cargo & Impact**
- **Stolen:** 400,000+ KitKat units
- **Estimated value:** €2M+
- **Route:** Italy (Ferentino) → Poland via Czech D1 corridor
- **Last known location:** ORLEN service station, Hulín — 26 Mar 2026 at 22:31 CET

---

*Have a Break — TryHackMe | Completed ✅*