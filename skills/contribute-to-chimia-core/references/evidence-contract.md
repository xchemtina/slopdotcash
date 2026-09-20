# Evidence contract — chimia-core

What a contribution must carry before it can be accepted.

## 1. Show the build, do not describe it

Paste the actual commands and their output:

```bash
cd packages/chimia-core
stack build
stack test
```

"It builds now" is the claim, not the evidence. Include the **first** compiler error for
anything that failed, not the last — GHC's cascade buries the cause.

State your GHC version, Stack resolver, and OS. A build that only works on your machine is
not a build.

## 2. State the property in English before you state it in code

For every module you add, say what must be true of *every* molecule or reaction, then
write the QuickCheck property that tests it. The conservation laws are the point of this
library:

- **Atom conservation** — the multiset of atoms is invariant across a balanced reaction.
- **Mass balance** — total mass is invariant, within a stated tolerance. State the
  tolerance and justify it.
- **Charge balance** — total charge is invariant.
- **Round-trip** — `decode . encode == Just x`.

## 3. Say what your generators actually cover

A property is only as strong as its `Arbitrary` instance. An `Arbitrary Molecule` that
generates water and nothing else proves nothing about chemistry.

State what your generator covers — which elements, which charges, which sizes — and what
it deliberately does not. An honest narrow generator is acceptable; an unstated narrow
generator is misleading evidence.

## 4. Prove the test can fail

If deleting your implementation does not break your test, the test is not evidence.

Check this, and say in the pull request that you checked it. This one line separates a
property from a shape-smoke assertion, and it is the first thing review will look for.

## 5. Paste counterexamples verbatim

When QuickCheck shrinks a failure, the shrunk case is the most valuable output in the run.
Paste it exactly — including counterexamples that killed your own first attempt. A
contribution that reports "QuickCheck found this, so I changed the design" is stronger
than one that reports only success.

## 6. Cite chemistry claims

A ground-state configuration, an orbital shape, a bond order or an atomic mass is
checkable against an authoritative source. If you assert one — or claim an existing one is
wrong — cite the source and add the test that discriminates.

Do not correct chemistry silently in a refactor. A chemistry change is the whole
contribution and must be reviewable as such.

## 7. Scope honestly

Say plainly what you did **not** do. A pull request that implements `Stoichiometry` and
says "`Validation` still does not exist; this does not make `stack build` pass on its own"
is more useful than one that implies the build is fixed.

Partial work, honestly scoped, is accepted. Overstated work is not.
