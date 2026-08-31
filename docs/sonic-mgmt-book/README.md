# Understanding sonic-mgmt

This is a learner-first guide to the `sonic-mgmt` repository. It complements
the existing reference documents by explaining the system in a deliberate
order.

Start with [`src/SUMMARY.md`](src/SUMMARY.md), or build the book with
[mdBook](https://rust-lang.github.io/mdBook/):

```bash
mdbook serve docs/sonic-mgmt-book --open
```

The generated site is written to `docs/sonic-mgmt-book/book/`, which is ignored
by Git.

## Documentation principles

- Teach one mental model before listing configuration options.
- Follow real data and control flow through the current code.
- Separate concepts, tutorials, how-to guides, and reference material.
- Link to detailed documents instead of duplicating them.
- Call out topology-dependent and environment-dependent behavior.

The foundational path and specialized tracks are usable now. The roadmap maps
the original expansion items to their completed chapters and records future
contribution themes.
