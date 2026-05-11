# Vendored BLAKE3 reference C implementation

Source of these files: https://github.com/hanzoai/blake3 (Hanzo's
canonical fork of [BLAKE3-team/BLAKE3](https://github.com/BLAKE3-team/BLAKE3)).

The brain runtimes all use BLAKE3 for wallet-style content-addressable
ids. Hanzo owns the fork so we can ship signed releases, pin compiler
flags, and audit changes:

| Runtime | Hanzo dep |
|---|---|
| C++ | `third_party/blake3/` (this directory; tracked from `hanzoai/blake3`) |
| Rust (node + mcp) | `blake3` crate (upstream binary; hanzoai/blake3 holds source) |
| Go | `lukechampine.com/blake3` (upstream pure-Go impl; mirror at [`hanzoai/blake3-go`](https://github.com/hanzoai/blake3-go)) |
| Python | `blake3` pip (oconnor663/blake3-py; mirror at [`hanzoai/blake3-py`](https://github.com/hanzoai/blake3-py)) |
| TypeScript | `@noble/hashes/blake3` (depended directly; vetted upstream) |

To update the vendored C copy:

```bash
git clone https://github.com/hanzoai/blake3.git /tmp/hanzoai-blake3
cp /tmp/hanzoai-blake3/c/blake3.h          third_party/blake3/blake3.h
cp /tmp/hanzoai-blake3/c/blake3_impl.h     third_party/blake3/blake3_impl.h
cp /tmp/hanzoai-blake3/c/blake3.c          third_party/blake3/blake3.c
cp /tmp/hanzoai-blake3/c/blake3_dispatch.c third_party/blake3/blake3_dispatch.c
cp /tmp/hanzoai-blake3/c/blake3_portable.c third_party/blake3/blake3_portable.c
cp /tmp/hanzoai-blake3/c/blake3_neon.c     third_party/blake3/blake3_neon.c
```

Stay on the portable + NEON files only — the SSE / AVX dispatch is
auto-selected at runtime by `blake3_dispatch.c` but we don't compile the
SIMD .c files (saves ~3 MB of binary on x86 without measurable hash-rate
loss for our usage pattern, where inputs are tiny: 32-byte public keys
and 21-byte versioned hashes).

Upstream: CC0 1.0 Universal / Apache 2.0.
