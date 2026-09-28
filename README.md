# igniteX# 📡 SpacePulse – Free Class Locator

> Timetables show where classes are. **SpacePulse reveals where possibilities are.**

SpacePulse is a single-file web app that finds free classrooms, labs and halls on campus right now (or at any time you choose). Type what you need in plain English, such as *"I need an AC room on the ground floor for me and my team for the next 2 hours"*, and it returns the best-matching rooms ranked by fit.

There is no backend, build step or dependency. Open `index.html` in a browser and it works.

---

## ✨ Features

- **Natural-language search** – describe your need and SpacePulse extracts floor, AC, group size, duration, quietness and start time.
- **SpaceFit score (0–100)** – rooms are ranked by how well they match your request.
- **Best match + alternatives** – the top room is highlighted, followed by other fully matching rooms and the closest partial matches.
- **Uninterrupted Work Route** – if no single room is free long enough, it chains two rooms (moving 5 minutes before the next class starts) to give you your full session.
- **Live status board** – every room is colour-coded and grouped by floor:
  - 🟢 free for your whole session
  - 🟡 free but too short
  - 🔴 occupied (with the class, section and when it frees up)
  - ⚪ outside class hours
- **Manual controls** – date picker, time slider (9:00 AM – 4:45 PM), duration, minimum seats, AC-only, quiet mode and floor tabs.
- **Quick actions** – *Now* jumps to the current time; *Demo* jumps to Mon 2:10 PM so you can try it outside class hours.
- **Timetable sanity check** – detects overlapping bookings of the same room by different sections and reports them in the footer.
- **Responsive and theme-aware** – works on phones, and follows the system light/dark mode.

---

## 🚀 Getting Started

1. Save the file as `index.html`.
2. Open it in any modern browser (double-click, or serve it with any static server).

```bash
# optional: serve locally
python3 -m http.server 8000
# then open http://localhost:8000
```

> The Plus Jakarta Sans font loads from Google Fonts. Without internet the app still works and falls back to system fonts.

---

## 💬 Example Queries

| You type | What it understands |
|---|---|
| `I need an AC room on the ground floor for me and my team for the next 2 hours` | AC, ground floor, 120 min |
| `We are 5 people, need a quiet room on the second floor for 90 minutes` | 5 seats, quiet, second floor, 90 min |
| `Any room after lunch for an hour` | starts 1:20 PM, 60 min |
| `team of 8 at 3pm for half an hour` | 8 seats, from 3:00 PM, 30 min |

If no duration is mentioned, 60 minutes is assumed and shown in the "Understood" pills.

**Recognised phrases:** durations (`2 hours`, `90 min`, `half an hour`, `couple of hours`), group size (`5 people`, `team of 4`, `me and 3 friends`), floors (`ground`, `first`, `2nd floor`, `floor 3`), AC (`AC`, `air conditioned`, `cool`), quiet (`quiet`, `silent`, `peace`), and start times (`at 3pm`, `after 2`, `after lunch`).

---

## 🧮 How It Works

1. **Timetable data** – each section has a weekly grid (Mon–Fri, 9 periods a day). Each letter is a subject slot, `-` means no class.
2. **Room lookup** – slots are mapped to rooms using a section's home room, with per-slot overrides for labs, halls and other special rooms. This builds a per-room, per-day list of busy intervals.
3. **Availability** – for the selected day and time, each room is either free (with the minutes until its next class) or busy (with back-to-back classes merged into one block).
4. **Scoring** – SpaceFit out of 100:
   - 40 – free time vs. time needed
   - 20 – floor match
   - 20 – AC match
   - 10 – enough seats
   - 10 – extra buffer beyond the need (120 min window in quiet mode, 60 otherwise)
5. **Routes** – if there's no perfect single room, it searches for pairs of AC, big-enough rooms that together cover the whole session.

Weekends show every room as free. Campus hours are **9:00 AM – 4:50 PM**.

---

## 🛠️ Customising for Your Campus

Everything is in `index.html`; the data lives at the top of the `<script>` block.

| Constant | Purpose |
|---|---|
| `P` | Period start/end times in minutes from midnight |
| `OPEN`, `CLOSE` | Campus hours in minutes from midnight (540 = 9:00 AM, 1010 = 4:50 PM) |
| `SEC` | Sections: `v` home room, `m` slot→room overrides, `s` slot→subject names, `t` five weekly rows (Mon–Fri) |
| `R` | Room metadata: type, capacity, AC (`1`/`0`) |
| `FL`, `DN` | Floor and day labels |

**Adding a section**

```js
"II CSE-A": {
  v: "305",                       // home room
  m: { L: ["107", "108"] },       // slot L is held in these rooms
  s: { A: "Maths", B: "Physics", L: "Physics Lab" },
  t: [                            // one row per day, one token per period
    "A B - - - - - - -",
    "B A - - - L L - -",
    "- - - - - - - - -",
    "- - - - - - - - -",
    "- - - - - - - - -"
  ]
}
```

**Adding a room**

```js
R["305"] = { ty: "Classroom", cap: 60, ac: 1 };
```

The floor is derived from the first digit of the room number (`305` → Third floor; `TB-106` is treated as ground). A lab with two consecutive periods uses the same slot letter twice in a row.

---

## ⚠️ Notes and Limitations

- **"Free" means unscheduled** in the loaded timetables. It does not guarantee the room is physically empty, and ad-hoc bookings, exams and events aren't tracked.
- **AC, seat counts and room types are sample metadata**, not taken from the timetables. Edit `R` to match reality.
- The "quiet" option only favours rooms with a longer free window; it doesn't use noise data.
- Timetables are hard-coded, so schedule changes mean editing the file.
- Natural-language parsing is rule-based (regex), not AI, so unusual phrasings may not be understood. Check the "Understood" pills to confirm what was picked up.
- The Demo button uses the date 2026-09-28 (a Monday).

---

## 🧰 Tech Stack

- HTML, CSS and vanilla JavaScript in one file
- Google Fonts (Plus Jakarta Sans)
- No frameworks, libraries or build tools

---

## 📄 License

Add your preferred license here (e.g. MIT).
