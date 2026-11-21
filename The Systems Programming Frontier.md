#by/claude

Right now the frontier work falls into four areas: memory safety, deterministic testing, making the kernel programmable, and getting more out of hardware. The best practices come out of the same places.

**What people are working on**

- **Memory safety.** Linux 7.0 officially ended the "Rust experiment." Upstream kernel developers now accept that Rust is here to stay. The next step is building the kernel with only stable Rust features. C++ is moving too: on a panel with Herb Sutter, speakers said C++26 features move C++ toward a more declarative style of systems programming without giving up low-level control. Zig is the third contender, and it's behind Bun, which Anthropic acquired.
- **Deterministic simulation testing (DST).** The production code runs inside a simulated world where the clock, network and disk are faked, so every failure can be replayed. In TigerBeetle's simulator, one minute of simulator time equals days of real-world testing, and any bug reproduces from a seed plus the Git commit. Antithesis takes a different route: a deterministic hypervisor that brings DST to systems never designed for it.
- **A programmable kernel.** sched_ext lets you write CPU schedulers in BPF and load them at runtime. Meta wrote one tuned to its ads workload and got a 28% cut in p99 latency and 3.28 MW of power savings.
- **The hardware gap** (from my general knowledge, not the searches): io_uring and kernel-bypass I/O, GPU and inference kernels, and CXL and memory tiering. The common driver is that the hardware has outrun the old operating-system abstractions.

**Best practices on the edge**

- **Design for determinism from the start.** Put time, randomness and I/O behind interfaces you can swap out. Adding this later is very expensive.
- **Test invariants, not examples.** Check safety and liveness properties under injected faults, and keep fuzzing for long stretches.
- **Fence off unsafe code.** Keep `unsafe` code in small, audited modules with safe wrappers around them. Rust for Linux shows the payoff: compartmentalizing unsafe code reduces critical errors and improves the C side's APIs too.
- **Tune to the workload.** Encode what you know about the workload into policy, like Meta's scheduler did, instead of trusting general-purpose defaults.
- **Measure before optimizing.** Profile first, look at tail latency rather than averages, and reduce variance in your benchmarks. TigerBeetle even runs deterministic performance tests that remove hardware variance.
- **Keep the tooling boring.** Pin toolchains, keep builds reproducible and leave CI uneventful.

If you're coming from C++, two good starting points are TigerBeetle's DST writeups and the C++26 features: contracts, reflection, and std::execution for structured concurrency.
