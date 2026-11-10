# #4471 split — S01E03 "what it shows"

The support desk merged 412 unique reports about dates into ticket #4471 and marked it known
as one parser issue. This repo holds the kb export and the split of #4471 into the problems it
really holds.

## The split

| Cause | Reports | Tenants | Ticket |
|---|---|---|---|
| `parser` | 379 | 11, day-first, app + email + booking-v2 forms | [#1](https://github.com/yushanwebdev/mega-s01e03-what-it-shows/issues/1) |
| `hint` | 33 | alder.physio, copper.studio, pale.harbour, booking-v3 form | [#2](https://github.com/yushanwebdev/mega-s01e03-what-it-shows/issues/2) |

- 427 export rows, **412 unique report IDs**. Every unique report belongs to #4471.
- `parser`: the shared parser reads dates month-first; these tenants write day-first.
- `hint`: the booking-v3 form prints `MM/DD/YYYY` but reads the date day-first. The parser fix
  does not touch it.
- Intake tags are guesses: 23 `parser` reports are tagged `hint`, and 15 `hint` reports are
  tagged `parser`.
- Five `parser` reports read as a one-day move (`R-41202`, `R-41209`, `R-41236`, `R-41344`,
  `R-41620`). The desk counts them under `parser` and footnotes them.

## Resolution

The desk closed #4471 on 05 Oct. `parser` is fixed (`fix(dates): parse dd/mm for eu locales`);
`hint` won't fix because booking-v3 is retired. Both issues are closed.

## What is here

```
support-kb/                         the kb export from the Challenge page
  src/lib/data/                     reports, desk notes, tenants
  src/lib/kb/causes.ts              the grouping logic this work runs
  src/lib/kb/SplitTable.svelte      the table on the new page
  src/routes/kb/incidents/4471-split/+page.svx   the new page, signed
rendered/4471-split.html            the page as the desk will see it
```

The page's frontmatter carries the course `signature:` and it ends with
`<RenderStamp {signature} />`, per the kb README. The rendered page shows desk ref
`DR-G3DJ-4GAX`.

## Reproduce

The counts come from `causes.ts`:

1. `uniqueById` removes the overlapping export rows.
2. `groupByCause` groups the unique reports by `causeOf(report, tenant)`.
3. `causeOf` uses the tenant's locale (day-first or not) and booking form:
   `form` + `booking-v3` is `hint`; everything else for a day-first tenant is `parser`.
