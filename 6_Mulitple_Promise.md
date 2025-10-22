
# ⚡ Promise Combinators — Short Summary
# Note: use Settle not resolved

### 🧩 Common Foundation

* All take an **iterable (usually array)** of promises or values.
* Each value is wrapped with `Promise.resolve()` internally.
* They **cannot cancel** other promises once started.
* Order of results = **same as input order**, not completion order.

---

## ✅ Promise.all

* Waits for **all promises to fulfill**.
* Returns a **fulfilled array** of results when *every* promise succeeds.
* If **any promise rejects**, it immediately rejects (others still run but are ignored).
* 🕐 Result appears after the **slowest** promise finishes.
* ⚠️ Known as **“all or none”** or **“fail fast”**.

---

## 🧾 Promise.allSettled

* Waits for **all promises to settle** (fulfilled *or* rejected).
* Always **resolves** with an array of objects like:
  `{ status: 'fulfilled', value }` or `{ status: 'rejected', reason }`.
* Does **not reject**, even if some promises fail.
* 🕐 Completes after the **slowest** promise settles.

---

## 🏁 Promise.race

* Settles as soon as the **first promise settles** (fulfilled or rejected).
* Returns that promise’s value or rejection reason.
* 🏃‍♂️ Used to find *who finishes first*, success or failure.
* ⚠️ If first one fails → the whole race fails.
* If iterable is empty → stays pending forever.

---

## 🥇 Promise.any

* Resolves with the **first fulfilled** (successful) promise.
* Ignores rejections until one succeeds.
* If **all promises reject**, it rejects with an **AggregateError** containing all reasons.
* ⚡ Use when you want the *first success*, not the first settlement.

---

## 🧠 Quick Comparison Table

| Combinator     | Waits for       | Resolves With                     | Rejects When | Notes                           |
| -------------- | --------------- | --------------------------------- | ------------ | ------------------------------- |
| **all**        | All fulfilled   | Array of values                   | Any rejects  | Fail fast, all‑or‑none          |
| **allSettled** | All settled     | Array of `{status, value/reason}` | Never        | For inspection of all results   |
| **race**       | First settled   | Value or reason of first settled  | Same         | Fastest to settle wins          |
| **any**        | First fulfilled | First success value               | All reject   | AggregateError on total failure |

---

✅ **Summary Mnemonics**

* `all` → *All must succeed*.
* `allSettled` → *Just tell me everyone’s final status*.
* `race` → *First to finish (good or bad)*.
* `any` → *First success wins*.


---

# In depth -> Promise combinators — Deep, corner‑case focused explainer

---

## Quick roadmap (what you'll read)

1. Shared fundamentals (wrapping, thenables, timing, iterables)
2. `Promise.all` — complete breakdown + corners
3. `Promise.allSettled` — complete breakdown + corners
4. `Promise.race` — complete breakdown + corners
5. `Promise.any` — complete breakdown + corners
6. Other related behaviors (empty iterables, repeated promises, non‑iterables, errors thrown during iteration)
7. Performance, resource, and cancellation considerations
8. Notes on spec behavior, AggregateError, and cross‑engine consistency

---

# 1. Shared fundamentals

These points apply to every combinator and are necessary to understand the rest.

* **Every input is normalized**: combinators take an *iterable*. Each entry of that iterable is passed through `Promise.resolve(entry)` internally (or equivalent algorithm). That means:

  * If the entry is already a `Promise`, it is used as-is (but still treated as an entry in the input order).
  * If the entry is a **thenable** (object with `.then`), it will be *assimilated* — the engine will call its `then` and follow its eventual resolution/rejection (this can cause custom thenable behavior and synchronous exceptions inside the thenable). Assimilation happens per the microtask/then semantics, not synchronously returning the value.
  * If the entry is a plain value, it is treated like `Promise.resolve(value)` — i.e., it is considered already fulfilled (but callbacks for downstream `.then` run asynchronously as microtasks).

* **Microtask timing**: promise settlements and callbacks occur in microtasks (job queue). Even if a promise was resolved synchronously in its constructor, any `.then` or combinator reaction will happen after the current synchronous call completes. This affects "who is first" when multiple things resolve in the same tick — they are queued as microtasks and processed in queue order.

* **Order preservation in result arrays**: combinators that produce arrays (`all`, `allSettled`) preserve the *input iteration order* in their output arrays. That means results are placed in the output at the same index/position as their corresponding input entry, not based on completion time.

* **Firing vs waiting**: these combinators do **not** cancel or prevent the input promises (or thenables) from running. They merely observe. All input promises start (or continue) executing as normal; the combinator's returned promise controls only when an aggregated value or rejection is produced.

* **Synchronous errors from iterable**: if iterating the input iterable throws (for example, a getter on the iterable throws, or it's not actually an iterable), the combinator will either throw synchronously or return a rejected promise depending on the exact step defined by the spec. Practically, passing a non‑iterable to `Promise.all` will cause a `TypeError` to be thrown *synchronously*.

---

# 2. `Promise.all(iterable)` — deep semantics & corner cases

**Behavior summary (short)**: Returns a promise that fulfills when **all** input promises fulfill, with an array of fulfillment values in input order. If **any** input promise rejects, the returned promise rejects immediately with that rejection reason.

**Detailed corners & notes**:

* **Fulfillment array order**: values are placed at positions corresponding to iteration order, *even if promises resolve out of order*.

* **First rejection wins (timing)**: the returned promise rejects as soon as the first input promise rejects (i.e., the first rejection that becomes known to the combinator via the microtask queue). The rejection reason forwarded is exactly that promise's rejection reason. The combinator does not wait for other promises to settle once it has rejected.

* **What "immediately" means**: rejection happens in the microtask queue when that input promise settles. If several promises settle in the same microtask tick, the one whose reaction runs first determines the rejection — microtask queue ordering matters.

* **Non‑promise values / thenables**: non‑promises are treated as already fulfilled values. Thenables are assimilated — if a thenable's `then` synchronously throws, that will be treated as the rejection for the corresponding slot and may cause `Promise.all` to reject.

* **Empty iterable**: resolves immediately (as a resolved promise) with `[]`.

* **Non‑iterable input**: if the provided value is not iterable, the function throws a `TypeError` synchronously (i.e., before returning a promise), per the spec rules for argument type validation.

* **Repeated references**: if the same `Promise` object appears multiple times in the iterable, `Promise.all` will wait for that same promise just once but will place the same resolved value into every corresponding slot in the result array. The combinator does not deduplicate beyond this (it preserves positions).

* **Long‑lived / pending promises**: if any promise is pending forever (never resolves nor rejects), `Promise.all` will remain pending forever (unless one of the other promises rejects first). `Promise.all` cannot time out by itself.

* **Exceptions during setup**: if creating the array of "handlers" involves code that throws (e.g., a getter on an input object throws while iterating), that thrown exception will cause the returned promise to reject (or the constructor to throw) per the spec steps. In practice, treat iteration and assimilation as points where user code can cause immediate synchronous errors.

* **Memory behaviour**: `Promise.all` must hold references to all individual results until the whole array fulfills; that has memory implications for very large iterables.

---

# 3. `Promise.allSettled(iterable)` — deep semantics & corner cases

**Behavior summary (short)**: Waits for **all** input promises/values to settle (either fulfilled or rejected). Always fulfills with an array of outcome objects `{ status: 'fulfilled', value }` or `{ status: 'rejected', reason }`, in input order.

**Detailed corners & notes**:

* **Never rejects**: the returned promise always fulfills (never rejects) with the array of settlement objects.

* **Order preservation**: like `all`, the output array entries correspond to input iteration order.

* **Settling semantics**: `allSettled` waits for **every** promise to either fulfill or reject, even if some reject early. The returned array reflects every final state.

* **Assimilation & synchronous exceptions**: thenable assimilation rules apply. If assimilating a thenable throws synchronously, that counts as a rejection for that input's slot; `allSettled` will still wait for the rest.

* **Empty iterable**: resolves immediately with `[]`.

* **Non‑iterable input**: same as `all` — will throw `TypeError` synchronously.

* **Performance & memory**: because `allSettled` waits for every item, it is useful when you need the final status of every promise, but it also means potentially long waits and retained memory if some promises are long‑running or never settle.

---

# 4. `Promise.race(iterable)` — deep semantics & corner cases

**Behavior summary (short)**: Returns a promise that settles (fulfills or rejects) with the **first** input promise/value that *settles* (i.e., is either fulfilled or rejected). The returned value or rejection reason is forwarded exactly from that settled promise.

**Detailed corners & notes**:

* **First settled, not first fulfilled**: `race` is based on *settlement* (fulfilled or rejected). If the first promise to settle is rejected, the `race` result is rejection.

* **Order and timing**: if multiple promises settle in the same microtask tick, microtask ordering determines which reaction runs first and thus which result `race` uses.

* **Empty iterable**: **does not resolve or reject** — `Promise.race([])` returns a promise that remains *pending forever* because there is no participant to settle it.

* **Non‑promise values**: immediate values are treated as already fulfilled entries. If the iterable contains an immediate value, `race` will settle (fulfill) with that value after the current microtask tick. If an entry is a thenable, it will be assimilated and may settle `race` corresponding to that thenable's eventual settlement.

* **Side effects**: like other combinators, inputs already started will continue; `race` gives you the earliest settlement but cannot stop other promises from continuing or producing side effects.

* **Use cases caution**: `race` is commonly (but sometimes mistakenly) used to implement timeouts by racing a real operation against a timeout promise. This pattern works to detect timeout but does **not** cancel the original operation — you must use explicit cancellation (e.g., `AbortController`) if cancellation is required.

---

# 5. `Promise.any(iterable)` — deep semantics & corner cases

**Behavior summary (short)**: Returns a promise that fulfills with the **first** successfully fulfilled input promise/value. Rejections are ignored until either a fulfillment occurs or **all** inputs have rejected — if every input rejects, `Promise.any` rejects with an `AggregateError` containing all rejection reasons.

**Detailed corners & notes**:

* **Waits for first success**: unlike `race`, `any` ignores rejections unless every input has rejected.

* **AggregateError on total failure**: when no input fulfills (i.e., all reject), `any` returns a rejected promise whose rejection is an `AggregateError`. That error wraps a list/array of the individual rejection reasons. The `AggregateError` type also has the `.errors` property in many engines (an array of the collected reasons).

* **Empty iterable**: calling `Promise.any([])` causes the returned promise to reject immediately with an `AggregateError` (there are no candidates to fulfill). The `AggregateError` will have an empty list of reasons in this case.

* **Assimilation of thenables**: thenable entries are assimilated and their rejections or fulfillments are considered normally. A thenable that synchronously throws during assimilation will count as a rejection for purposes of `any`.

* **Order of errors in AggregateError**: the spec requires collection of rejection reasons, but the exact ordering semantics are implementation‑sensitive; in practice engines collect reasons corresponding to the order they were processed. Do not rely on a strict order unless you inspect the engine’s behavior.

* **Memory and time considerations**: like `allSettled`, `any` must observe rejections from all inputs (if no success arrives). This means in failing cases it must keep track of each rejection reason until it can make the final `AggregateError`.

---

# 6. Other behavior notes (common pitfalls)

* **Synchronous thrown exceptions vs promise rejection**: if during the process of iterating or assimilating an entry an exception is thrown synchronously (for example, a property access throws or a thenable’s `then` throws before it yields), that counts as a rejection reason for that input. For `all`, it will likely cause immediate rejection; for `any`, it will be treated as one of the rejections.

* **Input that is not an iterable**: typically throws `TypeError` synchronously (rather than returning a rejected promise) — this is a common source of confusion.

* **Microtask ordering matters**: if multiple inputs settle "at the same time", the microtask queue ordering (and the internal order in which the combinator attached handlers) determines which settlement is observed first.

* **Thenable corner cases**: malicious or buggy thenables may call resolve/reject more than once, or call callbacks synchronously. Because combinators **assimilate** thenables, those behaviors can affect the combinator’s result. Engines follow the Promises/A+ / ECMAScript spec to ensure a thenable’s multiple calls only settle the assimilated promise once; however, if a thenable throws while being assimilated, that will be treated as a rejection.

* **Promises that settle synchronously in constructor**: even if a promise's executor calls `resolve` immediately, any `.then` reactions still run asynchronously. Combinators will treat those as microtask-settled values, not immediate synchronous returns.

---

# 7. Performance, cancellation, and resource management

* **No implicit cancellation**: none of the built‑in combinators cancels or stops other promises once the resulting promise settles. If you need cancellation or cleanup, you must design it explicitly (e.g., cooperative cancellation with `AbortController`, custom cancellation tokens, or explicit cleanup calls in your promise executors).

* **Memory footprint**: combinators like `all` and `allSettled` inherently hold references to all individual results/handlers until completion. For very large iterables that can mean high memory use. `race` and `any` can sometimes short‑circuit early (reducing wall‑clock wait time) but still cannot stop other started operations.

* **Timeout patterns**: racing an operation against a timeout promise is a common pattern, but remember: the real operation continues running unless you add cancellation mechanisms.

* **Parallelism vs sequencing**: combinators are observers — they do not change whether operations run concurrently or sequentially. The concurrency is determined by how the underlying promises are created.

---

# 8. Spec, AggregateError, and engine consistency

* **AggregateError**: introduced for `Promise.any` to report multiple failures. Its shape and properties are standardized but engines might differ slightly on the `.message` content. The `.errors` property (array of individual reasons) is the reliable API across modern engines.

* **Cross‑engine subtle differences**: modern browsers and Node.js follow the ECMAScript spec closely. Subtle differences can appear with thenables that do surprising synchronous work; engines aim to process assimilation deterministically, but microtask queue specifics and ordering may vary in edge micro‑timing cases.

---

# Visual timeline cheat‑sheet (ASCII)

Legend: each `●` is a promise, `t=…` are times; arrows show the combinator’s decision point.

```
Time →
Promises:   p1(2s)    p2(3s)    p3(1s)
Settled:     ●(2s)     ●(3s)     ●(1s)

Promise.all: waits until 3s (max) then fulfills with [v1, v2, v3]
Promise.allSettled: waits until 3s then fulfills with [{status, value}, ...]
Promise.race: settles at 1s with p3's result (fulfilled or rejected)
Promise.any: settles at first *fulfillment* (if p3 rejected at 1s and p1 fulfilled at 2s, any resolves at 2s)
```

Edge case timeline (one rejects early):

```
If p3 rejects at 1s:
- all: rejects at 1s with p3's rejection reason
- allSettled: still waits until all have settled (2s/3s)
- race: rejects at 1s (because p3 settled first and it was a rejection)
- any: ignores p3's rejection and waits for next fulfillment; if none fulfill, rejects with AggregateError when all have rejected
```

---

# Final practical checklist (quick mental model)

* Use **`all`** when you need *every* result and want failure fast.
* Use **`allSettled`** when you must know *every* outcome regardless of success/failure.
* Use **`race`** when you only care about the *first settlement* (success or failure) — great for *who finishes first*, not who succeeds first.
* Use **`any`** when you care about *first success*, and you are willing to handle an aggregated failure if everything fails.

---

If you want, I can now add **annotated diagrams**, **minimal timing visuals**, or a short **table comparing the behaviors** — or follow up with concrete, focused examples for each combinator (or tests showing edge cases).
