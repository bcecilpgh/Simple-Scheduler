# Simple Scheduler

A free, open-source Q-SYS plugin that does two jobs:

- **Weekly schedule triggers.** Up to 16 schedules, each firing a trigger output at a
  set time on the days you pick.
- **Automatic snapshot backup.** Once a week it saves your design's current state into a
  Q-SYS Snapshot bank, rotating through up to 12 slots so you always have that many weeks
  of history.

No licence key, no activation, no demo timer. MIT licensed.

---

## Install

1. Download `SimpleScheduler.qplugx` from the
   [latest release](https://github.com/bcecilpgh/Simple-Scheduler/releases).
2. Double-click it. Q-SYS Designer installs it to your Plugins folder.
3. In Designer, find **Fresh AV Labs > Simple Scheduler** in the Schematic Elements list and
   drag it into your design.

To update later, download the new `.qplugx` and double-click it again. The filename does not
change between versions, so the new build replaces the old one in place.

---

## Page 1 — Schedules

Set **Schedule Count** in the plugin's Properties panel (1 to 16). Each schedule gets its own
card:

| Control | What it does |
|---|---|
| **Enable** | Arms the schedule. Ships on. |
| **Hour** / **Minute** | The fire time. Minutes are in 5-minute steps. |
| **Sun**–**Sat** | The days it fires on. All ship off, so a new schedule fires nothing until you pick at least one day. |
| **Schedule Name** | A label for your own use. Does not affect firing. |
| **Trigger** | The output pin. Wire it to whatever you want to happen. |

The summary line under each card shows what the schedule will do, or `Disabled`.

A schedule fires once when the clock reaches its time on a selected day. The plugin uses the
Core's local time.

---

## Page 2 — Snapshot Backup

This page saves your design's state into a Snapshot bank on a weekly rotation, so you can roll
back to any week still held in a slot.

**Before you start, set the Snapshot bank's Script Access.** Right-click the Snapshot
component, open Properties, and set **Script Access** to `Script` or `All`. The default is
`None`, and a bank set to `None` is invisible to the plugin with no error — it simply will not
appear in the list.

If a bank you already picked goes away later — renamed, deleted, or set back to `None` — the
plugin keeps your selection and turns the status bar orange, so you can see what broke. Press
**Rescan** and it clears that dead name out of the list for you.

Then:

1. Set **Backup Slot Count** in the Properties panel to the number of snapshots your bank
   holds. The default is 4, which matches a stock Q-SYS Snapshot block. The range is 2 to 12,
   and one slot is one week of history.
2. Pick your bank in **Snapshot Bank**. Press **Rescan** if you add or rename one later.
   **Picking a bank switches every slot on**, so there is nothing else to arm.
3. Set the **Day**, **Hour** and **Minute** of the weekly window. The default is Sunday 03:00.

Each slot shows when it was last written, and has a **Save Now** button for an immediate save.

The slots ship **off**, and only that first bank selection turns them on. Nothing switches
them on again after that — not a Rescan, not re-picking the same bank, not a Core reboot —
so a slot you deliberately turn off stays off. If you **raise Backup Slot Count later**, the
slots that appear come up off; switch them on yourself, or clear **Snapshot Bank** and pick it
again to arm the whole set.

### How the rotation picks a slot

Every window, exactly one slot is saved: the next enabled slot after the one with the newest
timestamp, wrapping from the last slot back to the first. So the slots hold that many weeks,
oldest first in line to be overwritten.

Three things worth knowing:

- **Save Now steers the rotation.** The plugin has no separate pointer — it reads the
  timestamps. A manual save moves the queue on by one.
- **Turning a slot off protects it.** The rotation skips a disabled slot, but **Save Now**
  still writes to it. That is how you keep one slot as a known-good snapshot from
  commissioning that the weekly cycle can never overwrite.
- **A bank smaller than the slot count is flagged, not fatal.** The status bar tells you how
  many snapshots the bank actually has, and the rotation uses only those.
- **A slot switched on with no bank selected is flagged too.** Nothing can be saved, so the
  status bar says so until you pick a bank or switch the slot back off.

> **Before you change Backup Slot Count on a live system:** lowering it deletes the controls
> for the removed slots. Any schematic wiring on them is lost, and so are their saved
> timestamps — which is what the rotation uses to know where it is.

### Status bar

If you only want the schedules, the backup page costs you nothing and never warns about
itself: the slots ship off, so an untouched backup page reads OK.

| Colour | Meaning |
|---|---|
| Green | Running normally, or the backup page is untouched. |
| Orange | Something is set but cannot work: a slot is on with no bank selected, the bank cannot be reached, or it has fewer snapshots than **Backup Slot Count**. |
| Red | A save was attempted and failed. |

The status bar is one line and does not wrap, so it never includes your bank's name. Open the
Core's debug console for the detail — every status change logs a line there.

A bank you rename, delete, or set back to `None` is noticed at the next **Rescan** or Core
restart — the plugin does not re-scan the design every second. A restart keeps your selection
and flags it; only a **Rescan** clears it, because that is you asking what is really there.
The weekly save catches it either way: a save that cannot reach its bank turns the bar red.

---

## The source

`SimpleScheduler.qplug` is the plugin in readable form: plain Lua, and the same file Designer
loads if you import it directly instead of installing the `.qplugx`. That one file is the
whole program — read it, edit it, take what you want out of it.

It is generated, assembled from separate fragments in the maintainer's working repo, so the
comments in it are sparse by design. That changes nothing about your copy: it is ordinary Lua
and the MIT licence below covers it.

`SimpleScheduler.qplugx` is the same plugin packaged for double-click install. Q-SYS reads
either one; the `.qplugx` is only the more convenient way in.

---

## Licence

MIT — see [LICENSE](LICENSE). Use it, change it, ship it in your own work.

The Fresh AV Labs logo embedded in the plugin header is a trademark and is not covered by the
MIT grant. If you fork this and distribute your own build, replace the logo with your own.

## Support

This is a free tool published as-is. Bug reports and pull requests are welcome through GitHub
Issues, but there is no support commitment attached to it.
