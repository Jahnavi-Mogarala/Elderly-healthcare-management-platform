**Elderly-healthcare-management-platform**

# CareCircle

**Every dose. Every visit. Everyone in the loop.**

CareCircle is a shared healthcare hub for elderly people, their caretakers and their families. Everyone opens the same care profile with a personal passcode, medication schedules and adherence are tracked in one place, and smart email reminders escalate from the elder to the caretaker to the wider family when something is missed.

The elder stays at the centre: family and caretakers can *suggest* medicine changes, but nothing changes until the elder approves.

> **Status:** working front-end prototype (single file, no build step). Reminder emails are **simulated** in an in-app Inbox. 

---

## Features

### Access and roles
- Passcode-based access (4–6 digits) on a large keypad, with a 30-second lockout after 5 wrong tries.
- Four roles: **Elder** (owner), **Caretaker**, **Family**, **Doctor** (view only).
- Elder can invite, edit and remove members, and choose which emails each person receives.
- Passcodes are hashed (SHA-256 with a per-circle salt) before being stored.

### Elder-first decisions
- **Approval queue:** a family member or caretaker proposes adding, editing or stopping a medicine; the elder sees an Approve / Decline card. The requester gets an email with the result.
- Optional setting to let the caretaker change medicines directly.

### Medicines
- Schedules with multiple times a day, chosen weekdays, and food rules (with food / before food).
- Pill illustrations by colour and shape, so the elder can recognise each tablet.
- Stock tracking with days-left forecast and refill alerts; one-tap restock.
- Small built-in interaction check (warfarin + aspirin, and similar). It says clearly that it is **not** an all-clear.

### Reminder and escalation engine
1. Email before the dose, and again at the dose time (to the elder).
2. **Missed** after a configurable grace period, emailed to the caretaker.
3. **Escalation** to the wider family if still unconfirmed.

Also emails for: missing daily check-in, low stock, appointments (24 h and 2 h before, including who is driving), and two consecutive blood-pressure readings above the doctor's limit. All timings are editable in **Circle → Reminder rules**.

### Daily use
- **Today** screen with one large "I took it" button, snooze, skip with reason, read-aloud, and an escalation ladder showing who gets told when.
- Mood check-in and short messages from family.
- Due-dose alarm pop-up with optional sound and speech.
- English, తెలుగు and हिन्दी for the main screens, plus a text-size toggle and a hyper-legible font (Atkinson Hyperlegible).

### Visits and health
- Appointments with a preparation checklist and a "who is taking them" assignment.
- **Doctor summary** generated from the records (adherence, missed doses, readings, caregiver notes, questions to ask), copyable in one tap.
- 7-day adherence grid, streaks, blood pressure / sugar / pulse charts with the doctor's limits shaded.
- Emergency card and **SOS**: 108 / 112 call buttons and one tap to email the whole circle with the card.

### Demo clock
A floating clock lets you jump forward (15 min, 1 h, 3 h, next 7:25 AM) so you can watch reminders, missed-dose alerts and family escalations fire in seconds.

---

## Run it

No install or build needed.

```bash
git clone <your-repo-url>
cd carecircle
# open carecircle.html in any modern browser
```

Or serve it locally:

```bash
python3 -m http.server 8000
# visit http://localhost:8000/carecircle.html
```

Fonts load from Google Fonts. Without internet it falls back to system fonts.

### Demo passcodes

| Person | Role | Passcode |
|---|---|---|
| Lakshmi Devi | Elder (owner) | `1111` |
| Ravi Kumar | Family | `2222` |
| Priya Nair | Caretaker | `3333` |
| Dr. Anand Rao | Doctor (view only) | `4444` |
| Anita Kumar | Family | `5555` |

### 2-minute demo script
1. Open the app, watch the splash, tap **Begin**.
2. Log in as **Ravi (2222)** → Medicines → *Suggest a medicine* → send for approval.
3. Lock, log in as **Lakshmi (1111)** → approve it on the Today screen.
4. Open the **demo clock** (bottom right) → **+1 hour**. The due-dose alarm appears.
5. Skip the alarm, jump **+3 hours**, open **Inbox** to see the missed-dose and family-escalation emails.
6. Try **SOS**, **Visits → Doctor summary**, and **Health** charts.

---

## How it works

- Single HTML file: vanilla JavaScript, no framework, no dependencies.
- State lives in `localStorage` (key `carecircle_v1`), so **each browser/device has its own copy**. A passcode is requested on every load.
- A reminder engine runs every few seconds against a simulated clock. Each event has a de-duplication key, so jumping time forward never double-sends.
- A dose is **upcoming → soon → due → missed**, or **taken / taken late / skipped** once logged.
- Emails are stored in an outbox with recipient, subject, body and severity. "Open in mail app" uses a `mailto:` link as a convenience.

```
carecircle.html
 ├─ splash (canvas animation)        ├─ reminder engine + outbox
 ├─ passcode gate                    ├─ approval queue
 └─ app: Today · Medicines · Visits · Health · Circle · Inbox
```

---

## Roadmap

- [ ] Backend (Node/Python + database) for real multi-device sync and per-user accounts
- [ ] Real email delivery (SMTP / SES / SendGrid) and scheduled jobs for reminders
- [ ] Server-side passcode verification with rate limiting; optional WhatsApp / SMS reminders
- [ ] Web push notifications and offline-first service worker
- [ ] Prescription photo scan (OCR) to add medicines, with human confirmation
- [ ] Bluetooth devices (BP monitor, pulse oximeter) and wearable fall detection
- [ ] Pharmacy refill integration and larger, clinically reviewed interaction database
- [ ] More languages and voice-first interaction

---

## Safety and privacy

- CareCircle provides reminders and organisation, **not medical advice or diagnosis**. A reading or result inside a set range does not rule out illness.
- Reading limits (blood pressure, sugar) are set by the doctor in the app; the demo values are placeholders. Always confirm with a clinician.
- In an emergency call **108** or **112**.
- This prototype stores data unencrypted in the browser and is **not** a hardened, HIPAA/GDPR-ready system. Do not store real patient data in it without a proper backend.
- Demo email addresses use `example.com` and are not real.

---

## 📄 License

This project is licensed under the MIT License.
See the [LICENSE](LICENSE) file for more details.

