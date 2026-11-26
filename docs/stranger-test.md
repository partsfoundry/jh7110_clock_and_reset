# The stranger cadence

The release's whole claim is that a stranger, given this repository and nothing else, can build
the crate and run its proofs from the README alone. A real agent runs that check on a
cadence, and the weekly `stranger cadence` job fails when the
last run is past 28 days.

What a run is:

1. Clone the repository fresh.
2. Follow the README and nothing else: build, test, and run the proofs.
   The README must name the build and test commands (with any feature a test target needs), the
   bare-metal build with `rustup target add`, the Kani install before the first `cargo kani`, how
   long the proofs take and how to run one harness, what a passing run prints that looks alarming,
   how to run and revert one falsification patch, and how to check the minimum Rust version.
   Each one it lacks is a finding.
3. Note every place the README lied, omitted, or assumed. Those are findings against the README,
   not against the stranger.
4. Record the date on the line below.

Last run: 2026-11-26

## What the last run did

A fresh clone and the README, with empty Cargo, rustup and home directories, on a shared 80-core
x86_64 Linux host. Rust came from rustup.rs, and the run followed "Build, test and prove" and its
subsections command by command.

- `cargo build` and `cargo test` pass: 51 unit tests and 2 doctests. `cargo build --lib` passes for
  `riscv64imac-unknown-none-elf`, `aarch64-unknown-none-softfloat` and `x86_64-unknown-none`.
- The Kani install took 4 seconds and setup 22. Kani then held 485 megabytes in `~/.kani` and its
  nightly toolchain 1.5 gigabytes in `~/.rustup`.
- `cargo kani` ends with `Complete - 10 successfully verified harnesses, 0 failures, 10 total.` and
  exits 0, in 2 minutes 50 seconds at a peak of 892 megabytes. One harness by its full path takes
  about one second, and Kani refuses a bare name.
- Each of the ten falsification patches applies with `git apply -p1` and no offset, turns its
  harness red at the assertion its opening lines name, and exits 1. `git checkout -- src` leaves
  `git status` clean after each.
- A scratch crate that depends on this repository by `git` and `rev` builds and walks
  `ENTROPY_BRING_UP`.
- At Rust 1.88, the stated minimum, the tests and the three bare-metal builds pass.

The run found seven things.

1. `cargo kani` printed a future-incompatibility warning, `trailing semicolon in macro used in
   expression position`, for two proof arms that end in `assert!`. The arms are now blocks, and the
   warning is gone.
2. The README did not say which lines of a passing proof run look alarming and mean nothing. It
   now lists them.
3. The README did not say how to get the source, or that the host tests need a C linker. It now
   gives the clone and every build command.
4. The README did not say what the Kani install downloads or holds on disk. It now does.
5. The README gave one proof time, about 5 minutes. This host took under 3, so it now gives a range
   and names the three harnesses that take the time.
6. The README did not say what a failing falsification run prints or that it exits 1. It now does.
7. The README named the minimum Rust version without saying how to check it, and did not say how
   to depend on an unpublished crate. It now has both.

Result: pass on the build, the tests and the proofs, with six omissions in the README and one
warning from the code. This version corrects each of them, and a second fresh clone of it followed
the README to the same results with neither.
