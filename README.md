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
| A5 | Soak | 2 | 0.995917 | 375.44 ms | 1.03 s | 0.00% | 100% |

- A4, spike moment and recovery time:
  The spike occurred around 13:32:50–13:33:25 when the number of virtual users increased from 1 to 5. The response time increased during the spike, reaching approximately 489 ms. After the load decreased back to 1 user, the response time returned close to its earlier level within approximately 10–20 seconds.
- A5, soak test:
  The response time remained relatively stable throughout the test. There was a brief spike at the beginning, but it returned to around 300–400 ms. The error rate remained at 0%, so neither response time nor error rate got worse over time.

## Part B: Reflection

### 1. Results table for Steps A1 to A5

| Step | Test Type | Users | Req/s | p(95) | Max | Failed | Checks |
|---|---|---:|---:|---:|---:|---:|---:|
| A1 | Smoke | 1 | 0.471445 | 427.42 ms | 460.57 ms | 0.00% | 100% |
| A2 | Average Load | 3 | 1.26 | 385 ms | 438 ms | 0.00% | 100% |
| A3 | Stress | 5 | 2.127175 | 365.34 ms | 385.24 ms | 0.00% | 100% |
| A4 | Spike | 5 | 1.057408 | 373.18 ms | 520.13 ms | 0.00% | 100% |
| A5 | Soak | 2 | 0.995917 | 375.44 ms | 1.03 s | 0.00% | 100% |

### 2. Why was the request rate below 5 requests per second with 5 virtual users in Step 0.2?

The request rate stayed below 5 requests per second because each virtual user sleeps for 1 second after each request. Therefore, users cannot send requests continuously without waiting.

### 3. Did the response time grow from the average-load test to the stress test? Explain your answer with the operational envelope.

No, the response time did not grow. The p(95) decreased from 385 ms in the average-load test (3 users) to 365.34 ms in the stress test (5 users).

Both tests passed all thresholds with 0% failed requests. This means the system remained within its tested operational envelope of up to 5 virtual users.

### 4. In the spike test, how long did the response time take to return to normal after the spike?

Yes, the response time increased during the spike, reaching approximately 480 ms. After the number of users decreased from 5 to 1, the response time returned close to its earlier level (around 300–350 ms) within approximately 10–20 seconds.

### 5. In the soak test, did the response time or the error rate get worse over time? Name one kind of defect that a soak test of several hours could reveal.

No, neither the response time nor the error rate got worse over time. The response time remained relatively stable at around 300–400 ms, and the error rate stayed at 0%.

A soak test lasting several hours could reveal a memory leak.

### 6. Why do the thresholds in this lab use p(95) rather than the average?

The p(95) measures the response time experienced by 95% of requests. It helps identify slow requests that the average might hide. Therefore, it gives a better understanding of the user experience.

### 7. Your results come from a shared public site reached over the internet. Give two reasons why the same tests against a system in a lab could give different numbers.

1. Network conditions: Internet latency and network traffic can affect response times.
2. Server environment: Different hardware, resources, and server configurations can produce different performance results.

### 8. Why must you never run a stress, spike or breakpoint test with hundreds of users against a site that you do not own?

Because a large number of requests could overload the server, interrupt services, and affect other users. It could also be considered an unauthorized denial-of-service attack.

### 9. Write one vague performance requirement for an app you use every day. Rewrite it as a testable requirement, and show the K6 thresholds that check it.

**Vague requirement:**

The application should load quickly.

**Testable requirement:**

The home page must respond within 1 second for at least 95% of requests when tested with 10 virtual users for 5 minutes. The failed request rate must be below 1%.

**K6 thresholds:**

```javascript
export const options = {
  vus: 10,
  duration: '5m',
  thresholds: {
    http_req_duration: ['p(95)<1000'],
    http_req_failed: ['rate<0.01'],
    checks: ['rate>0.99'],
  },
};
```

These thresholds ensure that the application responds quickly and has a low failure rate under the specified load.