# k6-lab

## Part 0
- Step 0.1, http_req_duration: 263.79 ms
- Step 0.2, requests, rate and p(95), and why the rate stays below 5:
  75 requests, 3.580269 requests/s, p(95) = 461.1 ms.
  The rate stays below 5 because each virtual user sleeps for 1 second after each request, so it cannot send a new request immediately.
- Step 0.3: No written response required.
- Step 0.4: No written response required. all three checks pass.