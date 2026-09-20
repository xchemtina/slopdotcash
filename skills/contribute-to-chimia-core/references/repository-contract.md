# Repository contract — chimia-core

What the package is, how to build it, and what a contribution must not break.

## Where the work is

The listed work is **`packages/chimia-core`** inside the monorepo
`chimiadao/catalyst-usecase-concept`, integration branch `main`.

The rest of the repository describes an unfunded multi-chain blockchain proposal —
Cardano/Plutus, Monad, Solana, CIP-68, tokenomics, governance, and Catalyst funding
documents. **None of it is in scope.** It is not being paid for and it is not being
reviewed here.

## Package layout

```
packages/chimia-core/
  chimia-core.cabal      # declares 10 library modules and a 4-spec test suite
  stack.yaml             # pinned resolver
  src/Chimia/Core/
    Atom.hs              # elements, Charge
    Bond.hs              # bonds, canonical ordering
    Orbitals.hs          # spherical harmonics
    MO.hs                # LCAO-MO ansatz
    ElementConfig.hs     # ground-state electron configurations
    Molecule.hs          # graph representation
    Reaction.hs          # transformations
```

Three declared modules do not exist: `Stoichiometry`, `Validation`, `Serialization`.
The declared test directory `packages/chimia-core/test/` does not exist either.
`tests/ChemistryValidation.hs` sits at the repository root, outside the package, and is
compiled by nothing.

Verify all of this yourself before relying on it. Live GitHub wins over this document.

## Toolchain

```bash
cd packages/chimia-core
stack build
stack test
```

The repository's `CONTRIBUTING.md` also mentions pnpm, Foundry and Anchor. Those belong to
the out-of-scope blockchain layers; you need **only Stack and GHC**.

State your GHC version, Stack resolver and OS in the pull request. The resolver is pinned
in `stack.yaml`; changing it is a material change that needs its own justification, because
it changes the compiler for everyone.

## Invariants a contribution must not break

1. **Do not weaken the warning flags.** The library builds with `-Wall -Wcompat
   -Widentities -Wincomplete-record-updates -Wincomplete-uni-patterns
   -Wmissing-home-modules -Wpartial-fields -Wredundant-constraints`. Do not remove one,
   and do not add a warning. If a module genuinely needs a partial pattern, argue for it
   in the pull request.
2. **Do not introduce a second representation** of an element, a charge, or a bond.
   `Atom.hs` and `Bond.hs` define the vocabulary; new modules import it. A parallel type
   is how a formally-verifiable library stops being one.
3. **Do not loosen a type to get a green build.** If `Stoichiometry` is hard to express
   against the current `Reaction`, propose the change explicitly. Quietly relaxing a
   constraint destroys the only property this package sells.
4. **Do not add a dependency without saying why.** The library currently depends on
   `base`, `containers`, `aeson`, `text`, `bytestring`, `scientific`, `vector`. Anything
   beyond that is a design decision, not a detail.
5. **Do not leave a test file that nothing compiles.** Either wire it into the declared
   suite or remove it.

## Chemistry is checkable, and being wrong is a defect

This is a chemistry library. A wrong ground-state electron configuration, a wrong orbital
shape, or a bond ordering that is not actually canonical is a real defect, not a matter of
taste — and the existing modules were written quickly. The record already notes one
correction to the f-orbitals.

If you claim a chemistry error, **cite an authoritative source** and add a test that fails
before your fix and passes after. If you are unsure whether something is wrong, say so and
show the discrepancy rather than asserting.

## Where fixes belong

Everything in scope lands in this repository, on `main`, through a pull request. There is
no upstream to forward to — `chimia-core` is original work here, inspired by Oliver
Goldstein's MolADT approach. If you draw on that work, attribute it explicitly in the pull
request.
