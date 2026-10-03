# JVM Concurrency Benchmarks — JMH results for lock contention, lock-free hand-off and allocation in Java 21

> Open-source JMH microbenchmarks by Ansh Saxena (Backend & ML Infrastructure Engineer at Cyware Labs, Bengaluru): what locks and atomics cost as threads are added, how much faster a hand-written lock-free SPSC ring buffer is than the JDK queues, and what one allocation per event costs under G1, Parallel and ZGC.

Canonical page: https://anshsaxena05.github.io/projects/jvm-concurrency-benchmarks.html
Author: [Ansh Saxena](https://anshsaxena05.github.io/) (GitHub: AnshSaxena05)
Source code: https://github.com/AnshSaxena05/jvm-concurrency-benchmarks

Java 21, JMH 1.37, Maven, MIT licensed. Measured on an Intel Core i7-1255U laptop (10 cores, 12 threads, hybrid P/E cores, 15.7 GB RAM), Windows 11, Microsoft OpenJDK 21.0.12, on 2026-10-02, with no core pinning or frequency control: the ratios are the finding, so re-run on your own hardware.

## 1. Lock contention: shared counter (operations per microsecond, higher is better)

| Primitive | 1 thread | 4 threads | 8 threads |
|---|---:|---:|---:|
| `AtomicLong.incrementAndGet` | 152.3 ± 11.3 | 27.7 ± 4.4 | 22.8 ± 2.9 |
| `LongAdder.increment` | 91.9 ± 1.7 | 276.4 ± 49.5 | 439.9 ± 22.1 |
| `ReentrantLock` | 51.4 ± 2.8 | 28.6 ± 3.7 | 28.1 ± 4.3 |
| `synchronized` | 40.4 ± 2.8 | 13.3 ± 1.6 | 12.5 ± 1.5 |

Uncontended, a single CAS wins. As threads pile onto one cache line `AtomicLong` collapses (about 7x slower at 8 threads) while `LongAdder`, which stripes the count across cells, scales up (about 19x faster than `AtomicLong` at 8 threads). `LongAdder.sum()` is not an atomic snapshot, so it fits metrics, not sequence numbers.

## 2. Lock-free hand-off: SPSC ring buffer against JDK queues (hand-offs per microsecond)

| Queue | Throughput |
|---|---:|
| `SpscRingBuffer` (lock-free) | 77.4 ± 16.2 |
| `ConcurrentLinkedQueue` | 26.9 ± 3.9 |
| `ArrayBlockingQueue` | 10.8 ± 3.4 |
| `LinkedBlockingQueue` | 8.9 ± 2.4 |

With exactly one writer and one reader, dropping the lock and the CAS loop gives roughly 3x over the best general-purpose queue and 7x to 9x over the blocking ones. Both sides spin, so this measures the hand-off itself, not park/unpark latency. A test pushes 2,000,000 items across two threads and checks order and sum.

## 3. Allocation on the hot path (`-prof gc`, average time per operation and GC cycles in 6 s)

| Benchmark | G1 | Parallel | ZGC | Bytes/op |
|---|---:|---:|---:|---:|
| `newTickPerEvent` | 5.2 ns, 74 GCs | 5.2 ns, 137 GCs | 6.8 ns, 48 GCs | 40 |
| `reusedTick` | 1.1 ns, 0 GCs | 1.1 ns, 0 GCs | 1.5 ns, 0 GCs | 0 |
| `boxedLadderUpdate` (`HashMap<Long,Long>.merge`) | 17.5 ns, 38 GCs | 11.9 ns, 42 GCs | 20.1 ns, 16 GCs | 45 |
| `primitiveLadderUpdate` (`long[]`) | 1.5 ns, 0 GCs | 1.5 ns, 0 GCs | 1.8 ns, 0 GCs | 0 |

Reusing one mutable event removes 40 bytes of garbage per tick and about 5x of the time; a `long[]` ladder removes 45 bytes and about 8x to 12x against a boxed map. With zero allocation there is nothing for any collector to do.

### Two benchmarking lessons

- **Escape analysis can hide allocation.** The first `newTickPerEvent` did not hand the object to the `Blackhole`, the JIT scalar-replaced it, and the benchmark reported 0 bytes per operation. Now each event escapes through `Blackhole.consume`: 40 bytes.
- **Sample-mode percentiles are timer-dominated at nanosecond scale.** The tables use `AverageTime`.

## Links

- Source code, test and raw JMH output: https://github.com/AnshSaxena05/jvm-concurrency-benchmarks
- [More about the author](https://anshsaxena05.github.io/index.html.md)
- [SOC Triage Agent](https://anshsaxena05.github.io/projects/soc-triage-agent.html.md)
