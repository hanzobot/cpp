# Vendored BLAKE3 reference C implementation

Source: https://github.com/luxfi/blake3 — luxfi's canonical fork of
[BLAKE3-team/BLAKE3](https://github.com/BLAKE3-team/BLAKE3).

The brain runtimes use BLAKE3 for wallet-style content-addressable ids.
All cryptographic primitives sit under the `luxfi` org because Lux is
the canonical chain / crypto stack across Hanzo, Zoo, and the rest of
the ecosystem:

| Runtime | Dep |
|---|---|
| C++ | `third_party/blake3/` (this directory; tracked from `luxfi/blake3`) |
| Rust (node + mcp) | `blake3` crate ([`luxfi/blake3`](https://github.com/luxfi/blake3) is the canonical source) |
| Go | [`github.com/luxfi/crypto/blake3`](https://github.com/luxfi/crypto/tree/main/blake3) (wraps zeebo/blake3 with GPU batch path) |
| Python | [`blake3`](https://pypi.org/project/blake3/) pip ([`luxfi/blake3-py`](https://github.com/luxfi/blake3-py) holds the Lux-owned mirror) |
| TypeScript | `@noble/hashes/blake3` (vetted upstream) |

To resync the vendored C copy:

```bash
git clone https://github.com/luxfi/blake3.git /tmp/luxfi-blake3
cp /tmp/luxfi-blake3/c/blake3.h          third_party/blake3/blake3.h
cp /tmp/luxfi-blake3/c/blake3_impl.h     third_party/blake3/blake3_impl.h
cp /tmp/luxfi-blake3/c/blake3.c          third_party/blake3/blake3.c
cp /tmp/luxfi-blake3/c/blake3_dispatch.c third_party/blake3/blake3_dispatch.c
cp /tmp/luxfi-blake3/c/blake3_portable.c third_party/blake3/blake3_portable.c
cp /tmp/luxfi-blake3/c/blake3_neon.c     third_party/blake3/blake3_neon.c
```

CMakeLists.txt compiles only portable + NEON sources — saves ~3 MB of
binary on x86 without measurable hash-rate loss for our usage pattern
(inputs are tiny: 32-byte public keys and 21-byte versioned hashes).

Upstream license: CC0 1.0 Universal / Apache 2.0.
