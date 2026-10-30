# k6-lab

## Part 0
- Step 0.1, http_req_duration: 263.79 ms
- Step 0.2, requests, rate and p(95), and why the rate stays below 5:
  75 requests, 3.580269 requests/s, p(95) = 461.1 ms.
  The rate stays below 5 because each virtual user sleeps for 1 second after each request, so it cannot send a new request immediately.
- Step 0.3: No written response required.
- Step 0.4: No written response required. all three checks pass.
- Step 0.5: No written response required.
- Step 0.6, exit code of the normal run and of the run with P95=300:
  Normal run: 0
  P95=300 run: 99
- Step 0.7, rules that passed, p(95), p(99), exit code after your change:
  Normal run:
  - p(95) = 380.73 ms
  - p(99) = 439.68 ms
  - Passed rules: All thresholds passed.
  - Failed rules: None.
    After changing P95 to 1 ms:
  - p(95) = 414.98 ms
  - p(95) threshold failed.
  - p(99) and other thresholds passed.
  - Exit code = 99.
- Step 0.8, what was wrong, and how the checks showed it:
  The request used lowercase ingredient and tool names ('pepperoni' and 'knife'), but the API expected the exact names ('Pepperoni' and 'Knife'). This caused some checks to fail. After correcting the names, all checks passed with 100% success rate.

## Part A
| Step | Test Type | Users | Req/s | p(95) | Max | Failed | Checks |
|------|-----------|-------|-------|-------|-----|--------|--------|
| A1 | Smoke | 1 | 0.471445 | 427.42 ms | 460.57 ms | 0.00% | 100% |
| A2 | Average Load | 3 | 1.26 | 385 ms | 438 ms | 0.00% | 100% |
| A3 | Stress | 5 | 2.127175 | 365.34 ms | 385.24 ms | 0.00% | 100% |
| A4 | Spike | 5 | 1.057408 | 373.18 ms | 520.13 ms | 0.00% | 100% |

- A4, spike moment and recovery time:
  The spike occurred around 13:32:50–13:33:25 when the number of virtual users increased from 1 to 5. The response time increased during the spike, reaching approximately 489 ms. After the load decreased back to 1 user, the response time returned close to its earlier level within approximately 10–20 seconds.