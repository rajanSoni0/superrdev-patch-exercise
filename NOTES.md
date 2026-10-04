# Patch Exercise Notes

## Summary of Changes
- **SQL precedence:** Unparenthesized `AND`/`OR` made the status filter
  ineffective and let archived tasks leak into results. I grouped the
  title/description match in the repository query, `search_tasks.sql`, and
  both Oracle queries (they mirror the app).
- **Artificial delay:** Removed `Thread.sleep()`. It blocked request threads
  and made short queries slowest, so responses could arrive out of order.
- **Frontend request lifecycle:** `useTasks` now ignores stale responses,
  resets `error` on each request, and clears `loading` on failure.
- **Pagination reset:** Page returns to 1 when the search or status changes.
- **Validation:** Invalid `status`, `page` and `pageSize` return 400 instead
  of 500. The offset uses `long` to avoid integer overflow on huge pages.

## What I Chose Not to Change
- **In-memory pagination:** moving it to the database is a larger change;
  I kept the diff small.
- **Debouncing:** the `ignore` flag already prevents wrong results, so
  debounce would only save network requests.
- **Low-impact items:** LIKE wildcard escaping, logger instead of
  `System.out.println`, hardcoded page size in `App.jsx`, and input
  validation in the Oracle procedure.
- **Package restructuring:** would bury the real fixes in a noisy diff.

## Biggest Remaining Risk
Pagination loads every matching row into memory, which will not scale.
A close second: the H2 console is enabled and `show-sql` is on, which
must not reach production.

## Tools / AI Used
I used Claude and ChatGPT to review the code and suggest candidate bugs.
[I ran the app and confirmed each failure myself, e.g. `?status=DONE`
returned all statuses before the fix and only DONE after.] I wrote and
tested the fixes myself. [I chose an `ignore` flag over `AbortController`
because ___.] [I rejected the suggestion to ___ because ___.]