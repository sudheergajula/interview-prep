## 🔥 Hot shards in Databases

## When does this happen?
Typically, when there are lot number of traffic is redirected to few specific shards, this can lead to hot spots in the cluster.
These issues are prone to happen when we hit into celebrity problem: i.e. when a celebrity posts an image or note on their handle.

→ Memory spikes <br>
→ Latencies bumps from 200ms to 2 secs <br>
→ DB queries slow down on their executions <br>

## 🔎 Detection strategies
### Entropy calculation
Assume that we have 2 nodes in a DB cluster and each consuming load of 60:40.

Entropy is calculated as follows:

H(X) = -\sum_{i=1}^{n} p(x_i) \log_b p(x_i)
Entropy less than 0.7 is considered indicate skewness
```python
import math

def entropy(probs, base=2):
    """
    Compute Shannon entropy H = -Σ p * log_b(p).
    probs: list of probabilities summing to 1
    base: log base (2 for bits, math.e for nats, etc.)
    """
    H = 0.0
    for p in probs:
        if p > 0:
            H -= p * math.log(p, base)
    return H

print(entropy([0.5, 0.5]))        # → 1.0
print(entropy([0.7, 0.3]))        # → ≈0.8816

```
## Temporal Based detection
In this method, if there is going to be hot shard it is detecetd at least 30 seconds before with help of Data collection
and metrics collection through agents on those servers.

## ☢️ Mitigation Plan
- **Load Shredding** - Shred/ Drop these requests at LB
- **Rate limiting** - Token/Leaky bucket based rate limitter
- **Circuit breaker** - Half open/OPEN/CLOSED strategy
- **Adaptive Partitioning** - 
- **Dynamic Key range mapping** - Dynamically adjust key ranges
- **Hot key replication** - Replicate hot keys to read only shards


