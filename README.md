# Test-driven reverse engineering

*spice* is a library that makes re-implementing sections of machine code in
Rust more fun.

## Illustrative example

Take a trivial example that adds 1 + 1 in assembly:

```
0000000000001670 <main>:
    1670:       d10043ff        sub     sp, sp, #0x10
    1674:       b9000fff        str     wzr, [sp, #12]
    1678:       52800040        mov     w0, #0x2
    167c:       910043ff        add     sp, sp, #0x10
    1680:       d65f03c0        ret
```

Now let's implement it in Rust (probably with the help of a decompiler, in practice):

```rust
#[Splice(begin = 0x1670, end = 0x1680)]
fn add_one_plus_one() {
  1 + 1
}
```

What we get now is the original binary augmented with our custom function!
If we're adequately motivated, we can repeat this step iteratively until our entire
program is reverse engineered in Rust.

Most likely, we only care about reversing a few specific sections.

## How it works

As you may have guessed by the name, *spice* forcibly inserts the machine code for
Rust functions into a target binary (either via direct substitution or via unconditional
jumps).