# FAQ

### What does "provably fair" actually mean here?
Each **Wheel** result is a pure cryptographic function of a **server seed** (which we commit to *before*
you bet), your **client seed**, and a **nonce**, mapped by the game's **published odds**. Because the
commitment is published first, we cannot change the outcome afterward. You can recompute every result
yourself — see [Verifying Your Bets](Verifying-Your-Bets).

### Why is the server seed hidden at first?
If we revealed it up front, every future result would be predictable. Instead we publish
`SHA-256(serverSeed)` (the commitment). When you **Rotate**, the raw seed is revealed so you can verify
the whole history — and the hash proves we never swapped it.

### Can maczo change my result?
No. The server seed is fixed and its hash is published before you bet. Changing it would break
`SHA-256(serverSeed) == commitment`. The nonce is a server-owned counter that only increases, so no bet
can be replayed.

### Do I have to trust maczo's website to verify?
No. The browser verifier runs entirely **offline** — view source and confirm it makes no network calls.
Or run the Node CLI (`npm test`). Or re-implement the [algorithm](Algorithm) in any language.

### What are the "odds" / the paytable?
`paytable.json` publishes exactly what maps the random words to a Wheel outcome (multiplier tables /
weights / thresholds, keyed by your bet inputs where the game is parameterized). The resolver only
*indexes into* it, so the odds you see are the odds that settled your bet.

### What is the bias class?
Wheel is **`modulo`** — reduces a uint32 by modulo, carrying a documented-negligible bias (< ~1e-5). Every game documents this so you know exactly how its words become an
outcome.

### My verification failed — what now?
Double-check you used the **revealed** server seed (after Rotate), the exact client seed and nonce shown
for that bet, and the same bet inputs. The math is deterministic, so a correct input always reproduces the
published result.

### Is this a standard provably-fair scheme?
Yes — it is the widely used HMAC-SHA256 commit-reveal scheme. The implementation is open source (MIT) so
anyone can audit it.
