# Tier 3 — Safety / Critical Overlay

For code where undefined behavior, memory safety, timing, or hardware control
actually matter: C/C++ firmware, robotics, flight-style software, and any Rust
that drops to `unsafe` or drives a device. Adopt these on top of
[Tier 2](./02-languages.md). For prototypes, apply the high-value subset
(bounded loops, explicit error handling, memory discipline, small functions,
static analysis, reproducible builds); move toward full compliance as code
approaches sensor/actuator-adjacent or unattended deployment.

> **JSF** here is **Joint Strike Fighter Air Vehicle C++**, not JavaServer Faces.

## Standards by use case

**C++ systems / embedded / robotics / flight-style**, in priority order:

1. [JSF AV C++](https://www.stroustrup.com/JSF-AV-rules.pdf) — restrict C++ to a
   safer, analyzable subset.
2. [MISRA C++](https://www.misra.org.uk/) — widely-adopted analyzable subset.
3. [NASA/JPL Power of Ten](https://spinroot.com/gerard/pdf/P10.pdf) — ten rules
   for reviewable, bounded code.
4. NPR 7150.2D — lifecycle/process discipline.
5. NASA-STD-8739.8 — software assurance mindset.

**C projects:** MISRA C → Power of Ten → NPR 7150.2D → NASA-STD-8739.8.

## The Power of Ten (daily driver)

```text
1.  No unbounded recursion.
2.  No unbounded loops — every loop has a statically provable upper bound.
3.  No dynamic allocation after initialization in critical paths.
4.  Keep functions small — one printed page; one responsibility.
5.  Assert liberally — aim for meaningful runtime checks on every function.
6.  Declare data at the smallest possible scope.
7.  Check every return value; check every parameter at entry.
8.  Limit the preprocessor to includes and simple macros.
9.  Restrict pointer use; no more than one level of dereference; no function
    pointers in critical paths.
10. Compile with all warnings on, zero warnings, plus static analysis.
```

## Additional constraints (C/C++)

```text
No goto / setjmp / longjmp.
Use explicit ownership; document who frees what.
Avoid exceptions in embedded / safety-critical paths.
Avoid RTTI and deep / virtual inheritance.
Prefer compile-time bounds and constants over runtime checks where possible.
Prefer static analysis + sanitizers + tests over review alone.
Document every deviation from the standard, with rationale and mitigation.
```

## Rust in this tier

Rust gives you most of the above for free — keep it that way:

- `#![forbid(unsafe_code)]` by default; if a crate genuinely needs `unsafe`,
  scope it to a small, audited module with `// SAFETY:` invariants on every block.
- Bound loops and channels; set explicit timeouts; avoid unbounded queues.
- Fuzz (`cargo fuzz`) and property-test (`proptest`) the `unsafe` and
  protocol-parsing surfaces.
- No `panic!`/`unwrap`/`expect` in device-control or unattended paths — return
  `Result` and handle deterministically.

## Operational discipline (unattended / device-adjacent)

```text
Bound all loops/retries; add explicit timeouts.
Validate all inputs; use typed config schemas.
Fail closed for auth / security / safety decisions.
Prefer immutable data; minimize global mutable state.
Structured logs + health checks + a rollback path.
Document operational assumptions and the threat model.
```

## Verification expectations

- Static analysis (clang-tidy/cppcheck/MISRA checker, Clippy) clean in CI.
- Sanitizer-enabled test builds (ASan/UBSan/TSan).
- Reproducible builds; pinned toolchains.
- Traceability for safety-relevant requirements (NPR 7150.2D-style): requirement
  → code → test, recorded in the repo.
