# Patch Exercise Notes

## Summary of Changes

I focused on fixing correctness, reliability, and API behavior without rewriting the application.

I fixed the task search SQL precedence issue so archived tasks are excluded and the status filter is applied correctly to both title and description searches. I also updated the H2 and Oracle reference SQL to keep the same logic.

I fixed the frontend pagination behavior so changing the search query or status resets the page to 1.

I removed an artificial `Thread.sleep()` from the task API that unnecessarily delayed requests and could cause requests to complete out of order.

I improved the frontend request lifecycle by resetting errors, correctly clearing the loading state on failures, and preventing stale requests from overwriting newer results.

I added validation for `status`, `page`, and `pageSize` so invalid client input returns HTTP 400 instead of causing server errors. I also changed the pagination offset calculation to use `long` to avoid integer overflow for very large page values.

## What I Chose Not to Change

I did not rewrite the pagination/database implementation or make broader architectural changes because the exercise is timeboxed. I also left smaller issues such as search debouncing, LIKE wildcard escaping, and logging cleanup unchanged.

## Biggest Remaining Risk

Pagination is still performed in memory after retrieving all matching tasks. This could become a performance problem as the dataset grows. A production implementation should move pagination into the database query.

## Tools / AI Used

I used ChatGPT and Claude to help inspect the code, identify potential bugs, reason about fixes, and review the changes. I reproduced issues locally, tested the fixes, and made the final code changes myself.