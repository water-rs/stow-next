# stow-next

A long-term fork of cargo that builds [stow](https://github.com/water-rs/stow)'s
cache into cargo's own pipeline.

## Why stow-next

stow reaches a build from outside cargo, as a rustc wrapper. That works with
whatever cargo a user already has, and it stays the way stow builds: the
wrapper CLI keeps being maintained. But a wrapper only sees rustc invocations,
and some of the remaining cost comes from decisions only cargo makes:

- Cargo compiles and runs every build script itself. When stow serves a crate
  from its cache, the build script still runs once, because running it never
  goes through rustc.
- Cargo decides whether a unit is fresh from its own fingerprints, an internal
  format that changes between cargo versions. stow can answer cargo's rustc
  call, but it cannot tell cargo a unit is already done.
- Cargo still compiles the host-side dependencies of a proc macro stow serves,
  though nothing reads them.

## Why fork cargo

A cargo that stow ships could mark a cached unit, its build-script run
included, as done; skip units nothing reads; choose the host-side profile per
unit; and sandbox each unit. It keeps cargo's semantics (feature resolution,
config precedence, subcommands, the build-script protocol) without
reimplementing them.

Depending on cargo as a library is not enough: its `Executor` hook intercepts
rustc invocations only, while build-script execution and fingerprinting are
private to cargo.

The cost is a patch set rebased onto every cargo release, and shipping a cargo
no older than the newest stable a user's project needs. stow-next has a lower
maintenance priority than stow.

The plan and its open questions: [#1](https://github.com/water-rs/stow-next/issues/1).

## License

MIT, as [stow](https://github.com/water-rs/stow).
